# Operating Model — Maximalist Execution Guide

Purpose: Define a complete, repeatable operating model for PhillBot across build, test, release, and incident response.

Status: Active baseline.
Open Items / Remaining Work:
- Convert key manual procedures to scripted runbooks.
- Add CI automation for scenario gates and regression capture.

---

## 1) Operating Philosophy

- Build for durability, not demos.
- Treat every external dependency as unreliable by default.
- Preserve role independence (Builder / Examiner / Judge) to prevent overfitting.
- Keep secrets out of code and chat logs.
- Prefer deterministic replay over ad-hoc debugging.

Status: Locked.
Open Items / Remaining Work:
- None.

---

## 2) Release Modes

### Mode A — Build
- New features and refactors.
- Requires architecture stamp update before implementation.

### Mode B — Validate
- Scenario and invariant checks.
- No net-new feature work.

### Mode C — Stabilize
- Bugfixes and regression hardening only.
- Release gating focus.

### Mode D — Operate
- Production monitoring and incident response.
- Minimal config-only changes.

Status: Drafted.
Open Items / Remaining Work:
- Add explicit transitions and ownership between modes.

---

## 3) Role Separation Rules

- Builder cannot access hidden scenario generators.
- Examiner cannot edit production code.
- Judge cannot author code or scenario packs.
- Runtime operator can deploy only signed-off artifacts.

Status: Approved.
Open Items / Remaining Work:
- Enforce with automation and repository permissions.

---

## 4) Daily Operating Loop

1. Check runtime health (`docker compose ps`, recent logs).
2. Check channel health (Telegram command response).
3. Check memory health (basic Supabase read/write smoke).
4. Review open incidents and unresolved P1/P0 items.
5. Execute planned work in current mode.
6. Update docs (`status_tracker`, `update_log`, `test_plan`) before stop.

Status: Drafted.
Open Items / Remaining Work:
- Add command bundle script for each step.

---

## 5) Weekly Operating Loop

1. Dependency baseline review (Docker/runtime/provider changes).
2. Token/key rotation review and exposure checks.
3. Scenario seed pack refresh and replay audits.
4. Recovery drill (one rollback rehearsal).
5. Doc reconciliation checklist run.

Status: Drafted.
Open Items / Remaining Work:
- Add calendarized reminders and owner assignments.

---

## 6) Release Gate (Required)

Release is blocked unless all pass:

- P0 = zero
- P1 = zero or explicitly deferred with owner/date
- Telegram channel healthy
- Memory read/write healthy
- No unresolved secret exposure
- Latest update log + status tracker + test plan synchronized

Status: Approved.
Open Items / Remaining Work:
- Encode as machine-readable gate policy.

---

## 7) Incident Model

Incident severity:
- Sev-0: total outage / security compromise
- Sev-1: major function unavailable
- Sev-2: degraded behavior
- Sev-3: low-impact issue

Incident workflow:
1. Detect (logs/alerts/user report)
2. Contain (disable risky path)
3. Recover (rollback or patch)
4. Verify (test invariants)
5. Learn (add regression scenario + update docs)

Status: Drafted.
Open Items / Remaining Work:
- Add explicit pager/alert targets and escalation windows.

---

## 8) Secrets and Access Policy

- Store secrets only in runtime env files/secrets manager.
- Restrict env file permissions (`600`).
- Rotate compromised tokens immediately.
- Never paste secrets into logs/issues/chat.
- Use least-privilege provider keys and repo scopes.

Status: Active.
Open Items / Remaining Work:
- Add quarterly full rotation cadence.

---

## 9) Memory Governance (Option B -> C)

Current (Option B):
- Shared memory backend in Supabase.
- Single bot writes/reads memory.
- Manual commands manage key memory actions.

Future (Option C):
- Policy-based access tiers.
- Archival/retention windows.
- Audit log for memory mutations.
- Human approval path for sensitive writes.

Status: In progress.
Open Items / Remaining Work:
- Implement command handlers and retention policy.

---

## 10) Change Management Rules

- No direct `main` changes from autonomous paths.
- PR-based flow for non-trivial changes.
- Every bugfix adds a regression anchor.
- Every architecture change adds a stamp entry.

Status: Approved.
Open Items / Remaining Work:
- Enforce with branch protection and CI checks.

---

## 11) Minimal Command Deck (Operator)

- Start/refresh runtime:
  - `docker compose -f /home/claw/openclaw-upstream/docker-compose.yml -f /home/claw/openclaw-upstream/docker-compose.override.yml up -d`
- Check runtime:
  - `docker compose -f /home/claw/openclaw-upstream/docker-compose.yml -f /home/claw/openclaw-upstream/docker-compose.override.yml ps`
- Tail gateway logs:
  - `docker compose -f /home/claw/openclaw-upstream/docker-compose.yml -f /home/claw/openclaw-upstream/docker-compose.override.yml logs -f openclaw-gateway`
- Short recent logs:
  - `docker compose -f /home/claw/openclaw-upstream/docker-compose.yml -f /home/claw/openclaw-upstream/docker-compose.override.yml logs --since=2m openclaw-gateway`
- Context sync (local docs -> remote runtime repo):
  - One-time shell helper setup (local machine):
    - `cat <<'EOF' >> ~/.bashrc`
    - `syncctx() {`
    - `  rsync -av --delete /home/jonbold/PhillBot/Documentation/ claw@phill:/home/claw/openclaw-upstream/docs/phillbot-context/`
    - `}`
    - `EOF`
    - `source ~/.bashrc`
  - Daily command:
    - `syncctx`
  - Verify synced docs:
    - `ssh phill "ls -la /home/claw/openclaw-upstream/docs/phillbot-context"`

Status: Active.
Open Items / Remaining Work:
- Add memory smoke and GitHub smoke command entries.

---

## 12) Documentation Discipline

At milestone close, update:
- `Documentation/update_log.md`
- `Documentation/status_tracker.md`
- `Documentation/test_plan.md`
- `Documentation/handoff.md`

Status: Locked.
Open Items / Remaining Work:
- None.
