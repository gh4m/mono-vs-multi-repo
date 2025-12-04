# CI/CD and Build Strategies

How you structure your repositories has a direct impact on CI/CD pipeline design, build performance, and deployment workflows. Monorepos and multi-repos require different approaches to achieve fast, reliable builds at scale.

## Pipeline Design Patterns for Monorepos

Monorepo CI/CD pipelines must handle multiple projects in a single repository while avoiding unnecessary work.

### Affected Project Detection

The core challenge is determining what changed and what needs to be rebuilt or retested:

```yaml
# Example: GitHub Actions with affected detection
- name: Detect affected projects
  run: npx nx affected:build --base=origin/main --head=HEAD

- name: Test affected projects
  run: npx nx affected:test --base=origin/main --head=HEAD
```

Most monorepo tools (Nx, Turborepo, Bazel) provide dependency graph analysis to compute affected projects based on git diffs.

### Matrix or Parallel Job Strategies

Once affected projects are identified, pipelines typically run jobs in parallel:

```yaml
# Example: Dynamic matrix based on affected projects
jobs:
  detect:
    runs-on: ubuntu-latest
    outputs:
      projects: ${{ steps.affected.outputs.projects }}
    steps:
      - id: affected
        run: echo "projects=$(nx affected:projects --json)" >> $GITHUB_OUTPUT

  build:
    needs: detect
    strategy:
      matrix:
        project: ${{ fromJson(needs.detect.outputs.projects) }}
    runs-on: ubuntu-latest
    steps:
      - run: nx build ${{ matrix.project }}
```

### Single Pipeline, Multiple Stages

Another pattern uses a single pipeline with conditional stages:

```yaml
stages:
  - detect-changes
  - build
  - test
  - deploy

build-web-app:
  stage: build
  rules:
    - changes:
        - apps/web-app/**/*
        - libs/shared-ui/**/*
  script:
    - npm run build:web-app
```

### Deployment Orchestration

Monorepos often need to deploy multiple services or apps from a single commit:

- Deploy all affected services in dependency order
- Use feature flags to decouple deployment from release
- Tag specific projects for independent deployments (for example, `web-app-v1.2.3`, `api-v2.0.1`)

## Pipeline Design Patterns for Multi-Repos

Multi-repo CI/CD pipelines are simpler per repository but require coordination across repositories.

### Independent Pipelines

Each repository has its own straightforward pipeline:

```yaml
# Repository: payment-service
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm install
      - run: npm test
      - run: npm run build
      - run: docker build -t payment-service:${{ github.sha }} .
      - run: kubectl apply -f k8s/
```

Benefits:
- Simple, easy to understand
- Each team controls their own pipeline
- No cross-project complexity

### Cross-Repo Coordination

Challenges arise when changes span multiple repositories:

**Approach 1: Manual coordination**
- Update library, publish new version
- Update consumers manually via PRs
- Deploy after all updates are merged

**Approach 2: Automated dependency updates**
```yaml
# Dependabot or Renovate configuration
- package-ecosystem: npm
  directory: "/"
  schedule:
    interval: daily
  open-pull-requests-limit: 10
```

**Approach 3: Repository dispatch triggers**
```yaml
# Trigger downstream builds when a library is published
- name: Trigger consumer builds
  uses: peter-evans/repository-dispatch@v2
  with:
    token: ${{ secrets.REPO_ACCESS_TOKEN }}
    repository: org/web-app
    event-type: dependency-updated
```

### Integration Testing Across Repos

Multi-repo setups need separate integration test infrastructure:

- End-to-end test repositories that pull in multiple services
- Contract testing (for example, Pact) to verify API compatibility
- Staging environments that deploy from multiple repositories

## Caching, Artifact Reuse, and Incremental Builds

Effective caching is critical for both approaches but implemented differently.

### Monorepo Caching

Monorepo tools provide built-in caching based on content hashes:

**Local caching:**
```json
// nx.json or turbo.json
{
  "tasksRunnerOptions": {
    "default": {
      "options": {
        "cacheableOperations": ["build", "test", "lint"]
      }
    }
  }
}
```

**Remote caching:**
```bash
# Nx Cloud, Turborepo Remote Cache, BuildBuddy for Bazel
nx build my-app --configuration=production
# Pulls cached build from remote if available
```

Benefits:
- Never rebuild unchanged projects
- Cache hits across team members and CI
- Dramatic speedups (10x+ improvements common)

### Multi-Repo Caching

Multi-repo caching relies on CI platform features:

**Docker layer caching:**
```dockerfile
FROM node:18 AS deps
COPY package*.json ./
RUN npm ci
# This layer is cached if package.json hasn't changed
```

**CI artifact caching:**
```yaml
# GitHub Actions
- uses: actions/cache@v3
  with:
    path: ~/.npm
    key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
```

**Published artifacts:**
- Build once, publish to registry (npm, Docker Hub, Artifactory)
- Consumers download pre-built artifacts instead of rebuilding from source

## Common Pitfalls and Scaling Strategies

### Monorepo Pitfalls

**Problem: Full repository testing on every PR**
- Solution: Use affected project detection rigorously
- Solution: Implement remote caching to share work across CI runs

**Problem: CI queue saturation**
- Solution: Use matrix jobs to parallelize builds
- Solution: Invest in faster CI runners or self-hosted infrastructure

**Problem: Flaky tests affecting unrelated changes**
- Solution: Isolate test failures to affected projects only
- Solution: Implement test quarantine for known flaky tests

**Problem: Long-running pipelines blocking merges**
- Solution: Split required vs optional checks
- Solution: Use merge queues to batch changes

### Multi-Repo Pitfalls

**Problem: Dependency version sprawl**
- Solution: Use automated dependency update tools (Dependabot, Renovate)
- Solution: Establish organization-wide dependency policies

**Problem: Breaking changes cascading across repos**
- Solution: Use semantic versioning strictly
- Solution: Implement deprecation periods and changelog discipline
- Solution: Contract testing to catch breakages early

**Problem: Inconsistent CI/CD configuration**
- Solution: Use reusable workflows or shared pipeline templates
- Solution: Centralize CI configuration in a template repository

**Problem: Discovery and visibility across repos**
- Solution: Service catalog (for example, Backstage) to track all repositories
- Solution: Standardized metadata files (for example, `catalog-info.yaml`)

## Scaling Strategies

### For Monorepos
1. Invest in powerful build orchestration tools (Nx, Turborepo, Bazel)
2. Enable remote caching from day one
3. Use distributed task execution when repository grows very large
4. Monitor and optimize cache hit rates
5. Consider partial checkouts for very large repositories (Git sparse-checkout)

### For Multi-Repos
1. Standardize repository structure with templates
2. Automate cross-repo updates and dependency management
3. Build a service catalog for discoverability
4. Implement contract testing between services
5. Use artifact registries effectively to avoid rebuilding dependencies

## Key Takeaways

**Monorepos:**
- Require sophisticated tooling but enable atomic changes
- Benefit heavily from caching and affected project detection
- Single pipeline handles multiple projects with conditional logic

**Multi-repos:**
- Simpler per-repository pipelines
- Coordination across repositories requires planning and tooling
- Each repository deploys independently with clear versioning

Both approaches can scale effectively with the right investment in tooling and processes.

