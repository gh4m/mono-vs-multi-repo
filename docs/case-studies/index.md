# Case Studies

Real-world examples demonstrate how organizations of different sizes and domains choose and operate monorepo, multi-repo, or hybrid strategies. These case studies illustrate the trade-offs, tooling decisions, and lessons learned from production use. Use them as patterns to compare against your own context rather than prescriptions.

## Monorepo Case Studies

### Google: The Original Large-Scale Monorepo

**Context:**
- One of the largest monorepos in the world (billions of lines of code)
- Tens of thousands of engineers working in a single repository
- Decades of evolution and tooling investment

**Strategy and Tooling:**
- Custom version control system (Piper) built on Paxos-based replication
- Bazel build system for incremental, hermetic builds
- Code search and analysis tools built specifically for the monorepo
- Extensive automation for testing, code review, and refactoring

**Benefits:**
- Atomic cross-cutting changes across the entire codebase
- Easy code reuse and discoverability
- Consistent tooling and standards enforcement
- Large-scale refactoring is practical (for example, language migrations)
- Single source of truth for all code

**Challenges:**
- Requires massive investment in custom tooling
- Onboarding complexity for new engineers
- Clone and sync times require specialized infrastructure
- Not all tooling is open source or easily replicable

**Lessons Learned:**
- Monorepo at scale requires serious tooling investment
- Code ownership and review boundaries are essential
- Automation is critical for maintaining consistency
- Works well for organizations with strong engineering culture

**References:**
- [Why Google Stores Billions of Lines of Code in a Single Repository](https://dl.acm.org/doi/10.1145/2854146)

---

### Meta (Facebook): React, Jest, and Yarn Workspaces

**Context:**
- Large open-source JavaScript ecosystem including React, Jest, Relay
- Internal monorepo for facebook.com web and mobile apps
- Thousands of engineers and millions of lines of JavaScript/TypeScript

**Strategy and Tooling:**
- Yarn workspaces and custom build tooling for JavaScript monorepos
- Buck build system (later replaced by improvements to other tools)
- Extensive use of code generation and type checking (Flow, later TypeScript)
- Mercurial for version control (internal)

**Benefits:**
- Coordinated releases across React ecosystem packages
- Shared infrastructure for testing and linting
- Fast iteration on interconnected projects
- Internal dogfooding of open-source tools

**Challenges:**
- Complex dependency graphs require careful management
- Build time optimization is ongoing effort
- Balancing internal and external (open source) workflows

**Lessons Learned:**
- Monorepos work well for tightly coupled JavaScript projects
- Tooling investment pays dividends at scale
- Code splitting and lazy loading essential for web performance
- Open-source projects can thrive in monorepo structure

---

### Nx and Turborepo Adopters: Modern JavaScript Monorepos

**Context:**
- Thousands of companies using modern JavaScript monorepo tools
- Typical scale: 10-200 projects in a single repository
- Full-stack applications with shared component libraries

**Strategy and Tooling:**
- Nx or Turborepo for build orchestration and caching
- npm/yarn/pnpm workspaces for dependency management
- GitHub Actions or similar for CI/CD
- Remote caching (Nx Cloud, Vercel Remote Cache) for speed

**Benefits:**
- 10x+ build speedups with caching and affected detection
- Easy code sharing between frontend and backend
- Consistent tooling configuration across projects
- Atomic commits for feature development

**Challenges:**
- Learning curve for team members unfamiliar with monorepo tooling
- Initial setup and configuration effort
- Managing CI/CD pipelines for multiple deployment targets

**Lessons Learned:**
- Start simple with workspaces, add orchestration tools as needed
- Remote caching is essential for CI/CD performance
- Clear project boundaries and ownership prevent conflicts
- Documentation and onboarding materials are critical

---

### Internal Enterprise Monorepo: Line-of-Business Applications

**Context:**
- Mid-size enterprise with 10–50 line-of-business apps
- Mix of frontend, backend, and shared domain libraries
- Central platform team plus multiple feature teams

**Strategy and Tooling:**
- Monorepo with `apps/`, `services/`, and `libs/` structure
- Nx, Turborepo, or similar for task orchestration
- Shared CI/CD templates for builds, tests, and deployments
- Per-project metadata feeding a service catalog (see `../governance/catalog-and-metadata.md`)

**Benefits:**
- Easier to enforce standards (security, logging, observability)
- Shared domain and UI libraries reduce duplication
- Cross-cutting changes (for example, auth flows) are easier to implement
- Central visibility into what exists and who owns it

**Challenges:**
- Requires clear ownership and review rules to avoid “drive-by edits”
- Build and test performance must be engineered (affected detection, caching)
- Some teams may feel constrained by shared tooling

**Lessons Learned:**
- Start with a well-documented structure and ownership model
- Invest early in CI performance and developer tooling
- Use project metadata and catalogs to reduce administrative overhead

---

### Babel and Jest: Open Source JavaScript Tool Monorepos

**Context:**
- Popular open-source JavaScript tools with many packages
- Babel: 140+ packages, Jest: 40+ packages
- Maintained by small core teams with community contributions

**Strategy and Tooling:**
- Lerna for package management and publishing
- Fixed versioning (all packages share same version)
- GitHub Actions for CI/CD
- Extensive test suites ensuring package compatibility

**Benefits:**
- Coordinated releases across ecosystem
- Easy refactoring across packages
- Consistent testing and development experience
- Clear dependency relationships

**Challenges:**
- Version bumps affect all packages (even unchanged ones)
- Publishing workflow complexity
- Breaking changes require careful coordination

**Lessons Learned:**
- Fixed versioning simplifies release process for tightly coupled packages
- Automated testing across all packages catches integration issues
- Good for projects with strong conceptual cohesion

---

## Multi-Repo Case Studies

### Amazon: Service-Oriented Architecture at Scale

**Context:**
- Thousands of microservices across hundreds of teams
- Each team owns multiple services
- Strong emphasis on service autonomy and ownership

**Strategy and Tooling:**
- One repository per service (multi-repo approach)
- Internal package repositories for shared libraries
- Standardized deployment pipelines and infrastructure
- Service mesh for runtime coordination

**Benefits:**
- Clear ownership boundaries per service
- Teams can choose appropriate technology stacks
- Independent deployment and scaling
- Organizational autonomy and speed

**Challenges:**
- Dependency management across thousands of services
- Ensuring consistency in security and compliance
- Service discovery and documentation
- Cross-service refactoring requires coordination

**Lessons Learned:**
- Multi-repo scales organizationally by distributing ownership
- Strong service contracts (APIs) are essential
- Standardization through tooling and templates, not enforcement
- Works well for service-oriented architectures

---

### Netflix: Microservices and Multi-Repo

**Context:**
- Hundreds of microservices supporting streaming platform
- Engineering teams organized around business domains
- Cloud-native architecture on AWS

**Strategy and Tooling:**
- Separate repositories for each service
- Shared libraries published to internal Maven/npm registries
- Spinnaker for continuous delivery
- Extensive automation and internal platform tooling

**Benefits:**
- Teams move independently without coordination overhead
- Technology diversity (Java, Node.js, Python, Go)
- Clear blast radius for changes and incidents
- Organizational scaling through autonomy

**Challenges:**
- Cross-cutting changes require multi-repo coordination
- Dependency version sprawl
- Maintaining visibility across hundreds of repos

**Lessons Learned:**
- Invest in developer platform and tooling
- Automated dependency management is critical
- Service catalogs and documentation prevent duplication
- Cultural emphasis on ownership and responsibility

---

### Kubernetes: Open Source Multi-Repo Ecosystem

**Context:**
- Large open-source project with multiple organizations contributing
- Core Kubernetes plus ecosystem of related projects
- Distributed governance and contribution model

**Strategy and Tooling:**
- Separate repositories under kubernetes and kubernetes-sigs organizations
- Consistent CI/CD across repos (Prow, GitHub Actions)
- Go modules for dependency management
- Release process coordinated across multiple repos

**Benefits:**
- Clear boundaries between core and ecosystem projects
- Different repos can have different governance models
- Easier for external contributors to focus on specific areas
- Independent release cycles for ecosystem projects

**Challenges:**
- Coordinating releases across multiple repositories
- API compatibility across versions
- Documentation scattered across repos

**Lessons Learned:**
- Multi-repo works well for loosely coupled projects
- Strong API contracts enable independent evolution
- Governance and communication are critical for distributed teams
- Tooling consistency across repos improves contributor experience

---

### HashiCorp: Multi-Product Open Source Company

**Context:**
- Multiple infrastructure tools (Terraform, Vault, Consul, Nomad, etc.)
- Each product has independent release cycle and user base
- Open source with commercial offerings

**Strategy and Tooling:**
- Separate repository per product
- Shared Go libraries for common functionality
- Consistent build and test infrastructure
- Public GitHub repositories with issue tracking per product

**Benefits:**
- Products can evolve independently
- Clear product boundaries for users and contributors
- Different products can have different license models
- Per-product community and ecosystem

**Challenges:**
- Code duplication across products
- Ensuring shared libraries stay compatible
- Cross-product features require coordination

**Lessons Learned:**
- Multi-repo appropriate when products have distinct user bases
- Shared libraries need clear ownership and versioning
- Documentation per product simplifies user experience
- Works well for product-focused organizations

---

## Hybrid Approaches

### Microsoft: Mixed Strategy for Different Needs

**Context:**
- Massive organization with diverse products and teams
- Windows, Office, Azure, Visual Studio Code, and more
- Tens of thousands of engineers worldwide

**Strategy and Tooling:**
- Monorepo for some products (Windows uses single massive repo)
- Multi-repo for others (VS Code, TypeScript, Azure SDKs)
- Hybrid approach based on product needs
- Git Virtual File System (GVFS) for large repositories

**Benefits:**
- Flexibility to choose strategy per product line
- Windows monorepo enables coordinated OS development
- VS Code multi-repo ecosystem allows community contribution
- Right tool for each context

**Challenges:**
- Different tooling and processes across teams
- Knowledge sharing across different approaches
- Consistency in governance across strategies

**Lessons Learned:**
- One size doesn't fit all in large organizations
- Product characteristics should drive repository strategy
- Tooling investment required for either approach
- Clear decision criteria help teams choose appropriately

---

### Shopify: Modular Monolith to Monorepo

**Context:**
- Large Rails monolith serving e-commerce platform
- Thousands of engineers working on shared codebase
- Transition to more modular architecture

**Strategy and Tooling:**
- Started with traditional monolithic Rails app
- Moved to modular monolith with enforced boundaries (using Packwerk)
- Monorepo with clear component ownership
- Strong emphasis on internal APIs and boundaries

**Benefits:**
- Maintained monorepo benefits (atomic changes, shared tooling)
- Added modular architecture benefits (clear boundaries, ownership)
- Gradual migration without big-bang rewrite
- Tooling enforces architectural decisions

**Challenges:**
- Cultural change in how teams work
- Enforcing boundaries in existing codebase
- Balancing autonomy with shared infrastructure

**Lessons Learned:**
- Can get modularity benefits within a monorepo
- Tooling can enforce architectural boundaries
- Migration can be gradual and pragmatic
- Architecture and repository strategy are related but separate concerns

---

## Decision Framework

Based on these case studies, consider the following when choosing a strategy:

**Choose Monorepo when:**
- Projects are tightly coupled and frequently change together
- You value atomic cross-cutting changes
- Team is small to medium (or willing to invest heavily in tooling)
- Code reuse and discoverability are high priorities
- You want consistent tooling and standards

**Choose Multi-Repo when:**
- Projects have independent lifecycles and ownership
- Teams need autonomy over technology choices
- Organization is large with distributed ownership
- Products have distinct user bases and release cycles
- Clear service boundaries exist

**Consider Hybrid when:**
- Organization is large with diverse product needs
- Some projects benefit from tight coupling, others from independence
- Different teams have different maturity levels
- Migration between strategies is in progress

The right choice depends on your organization's size, structure, culture, and technical requirements. These case studies show that both approaches can succeed at scale with appropriate tooling and processes.
