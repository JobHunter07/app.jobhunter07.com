<!--
SYNC IMPACT REPORT
Version change: 0.0.0 → 0.0.1
Constitution Type: Initial Ratification (Pre-Release)
Modified Principles: All (Initial Creation)
Added Sections: All core principles, security, performance, design, testing, operational
Status: ✅ Constitution created
Versioning: Pre-release v0.0.1 - will become v1.0.0 upon production deployment
Next Steps: Review and populate other templates (plan, spec, tasks) with constitution references
-->

# app.jobhunter07.com Constitution

## Project Overview

**app.jobhunter07.com** is the core application of the JobHunter07 ecosystem. It provides all functional logic: authentication, authorization, user dashboards, job-hunting automation, billing, data persistence, and secure user workflows.

The marketing site (jobhunter07.com) is separate and static; this application is the operational engine.

**Technology Stack**: Next.js, React Server Components, TypeScript, TailwindCSS, Stripe, NextAuth

## Core Principles

### I. Spec-Driven Design (NON-NEGOTIABLE)

**Nothing is coded until it is fully specified, reviewed, and approved.**

- Every feature MUST have a specification before implementation begins
- Specifications define: purpose, inputs/outputs, states, interactions, error conditions, accessibility requirements, and Storybook requirements
- Specs must be small, clear, and testable
- No pull request is accepted without a reference to its approved specification

**Rationale**: Prevents rework, ensures alignment, enables parallel work, creates living documentation.

### II. Component-First Architecture

**Every UI element is a component, built bottom-up using Atomic Design.**

- **Atomic Design with Three Levels Only**: Atoms → Organisms → Pages (No Molecules)
- Components must be reusable, composable, predictable, and consistent with the design system
- No component is created without a corresponding specification
- Every component MUST have a Storybook story
- Components must be responsive by default

**Component Hierarchy**:
- **Atoms**: Basic building blocks (buttons, inputs, labels, icons)
- **Organisms**: Functional composites of atoms (forms, cards, navigation bars)
- **Pages**: Complete screens composed of organisms and atoms

**Rationale**: Simplicity through constraint. Three levels prevent over-engineering while maintaining composability.

### III. Screaming Architecture & Vertical Slice Architecture

**The folder layout must make use-cases obvious at a glance.**

- Features are organized by **vertical slices**, not technical layers
- Each slice contains: UI, server actions, validation, data access, tests, stories, and documentation
- Vertical slices are self-contained and deployable independently
- No cross-slice coupling except through explicitly defined interfaces
- Shared utilities live in `/src/lib` and must be stable, documented, and versioned

**Structure Example**:
```
src/
├── features/
│   ├── authentication/
│   │   ├── components/
│   │   ├── actions/
│   │   ├── schemas/
│   │   ├── tests/
│   │   ├── stories/
│   │   └── README.md
│   ├── job-tracking/
│   └── billing/
├── lib/
│   ├── database/
│   ├── validation/
│   └── utils/
└── stories/
```

**Rationale**: Features are use-case driven. New developers understand the domain from the folder structure.

### IV. Security-First (NON-NEGOTIABLE)

**Security is foundational and non-negotiable.**

**Authentication**:
- Use secure, modern methods: NextAuth with OAuth, email magic links, or passkeys
- No passwords stored in plain text
- Session management with secure, httpOnly cookies

**Authorization**:
- Role-based access control (RBAC) enforced at:
  - Server actions
  - API routes
  - Database queries (Row-Level Security where supported)
  - UI visibility
- Client-side state is NEVER trusted
- All authorization checks happen server-side

**Data Protection**:
- Sensitive data MUST be encrypted at rest and in transit
- Secrets MUST NEVER be committed to the repository
- Environment variables MUST be validated with a schema (e.g., Zod)
- All services follow least-privilege access principles

**Billing Security** (Stripe):
- Server-side only secret usage
- Webhook signature validation REQUIRED
- Idempotency keys for all mutations
- Role checks for subscription changes
- Client-provided pricing or plan IDs are NEVER trusted

**Production Hardening**:
- HTTPS everywhere
- Rate limiting on all public endpoints
- Input validation on every server action
- Structured logging for security events
- Monitoring with anomaly detection
- Content Security Policy (CSP)
- XSS protection headers
- Secure headers (HSTS, X-Frame-Options, etc.)

**Rationale**: Breaches are catastrophic. Security cannot be retrofitted.

### V. Performance Excellence

**Performance is a feature, not an afterthought.**

- Use React Server Components (RSC) by default
- Minimize client-side JavaScript bundle size
- Cache aggressively:
  - Next.js App Router caching
  - React `cache` function
  - CDN edge caching for static assets
- Avoid unnecessary re-renders through memoization and stable props
- Optimize images with Next.js Image component
- Lazy load components and routes where appropriate
- Monitor and set performance budgets (e.g., LCP < 2.5s, FID < 100ms, CLS < 0.1)

**Rationale**: Users expect instant experiences. Performance impacts conversions and retention.

### VI. Data Configuration Management

**Centralize all data schemas, validation, and types with a single source of truth.**

- All domain models defined in schema files (e.g., Zod schemas)
- Types generated from schemas (not vice versa)
- All data access goes through typed, validated interfaces
- Database schema migrations are versioned and reversible
- API contracts are strongly typed (e.g., tRPC, Zod validation)

**Rationale**: Prevents drift between frontend, backend, and database. Enables safe refactoring.

### VII. Design System & Visual Consistency

**Design follows the JobHunter07 Design System with strict constraints.**

**Styling**:
- TailwindCSS ONLY (no CSS-in-JS, no CSS modules except for edge cases)
- Extend the JobHunter07 design system (colors, spacing, typography)
- Avoid purple and heavy gradients
- Favor clarity, contrast, and accessibility (WCAG 2.1 Level AA minimum)

**Design Principles**:
- Simplicity over ornamentation
- Consistency over novelty
- Accessibility over aesthetics
- Mobile-first responsive design

**Rationale**: Consistency builds trust. Constraints enable speed.

### VIII. Storybook-Driven Component Development

**Every component MUST have a Storybook story.**

**Story Requirements**:
- Stories live in `src/stories`
- File name matches the component (e.g., `Button.stories.tsx`)
- Use CSF3 (Component Story Format 3) with autodocs
- Include:
  - Default state
  - Edge cases (empty, loading, error, disabled, max content)
  - Accessibility checks (via @storybook/addon-a11y)
  - Interactive controls for props
  - Interaction tests where applicable
- Stories use relative imports from the component directory

**Rationale**: Storybook is living documentation, visual testing, and isolated development environment.

## Development Workflow

### Spec-Driven Development

**Workflow**:
1. Feature request → Specification created (not code)
2. Specification reviewed and approved
3. Specification decomposed into atomic tasks
4. Tasks implemented in order, each referencing the spec
5. Implementation reviewed against specification
6. Tests validate behavior matches spec
7. Storybook stories document component per spec
8. Feature deployed

**Task Atomicity**:
- Every task is atomic: independently deliverable, testable, and traceable to a spec
- Each task is estimated as **one story point** (if larger, decompose further)
- Tasks have clear acceptance criteria derived from the spec

### Testing Requirements

**Testing Pyramid**:
- **Unit Tests**: All utility functions, validation logic, pure functions
- **Integration Tests**: Server actions, API routes, database interactions
- **Component Tests**: React Testing Library for UI logic
- **Visual Regression Tests**: Storybook with Chromatic or Percy
- **Accessibility Tests**: Every component tested with axe in Storybook
- **End-to-End Tests**: Critical user flows (authentication, billing, job tracking)

**Coverage Standards**:
- Minimum 80% code coverage for logic (excluding types, config)
- 100% coverage for security-critical paths (auth, billing, data access)

**Rationale**: Confidence in changes. Fast feedback loops. Living documentation through tests.

## Operational Principles

### Observability

**Structured Logging**:
- All logs must be structured (JSON) with context: userId, requestId, timestamp, severity
- Log levels: ERROR, WARN, INFO, DEBUG
- No sensitive data in logs (PII, tokens, secrets)

**Monitoring**:
- Track key metrics:
  - API latency (p50, p95, p99)
  - Authentication failures
  - Billing events (subscriptions, payments, failures)
  - Error rates by endpoint
  - Database query performance
- Set up alerts for anomalies

**Error Boundaries**:
- Every page has an error boundary
- Graceful degradation where possible
- User-friendly error messages (no stack traces to users)

### CI/CD & Quality Gates

**Continuous Integration**:
- Every commit runs:
  - Type checks (TypeScript strict mode)
  - Linting (ESLint)
  - Unit and integration tests
  - Storybook build
  - Accessibility tests

**Deployment Gates**:
- No deployment with failing checks
- Manual approval for production
- Automated preview deployments for PRs

**Rationale**: Catch issues early. Prevent broken deploys. Enable fast rollback.

### Documentation

**Every vertical slice MUST include**:
- README.md with:
  - Purpose and use cases
  - Architecture notes
  - Data flow diagrams (text-based diagrams acceptable)
  - Setup and usage instructions
  - Storybook references for UI components
- Inline code comments for complex logic
- API documentation (JSDoc or similar)

**Rationale**: Documentation is part of the deliverable, not an afterthought.

## Guiding Philosophy

**Security first**: Trust no input. Validate everything. Least privilege always.

**Predictability over cleverness**: Code should be obvious, not clever.

**Simplicity over complexity**: Choose boring solutions. Avoid premature abstraction.

**Explicitness over magic**: Favor explicit code over framework magic or implicit behavior.

**Human-centered design**: Users come first. Accessibility is not optional.

**Spec before code**: Thinking precedes typing.

**Vertical slices over horizontal layers**: Organize by feature, not by framework convention.

**Quality over speed**: Fast code that breaks is slow code.

## Governance

This constitution supersedes all other practices and conventions.

**Amendment Process**:
- Proposed changes require written justification
- Amendments require team review and approval
- Breaking changes require migration plan and timeline
- Version bump follows the versioning strategy:
  - **Pre-Release (0.x.x)**: Current phase while standards are being established
    - **0.0.x = PATCH**: Clarifications, typos, non-semantic refinements
    - **0.x.0 = MINOR**: New principles added, material expansions
    - **No MAJOR version during pre-release**
  - **Production Release (1.0.0)**: Constitution finalized and project launched to production
  - **Post-Production (1.x.x+)**: Standard semantic versioning
    - **MAJOR**: Backward-incompatible principle removals or redefinitions
    - **MINOR**: New principle/section added or materially expanded
    - **PATCH**: Clarifications, wording improvements, non-semantic refinements

**Compliance**:
- All pull requests MUST verify compliance with constitution principles
- Code reviews include constitution adherence check
- Complexity or deviations MUST be explicitly justified in PR description

**Runtime Guidance**:
- Use [.specify/memory/constitution.md](.specify/memory/constitution.md) for development decisions
- Use [.github/agents/](.github/agents) for agent-specific workflows
- Use [.specify/templates/](.specify/templates) for spec, task, and plan structures

**Version**: 0.0.1 | **Status**: Pre-Release | **Ratified**: 2026-02-12 | **Last Amended**: 2026-02-12
