# DevCollab Workflow

A lightweight, extensible collaboration workflow scaffold for building and operating developer-facing systems. This repository focuses on a clear separation of concerns (interfaces ↔ application/workflow ↔ domain ↔ infrastructure), so teams can iterate quickly without accumulating coupling.

## Project summary 
- Built and documented a modular **developer collaboration workflow** with an **architecture-first** structure for long-term maintainability.
- Applied **clean/hexagonal architecture** principles to keep core workflow logic independent from delivery mechanisms (CLI/HTTP) and external services.
- Established clear **layer boundaries, conventions, and repository structure** to accelerate onboarding and make adding new workflow steps/integrations predictable.

## Architecture 

### High-level view
The system is organized into layered modules so that business rules remain independent of frameworks and external services.

**Typical flow (request / job execution):**
1. **Interfaces (delivery)**: CLI / HTTP / event triggers receive input.
2. **Application layer**: use-cases validate inputs and orchestrate workflow steps.
3. **Domain layer**: workflow policies, entities, and invariants (framework-agnostic).
4. **Infrastructure layer**: adapters for persistence, messaging, and external APIs.
5. **Observability**: logs/metrics/traces emitted consistently across layers.

### Layers & responsibilities

#### 1) Domain layer (core)
**Purpose:** Source of truth for business rules.
- Entities / value objects
- Workflow state machine or step definitions
- Domain services (pure logic)
- Domain events (optional)

**Rules:**
- No direct dependency on frameworks, databases, or HTTP.
- Should only depend on language/runtime standard libraries.

#### 2) Application layer (use-cases)
**Purpose:** Orchestrates domain logic into executable operations.
- Use-cases (e.g., create collaboration request, run workflow step)
- Input validation, authorization hooks, transaction boundaries
- Ports/interfaces for persistence and external integrations

**Rules:**
- Depends on Domain.
- Defines interfaces that Infrastructure implements.

#### 3) Infrastructure layer (adapters)
**Purpose:** Integrates external systems.
- Database repositories
- HTTP clients / SDK wrappers
- Message queue/event bus adapters
- File storage adapters

**Rules:**
- Depends on Application + Domain.
- Must be replaceable without changing Domain logic.

#### 4) Interfaces / Delivery layer (entry points)
**Purpose:** Accepts input and returns output.
- REST/GraphQL handlers, CLI commands, scheduled jobs
- DTOs / request-response models

**Rules:**
- Depends on Application.
- Handles transport-specific concerns (HTTP status codes, CLI formatting).

### Key design principles
- **Ports & adapters (hexagonal)**: swap infra without rewriting core logic.
- **Test pyramid**: domain/application unit tests are fast; infra tests are narrower.
- **Explicit contracts**: interfaces define what the workflow needs, not how it’s implemented.
- **Observability-by-default**: correlation IDs, structured logs, and error boundaries.

## Repository structure (recommended)
> Adjust names to match the actual folder layout.

- `src/domain/` — core workflow rules, entities, state
- `src/application/` — use-cases, orchestration, ports
- `src/infrastructure/` — DB, external API integrations, messaging
- `src/interfaces/` — HTTP/CLI entry points
- `docs/` — architecture notes, ADRs, diagrams
- `.github/workflows/` — CI/CD automation

## Getting started
1. Clone the repo
2. Install dependencies
3. Run tests
4. Start the service / run the CLI (depending on the chosen entry point)

## Contributing
- Keep domain logic free of infrastructure dependencies.
- Prefer adding new workflow steps via application-layer use-cases + domain step definitions.
- Add/extend adapters in `infrastructure/` without leaking details upward.

## License
Add license information here.
