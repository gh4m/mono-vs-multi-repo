# Tooling Ecosystem

This section surveys tools and practices that make monorepo and multi-repo strategies practical at scale. The goal is not to endorse a single stack, but to highlight common building blocks and trade-offs.

## Monorepo-Focused Tools

- **Nx / Turborepo** – JS/TS-oriented build systems that understand dependency graphs, run only affected tasks, and provide generators, executors, and caching.
- **Bazel / Pants** – language-agnostic build systems with fine-grained targets, remote caching, and strong correctness guarantees.
- **Custom task runners** – homegrown scripts or Makefiles that orchestrate builds/tests across `apps/` and `libs/`, often a starting point before adopting heavier tooling.

Key capabilities:

- Incremental builds and tests based on dependency graphs.
- Shared configuration for linting, formatting, and testing.
- Hooks into CI/CD systems to avoid rebuilding everything on every change.

## Multi-Repo Practices and Tools

- **Git submodules / subtree / meta-repos** – link related repositories when some coordination is required.
- **Service catalogs** (for example, Backstage) – centralize knowledge about which repos exist, who owns them, and how to interact with them.
- **Template/scaffolding tools** (for example, Cookiecutter, `nx create-nx-workspace`) – ensure new repos start from a consistent baseline.

These tools help maintain cohesion and discoverability without forcing all code into one repository.

## Package Managers and Artifact Registries

Both approaches rely on packaging to share code:

- Package managers (for example, npm, Yarn, pnpm, Maven, Gradle, pip) to define dependencies.
- Registries (for example, npm Registry, Maven Central, PyPI, Docker registries) to publish and consume versioned artifacts.

In monorepos, versioning may be centralized (single version) or per-package (independent versions). Multi-repos almost always use per-repo or per-package versioning.

## Build, Cache, and Orchestration

- **CI systems** (for example, GitHub Actions, GitLab CI, CircleCI) orchestrate builds, tests, and deployments across projects.
- **Caching layers** (for example, remote caches, Docker layer caching) keep feedback loops fast.
- **Static analysis and quality gates** (for example, ESLint, Prettier, SonarQube, security scanners) integrate into pipelines to enforce standards.

For governance and reporting details built on top of these tools, see `../governance/overview.md`.

