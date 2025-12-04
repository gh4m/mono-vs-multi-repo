# Moving from Multi-Repo to Monorepo: When It Worked

This page collects examples of organizations that moved from many repositories to a monorepo and saw clear benefits. While moving away from monorepos gets attention (see `moved-away-from-mono.md`), many organizations successfully make the opposite transition and maintain monorepos long-term.

## Case Study 1: Airbnb Frontend Consolidation

**Original Context:**
- Multiple repositories for different frontend applications (web, mobile web, internal tools)
- Separate repos for shared component library published as npm packages
- 50+ engineers across frontend teams
- Frequent breaking changes in component library requiring coordinated updates

**What Multi-Repo Promised:**
- Clear boundaries between applications
- Independent deployment of each app
- Teams could move at their own pace

**Why They Moved to Monorepo:**
- Too much overhead keeping shared components in sync across repos
- Breaking changes in component library broke multiple apps
- Version fragmentation led to inconsistent user experience
- Atomic updates across components and consumers was needed
- Desire for consistent build and test infrastructure

**Migration Approach:**
- Created new Nx workspace with clear structure (apps/, libs/)
- Migrated projects incrementally, starting with most coupled
- Implemented remote caching for CI/CD performance
- Established CODEOWNERS for clear ownership boundaries
- Invested in developer tooling and documentation

**Why It Works:**
- Frontend projects are naturally tightly coupled through design system
- Shared components and consuming apps evolve together seamlessly
- Build tooling (Nx) handles complexity with affected detection
- Remote caching keeps CI/CD times under 10 minutes
- Team size manageable (100-200 frontend engineers)
- Refactoring across apps is straightforward and safe

**Key Success Factors:**
- Clear project structure prevents conflicts
- Investment in build tooling from day one
- Strong code review culture with ownership enforcement
- Incremental migration reduced risk and proved value early

**Continues to Work Because:**
- Design system changes propagate instantly to all apps
- New patterns discovered in one app quickly spread
- Consistent tooling reduces cognitive overhead
- Onboarding new engineers is faster with single codebase

---

## Case Study 2: SaaS Product Suite Consolidation

**Original Context:**
- Mid-size SaaS company with 20+ repos (web app, APIs, background workers, shared libraries)
- 30-50 engineers across multiple product teams
- Frequent cross-repo changes for features (feature flags, billing logic, auth flows)
- CI times growing and cross-repo breakages common

**What Multi-Repo Promised:**
- Independent scaling of services
- Clear service ownership
- Isolated deployments

**Why They Moved to Monorepo:**
- Hard to coordinate changes across many repos and teams
- Duplicate logic in multiple services and libraries
- Poor visibility into what depended on what
- Dependency version mismatches causing production bugs
- Cross-repo changes took days or weeks to coordinate

**Migration Approach:**
- Created `apps/`, `services/`, and `libs/` layout with clear ownership
- Adopted Turborepo for task orchestration and caching
- Introduced per-project metadata files feeding a service catalog
- Migrated shared libraries first, then applications
- Established workspace-wide dependency policies

**Why It Works:**
- Cross-cutting changes (pricing, auth) became single PRs instead of many
- CI stabilized because dependency graphs drove selective builds/tests
- Shared libraries reduced duplication and drift
- Feature development velocity increased significantly
- Deployment coordination simplified with atomic commits

**Key Success Factors:**
- Team size appropriate for monorepo coordination
- Modern tooling made migration practical
- Real coupling existed between projects
- Leadership support for migration investment

**Continues to Work Because:**
- Most features genuinely span multiple services
- Team remains small enough to coordinate effectively
- Tooling investment continues (remote caching, optimizations)
- Clear ownership prevents conflicts

---

## Case Study 3: Frontend Platform Unification

**Original Context:**
- Large frontend surface area: marketing site, dashboard, admin tools in separate repos
- 100+ frontend engineers across multiple teams
- Inconsistent design systems and build pipelines
- Multiple teams shipping to the same domains

**What Multi-Repo Promised:**
- Teams could choose their own frameworks
- Independent release schedules
- Clear boundaries between products

**Why They Moved to Monorepo:**
- Design system drift and duplicated UI components
- Difficult to roll out platform-wide improvements (accessibility, performance)
- High overhead maintaining many similar pipelines
- Brand inconsistency across products
- Hard to share performance optimizations

**Migration Approach:**
- Central `libs/ui-components` and `libs/design-tokens` libraries
- Shared Vite configuration and test setup
- Single CI template with per-app deployment stages
- Gradual migration over 6 months
- Nx workspace for build orchestration

**Why It Works:**
- Platform-wide UI and performance improvements ship faster
- Design and frontend platform teams enforce standards via shared code
- Onboarding improved: new engineers learn one repo and one set of tools
- Consistent user experience across all products
- TypeScript types shared across frontend

**Key Success Factors:**
- All apps used same tech stack (React/TypeScript)
- Strong platform team to maintain shared infrastructure
- Executive commitment to consistent brand experience
- Investment in migration tooling

**Continues to Work Because:**
- Design system is central to company brand
- Platform improvements benefit all products instantly
- Frontend engineers can contribute across products
- Build times managed with caching and affected detection

---

## Case Study 4: Domain Monolith for Business Area

**Original Context:**
- Enterprise with dozens of microservices in a single domain (billing)
- Each service in its own repo, but tightly coupled in practice
- 40-60 engineers working on billing domain
- Many cross-service incidents traced to incompatible changes

**What Multi-Repo Promised:**
- Service independence and autonomy
- Clear API contracts between services
- Independent deployment

**Why They Moved to Monorepo:**
- "Microservice sprawl" with unclear ownership and boundaries
- Repeated incidents from version skew and contract misunderstandings
- Difficulty answering "what is deployed where?" for the domain
- Most changes touched 3-5 services simultaneously
- Integration testing was nightmare across repos

**Migration Approach:**
- Consolidated domain services and libraries into single domain-focused monorepo
- Defined bounded contexts and ownership via directories and CODEOWNERS
- Standardized CI/CD and wired deployments into domain-specific dashboard
- Used Bazel for multi-language support (Java, Go, Python)
- Maintained service boundaries but in single repo

**Why It Works:**
- Refactors across domain became safer and more frequent
- Incident response improved because code and ownership in one place
- Governance and reporting for domain became far simpler
- Integration testing straightforward with all services present
- API contracts visible in single codebase

**Key Success Factors:**
- Services were actually tightly coupled, not independent
- Team structure aligned with monorepo (domain ownership)
- Tooling investment in build system
- Architecture patterns enforced within monorepo

**Continues to Work Because:**
- Domain boundaries are logical, not physical separation
- Most features span multiple services
- Single deployment pipeline for domain
- Team size appropriate for shared codebase

---

## Case Study 5: Stripe API Client Libraries

**Original Context:**
- Separate repositories for each language's API client library
- Ruby, Python, Node.js, Java, Go, PHP, .NET libraries in separate repos
- 10-15 engineers maintaining all libraries
- API changes required coordinated updates across all repos

**What Multi-Repo Promised:**
- Language-specific experts could own their library
- Independent release cycles per language
- Language-specific tooling per repo

**Why They Moved to Monorepo:**
- API changes needed to update all client libraries simultaneously
- Wanted consistent code generation from API specifications
- Desire for unified testing against live API
- Single place for API documentation and examples
- Consistency across languages was critical for user experience

**Migration Approach:**
- Created monorepo with language-specific directories
- Built code generation pipeline from OpenAPI specs
- Unified test infrastructure hitting test API
- Centralized CI/CD for all libraries
- Maintained separate publishing to language-specific registries

**Why It Works:**
- All libraries tightly coupled to same API surface
- Code generation ensures consistency across languages
- Breaking API changes update all libraries atomically
- Testing against same API catches compatibility issues early
- Small team can maintain visibility across all libraries

**Key Success Factors:**
- Strong automation via code generation
- Libraries naturally coupled to API specification
- Team small enough to understand all languages
- Clear separation maintained despite single repository

**Continues to Work Because:**
- API and all client libraries evolve in lock-step
- Consistency across languages maintained automatically
- Users have consistent experience regardless of language
- Documentation single source of truth

---

## Case Study 6: Internal Tooling Platform

**Original Context:**
- Enterprise with 30+ internal developer tools in separate repositories
- CLI tools, VS Code extensions, linters, code generators
- Inconsistent versioning and user experience
- Hard to discover what tools existed

**What Multi-Repo Promised:**
- Tools could evolve independently
- Different teams could own different tools
- Clear release boundaries

**Why They Moved to Monorepo:**
- Tools shared common libraries and infrastructure
- Inconsistent user experience across tools
- Hard to coordinate tool changes with platform changes
- Discovery problem: engineers didn't know what tools existed
- Integration between tools was painful

**Migration Approach:**
- Created monorepo under "developer-platform" organization
- Structured as tools/ and libs/ directories
- Adopted Nx for task orchestration
- Unified documentation in single repository
- Shared release process with consistent versioning

**Why It Works:**
- Common libraries shared across all tools reduce duplication
- Consistent UX patterns across tool suite
- Integration between tools straightforward
- Single documentation site for all tools
- Discoverability problem solved

**Key Success Factors:**
- Tools genuinely shared infrastructure
- Small team maintaining all tools (10-15 people)
- Strong focus on developer experience
- Unified documentation was priority

**Continues to Work Because:**
- Developer platform team owns entire tool suite
- Tools compose well together
- Common patterns make tools predictable
- New tools bootstrap quickly from existing patterns

---

## Common Themes: Why Moving to Monorepo Worked

### Tight Coupling Was Real
- Projects were separate but code frequently changed together
- Coordinating updates across repos was primary pain point
- Atomic changes valuable for maintaining consistency
- Version synchronization overhead outweighed autonomy benefits

### Team Size in Sweet Spot
- Small to medium teams (10-200 engineers per monorepo)
- Large enough to need organization, small enough to coordinate
- Everyone can understand full codebase over time
- Code review and ownership manageable at this scale

### Modern Tooling Available
- Nx, Turborepo, Bazel make monorepos practical
- Remote caching solves build performance challenges
- Affected detection prevents wasteful rebuilds
- Tooling investment reasonable for typical teams

### Code Reuse Was Priority
- Preventing duplicate implementations of similar functionality
- Making existing solutions discoverable
- Sharing patterns and best practices naturally
- Consistency in user/developer experience critical

### Consistency Valued Over Autonomy
- Enforcing code quality, security, testing standards simpler
- Single configuration for linters, formatters, type checkers
- Dependencies coordinated across entire codebase
- Upgrading frameworks affects everything (feature, not bug)

## What They Gained

### Velocity Improvements
- **Atomic changes**: Refactor across entire codebase in single PR
- **Faster reviews**: Reviewers see full context of changes
- **Simplified testing**: Test changes across affected projects automatically
- **Easier rollback**: Single commit contains all related changes

### Developer Experience
- **Code discoverability**: Find existing solutions easily
- **Simplified setup**: Clone one repo, run one command
- **Consistent tooling**: Learn once, apply everywhere
- **Cross-team visibility**: See what other teams are building

### Operational Benefits
- **Simplified CI/CD**: One pipeline pattern for all projects
- **Dependency management**: No version fragmentation
- **Security updates**: Apply once, affects everything
- **Compliance**: Enforce policies uniformly

## Keys to Successful Migration

1. **Start with Pain Points**
   - Migrate most tightly coupled projects first
   - Demonstrate value before moving everything
   - Measure coordination overhead before and after

2. **Invest in Tooling Early**
   - Don't attempt without proper build orchestration
   - Remote caching essential for CI/CD performance
   - Choose tools appropriate for language/framework

3. **Establish Clear Ownership**
   - Use CODEOWNERS files to define boundaries
   - Require reviews from owners for changes
   - Make ownership visible and discoverable

4. **Structure Matters**
   - Organize by apps/, libs/, tools/ or similar
   - Clear separation between deployable and shared code
   - Consistent naming conventions

5. **Enable Incremental Migration**
   - Don't require big-bang cutover
   - Allow projects to migrate one at a time
   - Prove value with early migrations

6. **Measure and Optimize**
   - Track build times and CI/CD performance
   - Monitor cache hit rates
   - Collect developer feedback regularly

## When Monorepo Migration Makes Sense

Consider moving to monorepo when:

1. **Coordination overhead is primary pain** - Spending more time coordinating than coding
2. **Code frequently changes together** - Most changes span multiple repositories
3. **Team size is appropriate** - 10-200 engineers who can coordinate
4. **Shared code is significant** - Substantial overlap between projects
5. **Tooling investment feasible** - Resources to adopt proper tooling
6. **Cultural fit exists** - Team values consistency and collaboration
7. **Consistency is critical** - User experience or developer experience demands it

## When to Stay Multi-Repo

Don't move to monorepo if:

- Teams need true autonomy over technology choices
- Projects have completely independent lifecycles
- Organization is very large (1000+ engineers) without massive tooling investment
- Products have distinct user bases with different requirements
- Cultural emphasis on independence over collaboration

## Long-Term Success Factors

Organizations maintaining successful monorepos share:

1. **Continuous tooling investment** - Build times don't optimize themselves
2. **Strong code review culture** - Quality maintained through reviews
3. **Clear architectural guidelines** - Boundaries enforced through convention
4. **Developer education** - Onboarding kept current
5. **Willingness to adapt** - Adjust structure as needs evolve
6. **Ownership discipline** - CODEOWNERS enforced consistently

## Conclusion

Moving to a monorepo can significantly improve developer velocity and code quality when done for the right reasons with proper tooling. Success stories show monorepos work well for:

- Tightly coupled projects that change together
- Teams in the 10-200 engineer range
- Organizations valuing consistency and code reuse
- Projects with shared domain logic and infrastructure

The key is being honest about whether projects are actually coupled, investing in proper tooling, and establishing clear ownership within the monorepo. Start small, demonstrate value, and expand gradually based on real results.

For organizations considering this transition, identify genuine coupling, prove value with tightly coupled projects first, and migrate incrementally rather than all at once.

