# Technical Decisions

Record only decisions that affect architecture, correctness, or delivery.

## D-001 — Lightweight layered architecture
- Status: Proposed
- Decision: Separate API, Application, Infrastructure, Tests, and frontend.
- Rationale: Enough separation for maintainability and testability without spending deadline on excessive abstractions.
- Revisit only if implementation complexity proves it necessary.

## D-002 — PostgreSQL is the source of truth
- Status: Proposed
- Decision: All class capacities, holds, confirmations, expiry and ownership state live in PostgreSQL.
- Rationale: In-memory state is lost on restart and cannot safely coordinate concurrent requests across instances.

## D-003 — Concurrency strategy
- Status: Pending implementation
- Decision: Use a PostgreSQL transaction and row-level locking / atomic database operation for seat allocation. Add DB constraints where they can enforce invariants.
- Rationale: A simple read-count-then-insert sequence can overbook under concurrent requests.
- Evidence: Record the chosen SQL/EF implementation and concurrency test result here.

## D-004 — Student identity
- Status: Proposed
- Decision: Use the assignment's `X-Student-Id` header; persist it in browser storage for the demo.
- Rationale: This is the explicit assignment contract. It is not production-grade authentication.

## D-005 — Mock payment
- Status: Proposed
- Decision: Confirm endpoint simulates successful fee payment; no real payment provider.
- Rationale: The brief calls for mock fee confirmation.

## D-006 — Hold expiry
- Status: Pending implementation
- Decision: Store `ExpiresAt` in UTC and evaluate expiry on relevant reads/writes; expired HELD records no longer count against capacity.
- Rationale: Correctness must not depend on a background worker running at the exact expiry moment.
