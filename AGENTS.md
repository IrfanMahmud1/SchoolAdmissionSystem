# School Admission System — Agent Instructions

## Mission
Build the take-home School Admission System described in the supplied assignment PDF. The deadline is 2026-11-11 23:59:59 Bangladesh time. Prioritize a correct, runnable, demonstrable submission over unnecessary architecture.

## Non-negotiable requirements
- Backend: C# / .NET 8 or newer, PostgreSQL, ORM of choice.
- Frontend: React 18 or newer, TypeScript preferred, Vite.
- Root `docker-compose.yml` must start PostgreSQL, backend, and frontend with `docker compose up`.
- Evaluator machine must need Docker only; do not require local .NET, Node, or PostgreSQL.
- Backend must be reachable at `http://localhost:8080`.
- DB connection must be configurable by environment variable.
- `HOLD_DURATION_SECONDS` must be configurable; default is 600.
- Never allow `heldSeats + confirmedSeats > totalSeats`, including concurrent requests.
- Expired holds release their seats.
- Confirm is idempotent: repeating confirmation must not increment confirmed seats more than once.
- A student may have at most one active or confirmed application for a class.
- Data must survive application restart and `docker compose down` followed by `docker compose up`.
- Invalid input returns 4xx, not 500.
- All user tasks must be possible from the UI; no Postman or direct DB interaction.
- UI must show EXPIRED and disable payment/confirmation after expiry; seat counts refresh without page reload; student application history restores after refresh/browser reopen.
- Friendly errors for full seats, expired hold, invalid input, and unavailable server.
- Include at least one automated concurrency test.
- Maintain `ASSUMPTIONS.md` with rationale for interpretations not explicit in the brief.
- Follow the exact API routes and DTO shapes in the assignment PDF. Do not invent replacements.

## Required API contract from assignment
1. `POST /classes`
2. `GET /classes`
3. `GET /classes/{classId}`
4. `POST /classes/{classId}/holds` — header `X-Student-Id`
5. `GET /holds/{holdId}`
6. `POST /holds/{holdId}/confirm` — header `X-Student-Id`
7. `DELETE /holds/{holdId}` — header `X-Student-Id`; successful response 204
8. `GET /me/holds` — header `X-Student-Id`

Inspect the PDF for exact request/response examples and status codes before implementing each route. Do not add authentication or payment gateway integrations: the brief describes a mock fee confirmation and identifies the student using `X-Student-Id`.

## Working mode: autonomous, checkpointed
You may make routine implementation decisions autonomously. Do not ask the user for approval for ordinary code choices. If a requirement is genuinely ambiguous, choose the simplest defensible interpretation, record it in `ASSUMPTIONS.md`, and continue.

Work in small milestones:
1. Inspect environment and assignment PDF; write a concise implementation plan.
2. Create solution skeleton and Docker Compose.
3. Implement persistence and class APIs.
4. Implement hold lifecycle and concurrency-safe seat allocation.
5. Add tests, including concurrent hold requests.
6. Implement React UI and persistence/refresh behavior.
7. Run the complete Docker-based acceptance check.
8. Finish docs, Git history, and submission checklist.

After EACH milestone:
- Run relevant build/test checks.
- Fix failures before moving on where practical.
- Update `TASKS.md` and `PROJECT_STATE.md` with actual results.
- Record material design choices in `DECISIONS.md`.
- Make a meaningful Git commit.
- Never claim a test passed unless it was actually run and passed.

Before context/session ends:
- Stop feature work.
- Update `PROJECT_STATE.md` with exact next action, commands, failing tests, and modified files.
- Ensure the working tree and latest commit state are accurately recorded.

## Architecture and code quality
Use lightweight Clean Architecture / separation of concerns, not architecture for its own sake:
- `backend/SchoolAdmission.Api`: HTTP endpoints, middleware, DI, configuration.
- `backend/SchoolAdmission.Application`: use cases, DTOs, validation, interfaces and business rules.
- `backend/SchoolAdmission.Infrastructure`: EF Core, PostgreSQL, persistence and transaction/locking implementation.
- `backend/SchoolAdmission.Tests`: automated tests.
- `frontend/`: React + TypeScript + Vite.

Keep the domain model close to the business concepts. A separate Domain project is optional; do not create it if it adds friction. Avoid microservices, CQRS/MediatR, Redis, event buses, generic repository frameworks, and speculative abstractions.

Code expectations:
- Clear names, small cohesive methods, modular files, readable control flow.
- Apply SOLID where it improves maintainability; do not force patterns.
- Keep business rules out of controllers and React components.
- Validate input at API boundaries and return consistent problem responses.
- Use async I/O appropriately; propagate cancellation tokens where practical.
- Never expose stack traces, SQL, secrets, or internal exception details to clients.
- Use transactions and PostgreSQL-safe concurrency control for seat allocation. Do not rely on an in-memory lock or a check-then-insert sequence that can overbook across concurrent requests or multiple app instances.
- Enforce idempotency and one-active-application rules in a concurrency-safe way, ideally with database constraints plus transactional logic.
- Store timestamps in UTC; compare expiry using server-side UTC time.
- Add useful indexes and database constraints.
- Avoid premature optimization; correctness and clear SQL/EF behavior come first.
- No secrets committed. Provide `.env.example`.

## Frontend expectations
- Functional, polished, responsive admin + guardian/student workflows.
- Do not leave important functionality as TODOs or console-only behavior.
- Keep API calls in a small typed API layer; keep reusable UI pieces focused.
- Persist the student identifier locally so refresh/reopen restores the student's applications.
- Refresh class seat counts periodically or through a simple polling strategy; no page reload required.
- Handle loading, empty, success, full-seat, expired, invalid-input, and server-unavailable states.
- Disable confirm/payment action when hold is expired or no longer HELD.
- Make it clear that payment is mocked, not a real payment integration.
- Avoid excessive animation and unnecessary UI dependencies.

## Docker and runtime
- Root compose file must build/run all three services.
- Use a persistent named PostgreSQL volume.
- Configure backend connection string and `HOLD_DURATION_SECONDS` through environment variables.
- Configure frontend API base URL through environment variables suitable for Vite build/runtime.
- Ensure frontend container can reach backend correctly from browser and host; remember browser requests to `localhost:8080` differ from container-to-container DNS.
- Add health checks and startup dependency handling where useful.
- Ensure migrations/schema initialization strategy works on a clean first startup.
- Verify with Docker, not just local IDE runs.

## Security
- Treat `X-Student-Id` as an assignment-level identity mechanism, not production authentication.
- Validate and normalize student IDs; never trust a client-supplied class seat count or status.
- Protect admin-only class creation in the UI as far as the brief permits, and document any simplified admin assumption.
- Use parameterized EF Core queries; do not concatenate SQL from user input.
- Keep CORS narrowly configured for the provided frontend origin(s).

## Git and documentation
- Create meaningful commits at milestones; do not commit build output, secrets, `.env`, or database files.
- Keep `README.md` focused on prerequisites (Docker only), startup, URLs, sample workflow, environment variables, tests, and architecture/concurrency explanation.
- Include `ASSUMPTIONS.md`, `AI_USAGE.md`, `TESTING.md`, and `PROJECT_STATE.md`.
- `AI_USAGE.md` must honestly describe AI assistance and human review/decisions. Never fabricate manual work or hide AI use.
- Final check: clean clone / fresh Docker startup, UI demo, API contract, concurrency test, persistence after restart, and public GitHub repository.
