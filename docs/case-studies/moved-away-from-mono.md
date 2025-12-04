# Moving Away From Monorepos

While many organizations successfully adopt monorepos, some have migrated away from them to multi-repo or hybrid approaches. These case studies illustrate the challenges that led teams to reconsider monorepo strategies and what they gained (and lost) by moving to multi-repo setups.

## Case Studies: Monorepo to Multi-Repo Migrations

The examples below highlight what teams originally hoped to gain from a monorepo, where those expectations didn’t hold, and why a move toward multi-repo or hybrid structures made more sense at their scale.

### Uber: Splitting the Monorepo for Team Autonomy

**Original Context:**
- Large monorepo containing most of Uber's services and applications
- Thousands of engineers working in a single repository
- Custom build tooling and infrastructure

**What They Hoped Monorepo Would Provide:**
- Simpler dependency management with all code in one place
- Easier cross-service refactoring and shared library ownership
- Unified tooling and standards for all backend services

**Why They Moved Away:**
- Teams felt constrained by shared tooling and standards
- Build times became prohibitive despite optimization efforts
- Different services had vastly different deployment cadences
- Ownership boundaries were unclear, leading to friction
- Desire for teams to own their full technology stack

**Migration Approach:**
- Gradual extraction of services to separate repositories
- Established clear service boundaries and contracts
- Built internal tooling for service discovery and dependency management
- Created repository templates for consistency

**After the Migration:**
- Teams gained autonomy over their technology choices
- Clearer ownership and accountability per service
- Simplified CI/CD pipelines per repository
- Faster local development (smaller codebases to clone)

**Trade-offs:**
- Lost atomic cross-service changes
- Increased coordination overhead for shared libraries
- Dependency version fragmentation became a challenge
- Required investment in service catalog and discovery tools

**Lessons Learned:**
- Monorepo worked well early on but organizational scale shifted trade-offs
- Team autonomy became more valuable than atomic changes
- Clear service contracts are essential in multi-repo setups
- Migration can be gradual and iterative, not big-bang

**Current State:**
- Primarily multi-repo with some shared libraries in dedicated repos
- Investment in platform tooling to maintain some monorepo benefits
- Clear guidelines on when to create new repositories

---

### Twitter: From Monorepo to Microservices Architecture

**Original Context:**
- Rails monorepo serving most of Twitter's functionality
- Growing team struggling with deployment coordination
- Single large application with increasing complexity

**What They Hoped Monorepo Would Provide:**
- Simple, single-codebase development model
- Fast iteration without cross-repo coordination
- Centralized deployment of the entire product

**Why They Moved Away:**
- Deployment of any change required full application restart
- Difficult to scale specific features independently
- Build and test times growing unsustainable
- Want to adopt microservices architecture
- Need for polyglot technology stack (JVM, Go, etc.)

**Migration Approach:**
- Extracted services one at a time based on business domains
- Started with less critical features to learn migration patterns
- Built service mesh and internal tooling for service communication
- Gradually moved from monolithic Rails app to service-oriented architecture

**After the Migration:**
- Services could be scaled independently based on load
- Teams could choose appropriate technology for their domain
- Faster deployment cycles for individual services
- Better fault isolation (one service failure doesn't take down entire platform)

**Trade-offs:**
- Increased operational complexity (monitoring, debugging distributed systems)
- Need for sophisticated service mesh and observability tooling
- Cross-service features require careful coordination
- Lost simplicity of single codebase deployment

**Lessons Learned:**
- Monolithic monorepo appropriate for early-stage products
- Microservices architecture naturally pushes toward multi-repo
- Service boundaries should align with team and domain boundaries
- Operational tooling investment is critical for microservices success

---

### Segment: Repository Split for Product Focus

**Original Context:**
- Growing startup with multiple products in single monorepo
- Mix of customer-facing apps, data pipeline, and analytics products
- Team growing rapidly with new product lines

**What They Hoped Monorepo Would Provide:**
- Shared infrastructure and libraries across product lines
- Easier movement of engineers between teams
- Unified CI/CD and security controls

**Why They Moved Away:**
- Different products had different release cycles and customer bases
- Shared CI/CD pipeline became complex with conditional logic
- New engineers struggled to understand which code belonged to which product
- Product teams wanted autonomy over their development processes
- Deployment of one product sometimes affected others

**Migration Approach:**
- Split repository by product lines rather than by service
- Each product got its own repository with clear ownership
- Created shared library repositories for truly common code
- Standardized CI/CD templates to maintain consistency

**After the Migration:**
- Product teams gained independence and moved faster
- Clearer boundaries for what code belongs to which product
- Simpler CI/CD pipelines per product
- Easier onboarding for new team members (smaller scope)

**Trade-offs:**
- Shared library changes require publishing and version updates
- Cross-product features require coordination between teams
- Some code duplication emerged across products
- Lost unified view of all company code

**Lessons Learned:**
- When products have distinct identities, separate repos often make sense
- Product boundaries are often better split points than service boundaries
- Shared libraries should be genuine abstractions, not just code sharing
- Template repositories help maintain consistency across repos

---

### Medium-Sized SaaS Company: Escaping Build Time Pain

**Original Context:**
- Monorepo with 50+ projects (apps, services, libraries)
- Growing from 50 to 200 engineers
- Nx-based monorepo with affected detection

**What They Hoped Monorepo Would Provide:**
- Consistent tooling and standards across all products
- Ability to reuse code easily instead of creating new repos
- Central visibility into everything the engineering org owned

**Why They Moved Away:**
- Despite tooling, CI/CD times kept growing
- Merge conflicts became frequent as team grew
- New engineers overwhelmed by codebase size
- Remote caching helped but didn't solve fundamental issues
- Different product lines rarely shared code in practice

**Migration Approach:**
- Analyzed dependency graph to identify natural split points
- Created 5 separate repositories aligned with product teams
- Moved truly shared code to dedicated library repositories
- Kept some small projects in monorepos where it made sense (hybrid approach)

**After the Migration:**
- CI/CD times dropped significantly (10+ minutes to 2-3 minutes per repo)
- Fewer merge conflicts with smaller, team-focused repositories
- Clearer ownership and faster code review cycles
- Easier to reason about each codebase

**Trade-offs:**
- Lost ability to make atomic changes across products (but rarely needed)
- Shared library versioning requires coordination
- Need for dependency update automation (Renovate/Dependabot)
- Initially slower to share new common patterns

**Lessons Learned:**
- Monorepo tooling can help but doesn't eliminate all scale challenges
- If code rarely changes together, maybe it shouldn't live together
- Team structure should influence repository structure
- Hybrid approach (some monorepos, some multi-repo) can work well

---

### E-commerce Platform: Splitting Frontend and Backend

**Original Context:**
- Full-stack monorepo with Next.js frontend and Node.js backend
- Mobile apps also in the same repository
- Shared TypeScript types and utilities

**What They Hoped Monorepo Would Provide:**
- Single source of truth for all customer-facing code
- Easy sharing of types and utilities across clients and APIs
- One pipeline and environment per commit for the full stack

**Why They Moved Away:**
- Frontend and backend teams had different deployment schedules
- Mobile releases followed Apple/Google app store timelines
- Frontend wanted to experiment with new frameworks
- Backend wanted to migrate some services to Go
- Shared CI/CD pipeline had too many conditional branches

**Migration Approach:**
- Split into separate repositories: web-frontend, mobile-apps, backend-services
- Created shared-types package published to private npm registry
- Each repository has its own CI/CD pipeline optimized for its needs
- Maintained API contracts using OpenAPI specifications

**After the Migration:**
- Frontend can deploy multiple times per day without backend changes
- Backend team migrated critical services to Go without frontend concerns
- Mobile team follows app store release schedule independently
- Clear contracts between frontend and backend improved API design

**Trade-offs:**
- Type definitions require publishing and version synchronization
- Breaking API changes require coordinated updates across repos
- Lost convenience of changing types and implementation atomically
- Need for API versioning and backward compatibility strategies

**Lessons Learned:**
- When deployment schedules diverge significantly, separate repos often make sense
- Strong API contracts become critical in multi-repo setups
- Can maintain type safety across repos with published type packages
- Technology diversity is easier with separate repositories

---

## Common Themes: Why Organizations Leave Monorepos

Based on these case studies, several patterns emerge:

### Organizational Scale
- As teams grow beyond certain size (often 100-200 engineers), monorepo coordination becomes challenging
- Merge conflicts, code review bottlenecks, and ownership ambiguity increase with scale
- Team autonomy becomes more valuable than atomic changes

### Build Performance
- Despite tooling (Nx, Turborepo, Bazel), some organizations hit performance ceilings
- CI/CD times impact developer productivity and slow down releases
- Remote caching helps but doesn't eliminate all issues

### Product Independence
- When products have distinct user bases, release cycles, and teams, separate repos often align better
- Multi-repo allows product teams to own their full stack and processes
- Clear product boundaries justify repository boundaries

### Technology Diversity
- Monorepos work best with homogeneous technology stacks
- Need for polyglot architectures (mixing JVM, Go, Node.js, Python) favors multi-repo
- Different languages have different build systems and tooling requirements

### Deployment Cadence Mismatch
- When different parts of codebase need vastly different release schedules
- Mobile apps (app store cycles) vs web apps (continuous deployment)
- Critical services vs experimental features

## What They Lost by Moving Away

It's important to acknowledge what these organizations gave up:

- **Atomic cross-cutting changes**: Can no longer refactor across entire codebase in single commit
- **Single source of truth**: Code is scattered across repositories
- **Simplified dependency management**: Versioning and compatibility become explicit concerns
- **Unified tooling**: Each repository may drift in tools and standards
- **Code discoverability**: Harder to find existing solutions across repositories

## Making the Decision

Moving away from a monorepo is a significant undertaking. Consider this path when:

1. **Build times are unsustainable** despite optimization and tooling investment
2. **Team autonomy** is more valuable than atomic changes for your organization
3. **Clear product or service boundaries** exist that rarely change together
4. **Technology diversity** is essential for your architecture
5. **Organizational structure** naturally aligns with repository splits

However, exhaust these options first:
- Better tooling and caching strategies
- Clearer ownership rules and CODEOWNERS files
- More granular affected detection
- Better developer education and documentation
- Enforced architectural boundaries within monorepo (like Shopify's Packwerk)

## Hybrid as Middle Ground

Many organizations don't fully commit to multi-repo but adopt a hybrid approach:
- Some tightly coupled projects remain in monorepos
- Independent products or services get separate repositories
- Shared libraries in dedicated repos
- Clear criteria for when to create new repositories

See `../migration/strategies.md` for practical guidance on migrating between repository strategies.

## Conclusion

Moving away from a monorepo is not an admission of failure—it's often a natural evolution as organizations scale and needs change. The case studies show that both approaches can work, and the right choice depends on your specific context, team structure, and technical requirements.

The key is making a deliberate decision based on your actual pain points rather than following trends, and being willing to adapt as your organization evolves.
