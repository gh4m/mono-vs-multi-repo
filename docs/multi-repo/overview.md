# Multi-Repo Overview

A **multi-repo** (or polyrepo) setup is an approach where each project, service, library, or application lives in its own separate version-controlled repository. Teams work in distinct repositories with independent tooling, release cycles, and ownership. For a contrasting model, see `../monorepo/overview.md`.

## Core Characteristics

- One repository per project, service, or library (for example, `payment-service`, `web-app`, `auth-library`).
- Independent build, test, and CI/CD pipelines per repository.
- Separate version histories and release cadences.
- Shared code is typically distributed as versioned packages through artifact registries (npm, Maven, PyPI, etc.).

A typical multi-repo organization might look like:

```text
github.com/org/
  web-portal/
  admin-console/
  billing-service/
  auth-service/
  ui-components-lib/
  domain-ordering-lib/
  ci-scripts/
  codegen-tools/
```

Each repository has its own `README.md`, dependencies, test suite, and deployment pipeline.

## Why Teams Choose Multi-Repos

- **Clear ownership boundaries** – each repository has a dedicated team or owner with full control over their tooling and workflow.
- **Independent scaling** – large teams can work in parallel without stepping on each other's changes or dealing with monorepo-scale tooling.
- **Isolation and stability** – breaking changes in one repository don't immediately affect others; consumers upgrade dependencies on their own schedule.
- **Granular access control** – easier to restrict who can view or modify specific services or libraries.
- **Simpler CI/CD** – each repository has a straightforward build-test-deploy pipeline without complex dependency graph analysis.

## Common Challenges

- **Dependency fragmentation** – teams may end up using different versions of shared libraries, leading to compatibility issues.
- **Cross-repo refactoring** – coordinating changes across multiple repositories requires careful planning, versioning, and migration strategies.
- **Code discoverability** – engineers may duplicate functionality because they don't know a solution already exists in another repository.
- **Tooling inconsistency** – without centralized standards, repositories can drift in their build tools, linters, and testing frameworks.
- **Coordination overhead** – breaking changes require versioning, deprecation periods, and coordinated upgrades across consumer repositories.

See `../governance/overview.md` for governance considerations in multi-repo setups.

## Tooling and Workflows

Multi-repo setups typically rely on:

- **Package managers and artifact registries** (for example, npm, Maven Central, PyPI, Docker registries) to distribute shared libraries and services.
- **Dependency management tools** (for example, Dependabot, Renovate) to keep dependencies up to date across repositories.
- **Git submodules or meta-repositories** (for example, Google's repo tool) for optional cross-repo coordination.
- **Service catalogs or discovery tools** (for example, Backstage) to maintain visibility into what repositories exist and who owns them.
- **Standardized templates and scaffolding** to enforce consistency when creating new repositories.

These tools help maintain cohesion and discoverability while preserving the independence that makes multi-repos attractive.

## Example Multi-Repo Layouts

### Microservices Architecture

```text
org/payment-service/
  src/
  tests/
  Dockerfile
  package.json

org/user-service/
  src/
  tests/
  Dockerfile
  package.json

org/notification-service/
  src/
  tests/
  Dockerfile
  package.json
```

Each service is independently deployable and versioned.

### Frontend and Backend Split

```text
org/web-app/
  src/
  public/
  package.json

org/mobile-app/
  ios/
  android/
  package.json

org/api-backend/
  src/
  tests/
  requirements.txt
```

Frontend and backend teams work independently with clear API contracts.

### Shared Library Ecosystem

```text
org/core-utils/
  src/
  tests/
  package.json

org/auth-sdk/
  src/
  tests/
  package.json

org/ui-components/
  src/
  stories/
  package.json
```

Libraries are published as versioned packages for consumption by application repositories.
