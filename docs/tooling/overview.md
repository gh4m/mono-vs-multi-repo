# Tooling Ecosystem

This section surveys tools and practices that make monorepo and multi-repo strategies practical at scale. The goal is not to endorse a single stack, but to highlight common building blocks and trade-offs.

For CI/CD-specific patterns, see `../ci-cd/overview.md`.

## Monorepo-Focused Tools

Monorepos require specialized tooling to manage complexity and maintain performance at scale.

### Build Orchestration and Task Runners

**Nx**
- JavaScript/TypeScript-focused with strong Angular and React support
- Sophisticated dependency graph analysis and affected project detection
- Built-in generators for scaffolding new projects
- Integrated remote caching via Nx Cloud
- Plugin ecosystem for various frameworks and tools

Example usage:
```bash
# Run tests only for affected projects
nx affected:test --base=main

# Build all apps in parallel
nx run-many --target=build --projects=app1,app2

# Visualize dependency graph
nx graph
```

**Turborepo**
- Simpler, more focused build system for JavaScript/TypeScript monorepos
- Fast incremental builds with content-aware hashing
- Remote caching with minimal configuration
- Works well with existing package.json scripts
- Lower learning curve than Nx

Example `turbo.json`:
```json
{
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**", ".next/**"]
    },
    "test": {
      "dependsOn": ["build"],
      "outputs": []
    }
  }
}
```

**Bazel**
- Language-agnostic build system from Google
- Hermetic builds with reproducible outputs
- Fine-grained build targets and dependencies
- Excellent for very large monorepos (10,000+ projects)
- Steeper learning curve but powerful at scale

**Pants**
- Language-agnostic alternative to Bazel
- Strong Python support, also handles Go, Java, Shell, Docker
- More user-friendly than Bazel while maintaining power
- Good for polyglot monorepos

**Custom task runners**
- Homegrown scripts, Makefiles, or npm/yarn/pnpm workspace scripts
- Often a starting point before adopting specialized tooling
- Simple but limited scalability

### Workspace Package Managers

These handle dependencies within a monorepo:

- **npm workspaces** – built into npm 7+, basic monorepo support
- **Yarn workspaces** – more mature, supports hoisting and linking
- **pnpm workspaces** – efficient disk usage via content-addressed storage

Key capabilities across monorepo tools:

- Incremental builds and tests based on dependency graphs
- Shared configuration for linting, formatting, and testing
- Hooks into CI/CD systems to avoid rebuilding everything on every change
- Remote caching to share build artifacts across team and CI
- Code generation and scaffolding for consistency

## Multi-Repo Practices and Tools

Multi-repo setups rely on different categories of tooling to maintain consistency and coordination across repositories.

### Repository Coordination

**Git submodules**
- Link specific commits from other repositories
- Useful for vendoring dependencies or shared configuration
- Requires explicit updates to track upstream changes
- Can be complex to manage at scale

**Git subtree**
- Merge another repository into a subdirectory
- Simpler workflow than submodules
- History becomes part of parent repository

**Meta-repositories (repo tool)**
- Google's repo tool for managing multiple Git repositories
- Manifest file describes all repositories and their relationships
- Used by Android Open Source Project (AOSP)
- Supports synchronized operations across many repositories

### Service Catalogs and Discovery

**Backstage**
- Open-source developer portal from Spotify
- Centralizes knowledge about services, libraries, and documentation
- Plugin ecosystem for integrations (CI/CD, monitoring, cloud providers)
- YAML-based metadata files (`catalog-info.yaml`) in each repository
- Provides searchable software catalog with ownership and dependencies

Example `catalog-info.yaml`:
```yaml
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: payment-service
  description: Payment processing service
  tags:
    - java
    - payments
spec:
  type: service
  lifecycle: production
  owner: payments-team
  system: billing
```

**Custom service registries**
- Homegrown catalogs or wikis tracking repositories
- Often outgrown as organization scales

### Repository Templates and Scaffolding

**Cookiecutter**
- Python-based project templating tool
- Language-agnostic template definitions
- Creates new repositories from standardized templates

**GitHub/GitLab repository templates**
- Built-in template repository feature
- One-click creation of new repositories with standard structure
- Includes files, directories, and initial configuration

**Yeoman, Plop, Hygen**
- JavaScript code generators
- Interactive prompts for customization
- Useful for creating consistent repository structure

### Dependency Management Automation

**Dependabot**
- GitHub-native automated dependency updates
- Creates PRs for outdated dependencies
- Security vulnerability scanning
- Configurable update schedules and grouping

**Renovate**
- More flexible alternative to Dependabot
- Supports more platforms (GitHub, GitLab, Bitbucket, Azure DevOps)
- Advanced configuration options (grouping, scheduling, auto-merge)
- Better monorepo support

Example `.renovaterc.json`:
```json
{
  "extends": ["config:base"],
  "packageRules": [
    {
      "matchUpdateTypes": ["minor", "patch"],
      "automerge": true
    }
  ],
  "schedule": ["after 10pm every weekday"]
}
```

**WhiteSource/Mend, Snyk**
- Security-focused dependency management
- Vulnerability scanning and remediation
- License compliance checking

These tools help maintain cohesion and discoverability without forcing all code into one repository.

## Package Managers and Artifact Registries

Both monorepo and multi-repo strategies rely on packaging to share code, though usage patterns differ.

### Package Managers

**JavaScript/TypeScript**
- **npm** – default Node.js package manager, supports workspaces (monorepo)
- **Yarn** – faster alternative with workspace support, yarn berry (v2+) with PnP mode
- **pnpm** – efficient storage with content-addressable system, excellent monorepo support

**Java/JVM**
- **Maven** – declarative XML-based builds, Central Repository
- **Gradle** – flexible Groovy/Kotlin DSL, better for monorepos with composite builds

**Python**
- **pip** – standard package installer
- **Poetry** – modern dependency management with lockfiles
- **uv** – fast Python package installer

**Go**
- **go modules** – built-in dependency management
- Uses semantic versioning and minimal version selection

**Rust**
- **Cargo** – integrated build system and package manager
- Excellent monorepo support via workspaces

### Artifact Registries

**Public registries:**
- npm Registry (JavaScript/TypeScript)
- Maven Central (Java)
- PyPI (Python)
- crates.io (Rust)
- Docker Hub (containers)
- RubyGems (Ruby)

**Private/hosted registries:**
- **Artifactory** – universal artifact repository supporting multiple package formats
- **Nexus Repository** – popular for Java/Maven, also supports npm, Docker, etc.
- **GitHub Packages** – integrated with GitHub, supports npm, Maven, Docker, NuGet
- **GitLab Package Registry** – similar to GitHub Packages
- **AWS CodeArtifact** – managed artifact repository service
- **Azure Artifacts** – part of Azure DevOps

### Versioning Strategies

**Monorepos:**
- Fixed versioning – single version for all packages (for example, Babel, Jest)
- Independent versioning – each package has its own version (for example, Google's Go monorepo)
- Tools like Lerna, Changesets handle versioning and publishing

**Multi-repos:**
- Per-repository versioning using semantic versioning (semver)
- Clear boundaries for breaking changes
- Consumers control upgrade timing

## Code Quality and Static Analysis

Both approaches benefit from consistent tooling for code quality.

### Linting and Formatting

**JavaScript/TypeScript:**
- **ESLint** – pluggable linting with extensive rule sets
- **Prettier** – opinionated code formatter
- **Biome** – fast alternative to ESLint + Prettier

**Python:**
- **Ruff** – extremely fast linter and formatter
- **Black** – opinionated code formatter
- **Pylint, Flake8** – traditional linters

**Go:**
- **golangci-lint** – fast linter runner aggregating multiple linters
- **gofmt** – standard code formatter

**Java:**
- **Checkstyle** – configurable style checker
- **SpotBugs** – bug detection
- **Google Java Format** – code formatter

### Security Scanning

- **Snyk** – vulnerability scanning for dependencies and code
- **Trivy** – container and dependency scanning
- **SonarQube** – comprehensive code quality and security platform
- **GitHub Advanced Security** – CodeQL scanning, secret detection
- **GitGuardian** – secret detection

### Type Checking and Static Analysis

- **TypeScript** – static typing for JavaScript
- **mypy** – static type checker for Python
- **Flow** – alternative to TypeScript for JavaScript
- **SonarQube** – multi-language static analysis

## Version Control and Code Review

**Git hosting platforms:**
- **GitHub** – dominant platform with excellent Actions CI/CD
- **GitLab** – integrated DevOps platform with built-in CI/CD
- **Bitbucket** – Atlassian ecosystem integration
- **Azure DevOps** – Microsoft enterprise DevOps suite

**Code review tools:**
- Built-in PR/MR review on hosting platforms
- **Gerrit** – code review system used by Android, Chromium
- **Reviewable** – enhanced GitHub code review
- **Graphite** – stacked PRs for faster iteration

**Code ownership:**
- **CODEOWNERS** files (GitHub/GitLab) – define ownership and required reviewers
- Critical for monorepos to maintain boundaries

## Choosing Tools for Your Context

### For Monorepos
- Start with workspace support in your package manager (npm/yarn/pnpm workspaces)
- Add task runner when coordination becomes painful (Nx or Turborepo for JS/TS, Bazel/Pants for polyglot)
- Enable remote caching early to share build artifacts
- Use CODEOWNERS to enforce boundaries

### For Multi-Repos
- Invest in service catalog (Backstage) for discoverability
- Automate dependency updates (Renovate or Dependabot)
- Standardize repository structure with templates
- Consider contract testing for service interactions

For governance and reporting details built on top of these tools, see `../governance/overview.md`.

