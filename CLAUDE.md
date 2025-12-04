# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Purpose

This is a documentation project that explores and compares monorepo versus multi-repo architectural patterns. The goal is to provide comprehensive, objective analysis of the pros, cons, trade-offs, and use cases for each approach.

## Repository Structure

```
docs/
  index.md                      - Main entry point routing to all topics
  monorepo/
    overview.md                 - Comprehensive monorepo overview with examples
  multi-repo/
    overview.md                 - Comprehensive multi-repo overview with examples
  ci-cd/
    overview.md                 - Pipeline patterns, caching, pitfalls for both approaches
  tooling/
    overview.md                 - Detailed tooling ecosystem (Nx, Turborepo, Bazel, etc.)
  case-studies/
    index.md                    - Real-world examples (Google, Meta, Netflix, etc.)
    moved-away-from-mono.md     - Organizations that left monorepos
    moved-to-mono-worked.md     - Successful multi-repo to monorepo migrations
  governance/
    overview.md                 - Governance and reporting considerations
    catalog-and-metadata.md     - Service catalogs and metadata
    dependencies-and-impact.md  - Dependency tracking
    release-and-versioning.md   - Release strategies
    environment-status.md       - Deployment tracking
  migration/
    strategies.md               - Migration patterns and approaches
  assets/
    README.md                   - Images and diagrams directory
examples/                       - Code samples and configurations
```

## Documentation Style

### Writing Guidelines

- **Objective and balanced**: Present trade-offs honestly, don't advocate for one approach
- **Practical focus**: Include real code examples, commands, and configurations
- **Evidence-based**: Use case studies and real-world examples
- **Comprehensive but focused**: Each file covers one topic thoroughly
- **Cross-referenced**: Link to related documents for connected concepts

### Content Characteristics

- All documentation in Markdown (.md) format
- Code blocks with language tags (bash, yaml, json, typescript, etc.)
- Real tool names and versions (Nx, Turborepo, Bazel, GitHub Actions, etc.)
- Concrete examples over abstract theory
- Team sizes and organizational context when discussing patterns
- Use "for example" not "e.g." in prose

### Structure Patterns

Each major overview document follows similar structure:
1. Introduction defining the approach
2. Core characteristics
3. Why teams choose this approach (benefits)
4. Common challenges
5. Tooling and workflows
6. Example layouts or configurations
7. Cross-references to related topics

Case studies follow consistent format:
- Context (organization size, domain, team size)
- Why they chose this approach or migrated
- Migration approach (if applicable)
- Why it works or worked
- Benefits and trade-offs
- Key success factors
- Lessons learned

## Key Documentation Areas

### Completed Sections

- **Monorepo fundamentals**: `docs/monorepo/overview.md` - characteristics, benefits, challenges, tooling, example layouts
- **Multi-repo fundamentals**: `docs/multi-repo/overview.md` - characteristics, benefits, challenges, tooling, example layouts
- **CI/CD patterns**: `docs/ci-cd/overview.md` - pipelines, caching, pitfalls, scaling for both approaches
- **Tooling ecosystem**: `docs/tooling/overview.md` - comprehensive tool coverage (build systems, package managers, security, etc.)
- **Case studies**:
  - `docs/case-studies/index.md` - 10 major case studies (Google, Meta, Netflix, etc.)
  - `docs/case-studies/moved-away-from-mono.md` - 5 examples of monorepo to multi-repo
  - `docs/case-studies/moved-to-mono-worked.md` - 6 examples of multi-repo to monorepo

### Areas for Future Expansion

- `docs/migration/strategies.md` - Needs detailed migration playbooks
- `docs/governance/*` - Governance files need expansion with examples
- Additional case studies as they emerge
- Diagrams and visual aids in `docs/assets/`
- Example configurations in `examples/`

## Content Organization Principles

When adding or editing documentation:

1. **Objectivity**: Present both sides fairly, acknowledge trade-offs
2. **Specificity**: Name specific tools, versions, companies (when public)
3. **Practicality**: Include runnable examples, commands, configurations
4. **Context**: Mention team sizes, organizational structures, scales
5. **Cross-reference**: Link to related docs (`../governance/overview.md` style)
6. **Real-world grounding**: Use case studies and examples from production
7. **Modularity**: Each file self-contained but interconnected

## Documentation Patterns to Follow

### Code Examples

Include practical, runnable examples:
```yaml
# Example: GitHub Actions with affected detection
- name: Detect affected projects
  run: npx nx affected:build --base=origin/main --head=HEAD
```

### Tool Mentions

Be specific about tools and their purposes:
- **Nx** - JavaScript/TypeScript monorepo tool with dependency graphs
- **Turborepo** - Simpler, faster build system for JS/TS monorepos
- **Bazel** - Google's language-agnostic build system

### Case Study Format

Follow established pattern:
- Context section with scale metrics
- "Why they moved" or "What they hoped to gain"
- Migration approach (concrete steps)
- Results (benefits and trade-offs)
- Lessons learned

### Cross-References

Use relative paths consistently:
- `See ../governance/overview.md for governance details`
- `For CI/CD patterns, see ../ci-cd/overview.md`
- `See moved-away-from-mono.md for counterexamples`

## Common Topics Covered

The documentation comprehensively addresses:
- Definitions and core characteristics of monorepo vs multi-repo
- Advantages, disadvantages, and trade-offs for each
- Tooling ecosystems (build systems, package managers, CI/CD, security)
- CI/CD pipeline patterns and performance optimization
- Code sharing, dependency management, versioning strategies
- Team collaboration, ownership models, organizational scaling
- Governance, reporting, and visibility across codebases
- Migration strategies and real-world case studies
- When to choose each approach based on context

## Target Audience

Documentation written for:
- Engineering leaders evaluating repository strategies
- Architects designing development workflows
- Platform teams implementing tooling
- Teams considering migration between strategies
- Developers wanting to understand trade-offs

Assume technical competence but don't assume prior monorepo/multi-repo experience.
