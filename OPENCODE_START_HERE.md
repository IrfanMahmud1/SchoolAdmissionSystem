# OpenCode Initial Workflow

## Before launching
1. Open PowerShell.
2. Run:
   ```powershell
   New-Item -ItemType Directory -Force D:\SchoolAdmission | Out-Null
   Set-Location D:\SchoolAdmission
   ```
3. Copy all starter-pack files into `D:\SchoolAdmission`.
4. Copy the original assignment PDF into this directory and name it `ASSIGNMENT.pdf`.
5. Check:
   ```powershell
   docker version
   docker compose version
   git --version
   opencode --version
   ```
   If `docker version` cannot reach the server, start Docker Desktop and retry.
6. Start OpenCode from this directory:
   ```powershell
   opencode
   ```

## Prompt 1 — inspect and plan (send first)
Read `AGENTS.md`, `PROJECT_STATE.md`, `TASKS.md`, and `ASSIGNMENT.pdf` completely. Do not write application code yet. Extract every required API route, request/response schema, status code, UI requirement, environment variable, and acceptance criterion into a concise `REQUIREMENTS.md`. Compare it against `TASKS.md`; correct the checklist if needed. Inspect the available environment and report exact Docker/Git/.NET/Node/OpenCode versions and any blockers. Then propose the smallest complete architecture and a time-boxed implementation order for the deadline tonight. Record assumptions in `ASSUMPTIONS.md` and decisions in `DECISIONS.md`. Update `PROJECT_STATE.md`. Do not spend time on optional features.

## Prompt 2 — implement autonomously (after plan)
Proceed autonomously using the agreed plan and `AGENTS.md`. Build the complete minimum viable submission. Work milestone by milestone. After each milestone run the relevant build/tests, update `TASKS.md` and `PROJECT_STATE.md`, and create a meaningful Git commit. Prioritize:
1. concurrency-safe PostgreSQL hold allocation and idempotent confirmation;
2. exact required API contract;
3. React UI for admin and student/guardian;
4. Docker Compose startup and persistence;
5. automated concurrency test;
6. README and honest AI disclosure.
Do not stop after generating a plan. Continue implementation unless blocked by a concrete environment or requirement issue. Do not invent successful test results.

## Prompt 3 — recovery after context/session ends
Read `AGENTS.md`, `PROJECT_STATE.md`, `TASKS.md`, `DECISIONS.md`, and the latest Git log. Inspect the working tree and run the smallest relevant verification command. Continue from the exact `Next action` in `PROJECT_STATE.md`. Do not redo completed work unless verification reveals it is broken. Update the state file before ending the session.

## Prompt 4 — final audit (reserve time for this)
Stop adding features. Inspect the entire repo and compare it to `REQUIREMENTS.md` and `TASKS.md`. Run backend tests, frontend build, and `docker compose up --build`. Verify all critical workflows, concurrency, expiry, idempotent confirm, and persistence after down/up. Fix critical defects only. Update `README.md`, `TESTING.md`, `AI_USAGE.md`, `ASSUMPTIONS.md`, and `PROJECT_STATE.md` with truthful results. Check `git status`, ensure no `.env`/secrets are committed, commit final changes, push to the public GitHub repository, and verify the remote URL and public visibility.

## Important
If OpenCode starts consuming too much context, ask it to checkpoint immediately:
"Stop implementation now. Update PROJECT_STATE.md and TASKS.md with exact completed work, actual verification results, modified files, known blockers, latest commit, and the next exact command/action. Do not claim unverified work."
