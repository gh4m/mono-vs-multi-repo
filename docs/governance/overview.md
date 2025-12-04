# Governance and Reporting Overview

As codebases grow—whether in monorepos or across many repositories—organizations need systematic approaches to answer fundamental questions: What exists? Who owns it? What depends on what? Where is it deployed? Without clear governance and reporting practices, teams lose visibility, duplicate work, and struggle to assess the impact of changes.

This document provides practical patterns for governance and reporting in both monorepo and multi-repo contexts. Examples and snippets are illustrative only; adapt them to your stack, tools, and organizational constraints.

## Why Governance Matters

### The Visibility Problem

**In Monorepos:**
- Single repository doesn't automatically mean visibility
- With hundreds of projects, engineers still can't find what exists
- Need to know: Which apps are production-critical? Which libraries are experimental?
- Ownership unclear without explicit metadata

**In Multi-Repos:**
- Repositories scattered across organizations
- Hard to discover what exists and who maintains it
- Version fragmentation across dependencies
- No central view of what's deployed where

### Core Governance Questions

Every organization needs to answer:

1. **What exists?** - Catalog of all apps, services, libraries, tools
2. **Who owns it?** - Clear ownership and contact information
3. **What depends on what?** - Dependency graph and impact analysis
4. **What's the status?** - Lifecycle stage (experimental, production, deprecated)
5. **Where is it deployed?** - Which versions are in which environments
6. **How do we release it?** - Versioning and release strategies
7. **What's the impact?** - Understanding blast radius of changes

## Governance in Monorepos

### Advantages

Monorepos provide natural foundations for governance:
- All code in one place makes cataloging straightforward
- Single CI/CD system can collect metadata consistently
- Dependency graphs computable from source
- Atomic view of what changed together
- Easier to enforce policies uniformly

### Challenges

But monorepos don't solve governance automatically:
- Scale problem: Thousands of projects still need organization
- Ownership ambiguity without explicit metadata
- Need tooling to surface what's in the repository
- Release complexity: Different projects have different cadences
- Still need to track what's deployed where

### Monorepo Governance Patterns

**Project Metadata Files**

Each project maintains metadata describing itself:

```yaml
# apps/web-portal/project.yaml
name: web-portal
type: application
owner: frontend-team
lifecycle: production
tier: critical
dependencies:
  - libs/ui-components
  - libs/auth-sdk
repository-path: apps/web-portal
deployment:
  strategy: continuous
  environments:
    - staging
    - production
documentation: https://wiki.company.com/web-portal
oncall: frontend-oncall@company.com
```

**CODEOWNERS for Ownership**

```
# .github/CODEOWNERS
/apps/web-portal/              @frontend-team
/apps/admin-console/           @admin-team
/services/billing/             @billing-team @platform-team
/libs/ui-components/           @design-system-team
/libs/auth-sdk/               @security-team @platform-team
/tools/                        @platform-team
```

**Automated Catalog Generation**

Build service catalog from metadata files:

```typescript
// Tool to generate catalog from project.yaml files
import { glob } from 'glob';
import { parse } from 'yaml';

const projects = glob.sync('**/project.yaml')
  .map(path => ({
    ...parse(readFileSync(path)),
    path
  }));

// Generate catalog JSON for internal portal
writeFileSync('catalog.json', JSON.stringify(projects));
```

**Dependency Graph from Source**

Monorepo tools provide built-in dependency analysis:

```bash
# Nx dependency graph
nx graph

# Find what depends on a library
nx graph --focus=libs/auth-sdk

# Find all apps affected by library change
nx affected:apps --base=main
```

**Enforcement via Linting**

Require metadata files for all projects:

```javascript
// Custom lint rule
module.exports = {
  create(context) {
    return {
      Program(node) {
        const projectYaml = findProjectYaml(context.getFilename());
        if (!projectYaml) {
          context.report({
            node,
            message: 'Project must have project.yaml metadata file'
          });
        }
      }
    };
  }
};
```

## Governance in Multi-Repos

### Advantages

Multi-repos have natural governance boundaries:
- Clear ownership per repository
- Repository-level access controls
- Independent versioning and releases
- Explicit dependencies via package managers

### Challenges

But distributed repositories create visibility problems:
- No central catalog of what exists
- Hard to discover related repositories
- Dependency relationships hidden in package.json files
- Version sprawl across organization
- No unified view of deployments

### Multi-Repo Governance Patterns

**Service Catalog (Backstage)**

Centralized developer portal aggregating repository metadata:

```yaml
# payment-service/catalog-info.yaml
apiVersion: backstage.io/v1alpha1
kind: Component
metadata:
  name: payment-service
  description: Payment processing service
  annotations:
    github.com/project-slug: company/payment-service
    pagerduty.com/integration-key: abc123
  tags:
    - java
    - payments
    - critical
  links:
    - url: https://dashboard.company.com/payments
      title: Monitoring Dashboard
    - url: https://wiki.company.com/payments
      title: Documentation
spec:
  type: service
  lifecycle: production
  owner: payments-team
  system: billing
  dependsOn:
    - component:database-payments
    - component:auth-service
  providesApis:
    - payment-api
```

**Repository Templates with Metadata**

Standardize repository structure with required files:

```
repository-template/
  .github/
    CODEOWNERS              - Ownership definition
  catalog-info.yaml         - Service catalog metadata
  README.md                 - Documentation template
  OWNERS                    - Alternative ownership format
  .gitlab-ci.yml           - Standard CI/CD
  package.json             - With required fields
```

**Dependency Discovery Tools**

Automated dependency mapping across repositories:

```javascript
// Tool to scan all repos for dependencies
const repos = await github.listRepositories({ org: 'company' });

const dependencyGraph = {};
for (const repo of repos) {
  const packageJson = await github.getFile(repo, 'package.json');
  dependencyGraph[repo.name] = {
    dependencies: packageJson.dependencies,
    owner: await github.getOwner(repo),
    version: packageJson.version
  };
}

// Analyze for version sprawl
const libVersions = analyzeDependencyVersions(dependencyGraph, '@company/auth-sdk');
// Output: 15 repos use v1.2.0, 8 use v1.1.0, 3 use v1.0.0
```

**Organization-Wide Metadata Repository**

Central repository tracking all repositories:

```yaml
# org-metadata/repositories.yaml
repositories:
  - name: payment-service
    owner: payments-team
    lifecycle: production
    tier: critical
    primary-language: java

  - name: user-service
    owner: identity-team
    lifecycle: production
    tier: critical
    primary-language: go

  - name: analytics-library
    owner: data-team
    lifecycle: stable
    tier: standard
    primary-language: python
```

**Automated Repository Discovery**

Scan GitHub/GitLab APIs to maintain inventory:

```bash
#!/bin/bash
# Discover all repositories in organization

gh repo list company --json name,owner,createdAt,updatedAt,languages --limit 1000 \
  | jq -r '.[] | [.name, .owner.login, .languages[0].name] | @csv' \
  > repositories-inventory.csv
```

## Cross-Cutting Governance Practices

Some governance patterns work across both approaches:

### Release and Versioning

See `release-and-versioning.md` for detailed strategies:
- Semantic versioning for libraries
- Calendar versioning for applications
- Fixed vs independent versioning in monorepos
- Release trains and coordinated releases

### Deployment Tracking

See `environment-status.md` for deployment governance:
- What version is deployed where
- Deployment history and audit trails
- Promotion workflows across environments
- Rollback capabilities and history

### Dependency Management

See `dependencies-and-impact.md` for dependency governance:
- Direct and transitive dependency tracking
- Security vulnerability scanning
- License compliance checking
- Impact analysis for dependency updates

### Ownership Models

**Individual Ownership**
- Single person responsible (good for small projects)
- Clear accountability but creates bottlenecks
- Vacation coverage requires clear delegation

**Team Ownership**
- Team collectively responsible
- Better coverage but can diffuse accountability
- Requires clear team membership definition

**Shared Ownership with Reviewers**
- Multiple teams can contribute
- Designated reviewers approve changes
- Balances collaboration with quality control

**Domain Ownership**
- Ownership by business domain or capability
- Scales well organizationally
- Requires clear domain boundaries

### Lifecycle Stages

Define standard lifecycle stages for projects:

- **Experimental**: Early stage, expect breaking changes
- **Stable**: Production-ready, follows semantic versioning
- **Production**: Actively used in production systems
- **Critical**: Business-critical, requires extra scrutiny
- **Maintenance**: Bug fixes only, no new features
- **Deprecated**: Scheduled for removal, migration path provided
- **Archived**: Read-only, no longer maintained

Tag projects with lifecycle stage in metadata.

## Tooling and Automation

### Service Catalog Platforms

**Backstage (Open Source)**
- Spotify's developer portal platform
- Plugin ecosystem for integrations
- Supports both monorepo and multi-repo
- Catalog based on YAML metadata files

**Custom Internal Portals**
- Built on internal infrastructure
- Tight integration with company systems
- Custom workflows and approvals
- Often start as wikis, evolve to structured systems

**OpsLevel, Cortex, Compass**
- Commercial service catalog platforms
- Pre-built integrations with common tools
- Scorecards for measuring service quality
- Faster time-to-value than building custom

### CI/CD Integration

Collect metadata during builds:

```yaml
# GitHub Actions example
- name: Report deployment
  run: |
    curl -X POST https://catalog.company.com/api/deployments \
      -d '{
        "service": "payment-service",
        "version": "${{ github.sha }}",
        "environment": "production",
        "timestamp": "'$(date -u +%Y-%m-%dT%H:%M:%SZ)'"
      }'
```

### Dependency Scanning

Automated tools for dependency governance:

```bash
# Scan for outdated dependencies
npm outdated --json > outdated.json

# Security vulnerabilities
npm audit --json > audit.json

# License compliance
npx license-checker --json > licenses.json

# Send to governance dashboard
curl -X POST https://governance.company.com/api/scans \
  -d @scan-results.json
```

## Governance Metrics

Track governance health with metrics:

### Coverage Metrics
- **Catalog completeness**: % of projects with metadata
- **Ownership coverage**: % of code with assigned owners
- **Documentation coverage**: % of projects with README/docs

### Health Metrics
- **Dependency freshness**: Average age of dependencies
- **Security vulnerabilities**: Count and severity
- **Deprecated usage**: Projects still using deprecated libraries

### Compliance Metrics
- **License compliance**: % of dependencies with acceptable licenses
- **Required reviewers**: % of PRs reviewed by code owners
- **Deployment tracking**: % of deployments reported to catalog

## Governance Workflows

### New Project Onboarding

**Monorepo:**
1. Create project directory in appropriate location (apps/, libs/, etc.)
2. Add project.yaml metadata file
3. Update CODEOWNERS with ownership
4. Add project to documentation
5. CI automatically discovers and catalogs

**Multi-Repo:**
1. Create repository from template
2. Fill out catalog-info.yaml metadata
3. Set up GitHub/GitLab team permissions
4. Register in service catalog
5. Configure CI/CD and monitoring

### Deprecation Workflow

1. **Mark as deprecated** in metadata
2. **Update documentation** with migration path
3. **Add deprecation warnings** to code/logs
4. **Notify consumers** of deprecation timeline
5. **Track migration progress** of consumers
6. **Remove after grace period** when safe

### Ownership Changes

1. **Update CODEOWNERS** or repository ownership
2. **Update catalog metadata** with new owner
3. **Transfer oncall responsibilities** and access
4. **Knowledge transfer** documentation and runbooks
5. **Update monitoring alerts** to notify new owner

## Comparing Governance: Monorepo vs Multi-Repo

| Aspect | Monorepo | Multi-Repo |
|--------|----------|------------|
| **Catalog Discovery** | Parse metadata files in repo | Aggregate from many repos via API |
| **Ownership** | CODEOWNERS file | Repository-level permissions |
| **Dependencies** | Computed from source | Declared in package manifests |
| **Versioning** | Can be unified or independent | Always independent |
| **Deployment Tracking** | Single CI/CD system reports all | Each repo reports separately |
| **Enforcement** | Linters, build rules, required files | Templates, org policies, bots |
| **Visibility** | Single repo, need tooling to surface | Scattered, need aggregation |
| **Tooling Complexity** | Build-time analysis | API aggregation and discovery |

## Best Practices

### For Both Approaches

1. **Metadata is code**: Version control all governance metadata
2. **Automate collection**: Don't rely on manual updates
3. **Validate metadata**: Check for completeness and correctness in CI
4. **Make it visible**: Dashboards, portals, searchable catalogs
5. **Keep it current**: Automated updates from CI/CD pipelines
6. **Enforce standards**: Require metadata for new projects
7. **Measure compliance**: Track and improve governance metrics

### For Monorepos Specifically

1. **Use project metadata files** consistently (project.yaml, package.json, etc.)
2. **Leverage build tools** for dependency graph analysis
3. **CODEOWNERS is critical** for ownership clarity
4. **Enforce structure** through linting and build rules
5. **Generate documentation** from metadata automatically

### For Multi-Repos Specifically

1. **Adopt service catalog** early (Backstage or similar)
2. **Use repository templates** to standardize structure
3. **Automate discovery** of repositories and dependencies
4. **Central metadata repository** for organization-level data
5. **Bot automation** for enforcing standards across repos

## Getting Started

### Minimal Viable Governance (Monorepo)

1. Add CODEOWNERS file with team ownership
2. Create project.yaml metadata for each project
3. Document projects in README or wiki
4. Use monorepo tool's dependency graph visualization

### Minimal Viable Governance (Multi-Repo)

1. Create catalog-info.yaml in each repository
2. Set up Backstage or equivalent service catalog
3. Document ownership in each README
4. Track repositories in shared spreadsheet initially

### Evolution Path

1. **Start simple**: Basic metadata and ownership
2. **Add automation**: CI/CD collects and validates
3. **Build visualizations**: Dashboards and portals
4. **Integrate tools**: Connect to monitoring, incidents, deployments
5. **Enforce compliance**: Required metadata, automated checks
6. **Continuous improvement**: Metrics and feedback loops

## Conclusion

Governance and reporting are essential regardless of repository strategy. The specific tools and patterns differ between monorepo and multi-repo approaches, but the fundamental questions remain the same: What exists? Who owns it? What depends on what? Where is it deployed?

**Monorepos** have advantages in dependency analysis and unified tooling but need explicit metadata to surface what's in the repository.

**Multi-repos** have clear ownership boundaries but require aggregation and discovery tooling to maintain visibility across repositories.

Both approaches benefit from:
- Structured metadata describing projects
- Clear ownership assignments
- Automated collection and validation
- Visible, searchable catalogs
- Integration with CI/CD pipelines

Start with basic metadata and ownership, then progressively add automation and tooling as the organization scales.

For specific governance topics, see:
- `catalog-and-metadata.md` - Service catalogs and metadata schemas
- `dependencies-and-impact.md` - Dependency tracking and impact analysis
- `release-and-versioning.md` - Versioning strategies and release governance
- `environment-status.md` - Deployment tracking and environment status
