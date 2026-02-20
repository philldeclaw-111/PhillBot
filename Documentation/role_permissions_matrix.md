# Role Permissions Matrix — PhillBot

Purpose: Define role-based tooling access, secret scope, and abstraction boundaries so the system remains modular, secure, and evolvable.

Status: Active baseline.
Open Items / Remaining Work:
- Enforce this matrix in runtime config and repository permissions.
- Add CI checks that verify role constraints are not violated.

---

## 1) Core Abstraction Rule (Non-Negotiable)

Business logic must remain independent from interfaces and infrastructure.

Required boundaries:
- Interface Layer (Telegram, webhooks, CLI surface) -> no direct data/storage logic.
- Orchestration Layer (workflows, policy, command routing) -> no UI/channel SDK calls.
- Domain Layer (business rules and decisions) -> no provider SDKs, no SQL inline.
- Data Layer (Supabase/DB adapter) -> no domain branching logic.
- Integration Layer (OpenAI/GitHub/other APIs) -> no domain state mutation without orchestration.

Status: Locked.
Open Items / Remaining Work:
- Validate current code path against these boundaries before adding new features.

---

## 2) Role Definitions

### Runtime Bot (user-facing)
- Purpose: receive commands and produce user responses.
- Allowed: Interface + orchestration + read/write memory via adapters.
- Disallowed: direct schema migration, repo admin, host-level ops.

### Librarian Role (internal for now, separate bot later)
- Purpose: memory curation (classify, dedupe, summarize, retention ops).
- Allowed: memory write/read operations via data layer.
- Disallowed: deployment actions, privileged host actions.

### Builder Role
- Purpose: implementation and patching.
- Allowed: code edits, tests, branch creation, PR creation.
- Disallowed: direct production deploy, hidden examiner artifacts.

### Examiner Role
- Purpose: scenario generation and execution orchestration.
- Allowed: scenario packs, seeds, chaos controls.
- Disallowed: code edits, production secret access.

### Judge Role
- Purpose: invariant scoring and severity assignment.
- Allowed: read logs/results, publish verdict artifacts.
- Disallowed: scenario generation, code edits, deployment.

### Operator (human)
- Purpose: final approval, incident control, token rotation.
- Allowed: all with discipline and least-privilege day-to-day practice.

Status: Approved.
Open Items / Remaining Work:
- Add concrete account mapping for each role in secrets manager.

---

## 3) Tooling and Permission Matrix

| Role | Telegram | Supabase | OpenAI | GitHub | Docker/Host | CI/Deploy |
|---|---|---|---|---|---|---|
| Runtime Bot | Send/receive in approved chats only | `memory_rw` (table-level) | Model invoke only | Branch+PR only (no admin/no main push) | Restart own service only | Trigger approved pipelines only |
| Librarian | Optional internal only | `memory_rw` + retention procedures | Summarization/classification only | None (or read-only docs) | None | None |
| Builder | None | Staging/test DB only | Optional for test harness | Branch, commit, PR write | Local dev containers | No prod deploy |
| Examiner | None | Test fixtures read/write only | Optional scenario generation | Read-only (or artifact upload only) | Chaos test harness only | No prod deploy |
| Judge | None | Read-only results schema | None required | PR comments/checks only | Read-only logs | Gate verdict publish only |
| Operator | Full controlled access | Admin for migrations/rotations | Account owner controls | Admin owner | Full | Final go/no-go |

Status: Drafted.
Open Items / Remaining Work:
- Enforce role-specific DB keys and GitHub token scopes.

---

## 4) Secret Scope by Role

Runtime bot required secrets:
- `OPENAI_*` auth token(s)
- `SUPABASE_URL`
- `SUPABASE_SERVICE_ROLE_KEY` (temporary for bootstrap; move to reduced-scope DB role key)
- `GH_TOKEN` / `GITHUB_TOKEN` (fine-grained repo-scoped)
- `TELEGRAM_BOT_TOKEN`

Librarian (future separate process):
- `SUPABASE_URL`
- `SUPABASE_MEMORY_RW_KEY`
- optional model token for summarization only

Builder:
- No production secrets.
- Staging-only keys if needed.

Examiner/Judge:
- No production secrets.
- Read-only artifact access where possible.

Status: Active.
Open Items / Remaining Work:
- Replace broad Supabase service key usage with least-privilege runtime key.

---

## 5) GitHub Permissions Policy

Required:
- Fine-grained PAT scoped to one repository.
- `contents: read/write` and `pull_requests: read/write` only (if PR automation is needed).
- No admin permissions.
- Branch protection on `main` (PR required, no force push).

Status: Active.
Open Items / Remaining Work:
- Verify branch protection and required checks are enabled.

---

## 6) Database Permissions Policy

Current:
- Supabase RLS enabled.
- Runtime uses secret/service key for bootstrap integration.

Target:
- Create dedicated DB roles:
  - `memory_rw` for runtime/librarian
  - `memory_ro` for judge/reporting
- Reserve service/admin key for migrations and emergencies only.

Status: In progress.
Open Items / Remaining Work:
- Implement role-scoped key transition.
- Add policy tests for denied operations.

---

## 7) Mode-Based Permission Shifts

### Build Mode
- Builder elevated for code only.
- Runtime production secrets blocked from builder.

### Validate Mode
- Examiner/Judge elevated for scenarios and scoring.
- Builder locked to bugfix branch only.

### Operate Mode
- Runtime active with least privilege.
- Builder/Examiner/Judge mostly read-only for production.

Status: Drafted.
Open Items / Remaining Work:
- Add automatic mode toggles in pipeline/runbook.

---

## 8) Required Guardrails

- No direct domain writes from interface handlers.
- No direct SQL in command handlers.
- All persistence via data adapters.
- All provider calls through integration adapters.
- Every security-sensitive action requires explicit logging with actor role.

Status: Locked.
Open Items / Remaining Work:
- Add static checks/lint rules where possible.

---

## 9) Validation Checklist for This Matrix

- [ ] Runtime cannot push directly to `main`.
- [ ] Runtime can create branch + PR only.
- [ ] Builder has no production secrets.
- [ ] Examiner cannot edit source.
- [ ] Judge cannot execute deploy actions.
- [ ] DB role separation implemented and verified.
- [ ] Abstraction boundaries validated by code review checklist.

Status: Drafted.
Open Items / Remaining Work:
- Run this checklist at each milestone close.

---

## 10) Evidence Expectations

For each role-change or permission change, log:
- What changed
- Why it changed
- Validation command/results
- Residual risk

Record in:
- `Documentation/update_log.md`
- `Documentation/status_tracker.md`
- `Documentation/test_plan.md` (if behavior is affected)

Status: Locked.
Open Items / Remaining Work:
- None.
