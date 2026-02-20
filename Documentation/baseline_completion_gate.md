# Baseline Completion Gate — PhillBot

Purpose: Define the minimum, unambiguous execution lane for the next functional milestone. This gate prioritizes safety and speed while preventing scope drift.

Status: Active; ready for execution.
Open Items / Remaining Work:
- Execute each step in order and attach evidence in `Documentation/test_plan.md` and `Documentation/update_log.md`.
- Produce final release gate report at the end of this sequence.

---

## Target End-State for This Gate

- Runtime uses least-privilege DB credentials for memory operations.
- `/pin`, `/forget`, and `/source` work end-to-end in Telegram/runtime path.
- Runtime GitHub path is validated with branch + PR-only scope.
- Non-AI watchdog alerting works when model/auth paths fail.

Status: Defined.
Open Items / Remaining Work:
- Implementation and validation pending.

---

## Single Execution Lane (No Parallel Build-Out)

Do not parallelize AgentOS/UI/memory implementation streams for this milestone.
Execute one lane in this exact order:

1. Minimum Baseline Gate
2. Least-Privilege Runtime Cutover
3. Memory Commands v1
4. GitHub Runtime Validation
5. Non-AI Watchdog Path
6. Validation Pass + Release Gate Report

Status: Locked.
Open Items / Remaining Work:
- Team/session discipline must maintain this order.

---

## Step 1 — Minimum Baseline Gate

Scope:
- Create AgentOS skeleton and role contract files required for execution.
- Confirm abstraction boundaries are acknowledged in `Documentation/component_library.md`.
- Keep this minimal and actionable; do not expand into framework perfection.

Done means (binary):
- Required files exist and map to executable constraints.
- Constraints are referenced by implementation work items.

Rollback checkpoint:
- Record known-good compose state (`docker compose ps`) and config fingerprint notes before continuing.

Status: Pass (minimum gate met).
Open Items / Remaining Work:
- Add CI/static enforcement for boundary checks in a later milestone.

---

## Step 2 — Least-Privilege Runtime Cutover

Scope:
- Switch runtime memory path to scoped DB role credentials.
- Remove broad service-role assumptions from normal runtime command path.

Done means (binary):
- Runtime read/write calls succeed using scoped role credentials.
- Permission model matches documented role boundaries.

Rollback checkpoint:
- Capture pre-cutover env/config snapshot metadata and compose status.

Status: Pending (provisional env checks complete; behavior-path proof pending).
Open Items / Remaining Work:
- Runtime env presence checks passed, but final proof of least-privilege behavior must follow active backend path in runtime codebase.
- Reconcile runtime backend reality (QMD-focused build) with Supabase-targeted gate language, then close with explicit evidence.

---

## Step 3 — Memory Commands v1

Scope:
- Implement `/pin`, `/forget`, `/source` against least-privilege runtime path.
- Validate end-to-end behavior from Telegram command to DB effect.

Done means (binary):
- All three commands succeed in runtime with expected persisted/query behavior.
- Evidence recorded in `Documentation/test_plan.md`.

Rollback checkpoint:
- Capture known-good runtime logs and command outcomes before moving to next step.

Status: Pending (implementation in progress).
Open Items / Remaining Work:
- Implementation attempt started; finalize runtime file edits and compile/Telegram smoke validation in runtime workspace.

---

## Step 4 — GitHub Runtime Validation

Scope:
- Validate runtime token behavior for branch + PR-only operations.
- Confirm least-privilege constraints are sufficient for required automation path.

Done means (binary):
- At least one successful runtime-authenticated operation is logged with evidence.
- Behavior is confirmed to stay within branch + PR-only boundaries.

Rollback checkpoint:
- Save command outputs and current auth status evidence.

Status: Pending.
Open Items / Remaining Work:
- Authenticated runtime operation test pending.

---

## Step 5 — Non-AI Watchdog Path

Scope:
- Implement alert path independent of model/provider response path.
- Ensure auth/model outages still produce operator-visible signal.

Done means (binary):
- Simulated model/auth failure triggers an operator-visible alert.
- Incident handling notes are captured in docs.

Rollback checkpoint:
- Preserve pre-watchdog config state and alert route settings.

Status: Pending.
Open Items / Remaining Work:
- Watchdog implementation and simulated failure test pending.

---

## Step 6 — Validation Pass and Release Gate Report

Scope:
- Run required milestone suites and update status/test/update logs.
- Produce a short release gate report with pass/pending risks.

Done means (binary):
- Required suites for this milestone are marked Pass/Fail/Pending with dated evidence.
- Release gate report is produced and linked.

Rollback checkpoint:
- Record final known-good compose state and config fingerprint notes.

Status: Pending.
Open Items / Remaining Work:
- Validation execution and reporting pending.

---

## Explicit Deferred Queue (Not Blockers for This Milestone)

- Embedded UI chat in cockpit.
- Secondary alert channel (Telegram remains primary for now).
- Full Option C memory governance transition.

Status: Deferred by design.
Open Items / Remaining Work:
- Re-plan after this baseline completion gate closes.
