# Delivery Checklist

Legend: `[ ]` not started, `[~]` in progress, `[x]` completed and verified.

## 0. Bootstrap
- [ ] Inspect assignment PDF end-to-end and extract exact request/response/status contracts.
- [ ] Verify Docker engine, Compose, Git, OpenCode.
- [ ] Initialize Git repository and `.gitignore`.
- [ ] Create solution and frontend skeleton.
- [ ] Add root `docker-compose.yml`, PostgreSQL service, persistent volume.
- [ ] Add `.env.example` and environment configuration.
- [ ] Confirm clean `docker compose up` works.

## 1. Data model and persistence
- [ ] Define class/shift model and application/hold model.
- [ ] Define statuses: HELD, CONFIRMED, EXPIRED, CANCELLED.
- [ ] Add UTC expiry timestamp and configured hold duration.
- [ ] Add database indexes and uniqueness constraints for student/class active-or-confirmed rule.
- [ ] Implement migration/schema initialization for clean startup.
- [ ] Verify data survives app restart and compose down/up.

## 2. Backend API
- [ ] `POST /classes`
- [ ] `GET /classes`
- [ ] `GET /classes/{classId}`
- [ ] `POST /classes/{classId}/holds`
- [ ] `GET /holds/{holdId}`
- [ ] `POST /holds/{holdId}/confirm`
- [ ] `DELETE /holds/{holdId}`
- [ ] `GET /me/holds`
- [ ] Validate `X-Student-Id` on student-specific routes.
- [ ] Return 4xx for invalid input and business conflicts.
- [ ] Return friendly, consistent error responses.
- [ ] Add OpenAPI/Swagger if time permits without jeopardizing core requirements.

## 3. Critical business rules
- [ ] Seat allocation is concurrency-safe in PostgreSQL.
- [ ] Held + confirmed never exceeds total seats.
- [ ] Expired HELD records stop consuming seats.
- [ ] Hold expiry uses `HOLD_DURATION_SECONDS`, default 600.
- [ ] Confirm succeeds only for owner and valid unexpired HELD record.
- [ ] Confirm is idempotent; confirmed count changes once.
- [ ] Student cannot create another active/confirmed application for same class.
- [ ] Cancel is owner-checked and releases a held seat.
- [ ] At least one automated concurrency test proves no overbooking.

## 4. React UI
- [ ] Admin can create/list classes from UI.
- [ ] Student/guardian can identify themselves with student ID.
- [ ] Class list shows total, held, confirmed, available seats.
- [ ] Student can request a hold.
- [ ] Current hold status and expiry are visible.
- [ ] Confirm/pay mock fee action works only while HELD and not expired.
- [ ] Student can cancel a hold.
- [ ] Student can view their applications/history.
- [ ] Student identity persists across refresh/browser reopen.
- [ ] Seat counts update without page reload.
- [ ] Friendly loading/empty/error/full/expired/server-down states.

## 5. Docker acceptance
- [ ] Root `docker-compose.yml` builds frontend and backend and starts PostgreSQL.
- [ ] Backend host URL is `http://localhost:8080`.
- [ ] Frontend is reachable on documented host port.
- [ ] Browser frontend can call backend correctly.
- [ ] DB uses named persistent volume.
- [ ] `docker compose down` then `docker compose up --build` retains data.
- [ ] No local .NET/Node/PostgreSQL installation required for evaluator.

## 6. Submission
- [ ] `README.md` has exact setup, URLs, env vars, demo steps.
- [ ] `ASSUMPTIONS.md` records interpretations and rationale.
- [ ] `AI_USAGE.md` honestly documents AI use and human review.
- [ ] `TESTING.md` lists commands and actual results.
- [ ] `PROJECT_STATE.md` reflects final state.
- [ ] Run all tests and Docker smoke test.
- [ ] Check `git status`; no secrets, `.env`, or generated artifacts.
- [ ] Create public GitHub repository.
- [ ] Push all commits and verify repository is public and accessible.
