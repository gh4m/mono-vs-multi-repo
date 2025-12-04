# Monorepo Overview

A **monorepo** is a single version-controlled repository that contains multiple projects: applications, libraries, infrastructure code, and shared tooling. Teams work in one place, but still slice the codebase into logical modules and domains. For a contrasting model, see `../multi-repo/overview.md`.

## Core Characteristics

- One repository, many projects (for example, `apps/`, `services/`, `libs/`, `tools/`).
- Shared build, test, and CI/CD tooling.
- A single history and trunk (or mainline) that all changes flow through.
- Strong emphasis on internal reuse via shared libraries and utilities.

A simple layout might look like:

```text
apps/
  web-portal/
  admin-console/
services/
  billing/
  auth/
libs/
  ui-components/
  domain-ordering/
tools/
  ci-scripts/
  codegen/
```

## Why Teams Choose Monorepos

- **Shared tooling and standards** – one place to enforce linting, formatting, testing, and security policies.
- **Easier refactoring** – cross-cutting changes (for example, API evolution, domain refactors) can be done atomically.
- **Discoverability and reuse** – engineers can find and reuse existing code instead of cloning patterns in separate repos.
- **Consistent dependency management** – coordinated versions of core libraries and frameworks.

## Common Challenges

- **Scale and performance** – builds, tests, and tooling must handle a large codebase efficiently; without proper tooling, CI times can become prohibitive.
- **Ownership clarity** – clear boundaries, code owners, and review rules are needed to avoid "drive-by" changes across team boundaries.
- **Release complexity** – individual apps and libraries often need independent versioning and deployment, requiring sophisticated orchestration.
- **Reporting and governance** – you still need to know what exists, who owns it, and what is deployed where.
- **Tooling investment** – requires specialized build systems and dependency analysis tools that have a learning curve and maintenance overhead.
- **Repository size** – over time, the repository can grow large, potentially affecting clone times and local operations without proper VCS optimization.

See `../governance/overview.md` for governance and reporting considerations.

## Tooling and Workflows

Modern monorepos typically rely on specialized tooling:

- **Task runners and orchestrators** (for example, Nx, Turborepo, Bazel, Pants) to manage builds and tests efficiently.
- **Dependency graph analysis** to run only affected builds and tests, avoiding full repository rebuilds on every change.
- **Caching systems** (local and remote) to speed up repetitive build and test operations.
- **Code owners and review policies** (for example, GitHub CODEOWNERS, GitLab Code Owners) to enforce boundaries and approval workflows.
- **Per-project metadata files** feeding a service catalog or governance system to track what exists and who owns it.
- **Workspace or package management** tools (for example, npm workspaces, Yarn workspaces, pnpm workspaces) to handle internal dependencies.

These tools make it practical to treat the monorepo as a cohesive whole while still supporting independent lifecycles for individual apps and libraries.

## Example Monorepo Layouts

### Full-Stack Application

```text
apps/
  web-portal/
    src/
    tests/
    package.json
  admin-console/
    src/
    tests/
    package.json
services/
  billing/
    src/
    tests/
    Dockerfile
  auth/
    src/
    tests/
    Dockerfile
libs/
  ui-components/
    src/
    stories/
    package.json
  domain-ordering/
    src/
    tests/
    package.json
tools/
  ci-scripts/
  codegen/
```

All projects share the same root-level tooling configuration (ESLint, Prettier, TypeScript), and changes can be coordinated atomically.

### Platform Engineering

```text
infrastructure/
  terraform/
  kubernetes/
services/
  api-gateway/
  user-service/
  payment-service/
libraries/
  sdk-go/
  sdk-python/
  sdk-javascript/
tooling/
  cli-tools/
  monitoring-config/
```

Infrastructure, services, and SDKs evolve together, ensuring compatibility across the stack.

### Enterprise Monorepo

```text
products/
  mobile-app/
  web-app/
  desktop-app/
shared/
  design-system/
  analytics-sdk/
  core-utils/
platform/
  build-tools/
  testing-frameworks/
  deployment-scripts/
docs/
  architecture/
  runbooks/
```

Multiple product teams share common platform code and documentation in a single repository.

