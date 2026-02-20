# Test Plan — PhillBot (Runtime Context Copy)

Status: Active; updated for Step 2 and Step 3 execution evidence.
Open Items / Remaining Work:
- Add fresh interactive Telegram smoke rerun evidence in the next operator session.

## Baseline Completion Gate Checklist

### Step 2 — Least-Privilege Runtime Cutover

Status: Pass (active backend interpretation)
Date: 2026-02-20
Validation:
- Codepath check shows memory backend resolves to non-Supabase runtime memory flow:
  - `src/memory/backend-config.ts` defaults `DEFAULT_BACKEND` to `builtin`.
  - `src` search did not surface active Supabase memory adapter usage in command handling.
- Runtime path evidence (active backend write/read substrate):
  - Workspace root resolved from `.env`: `OPENCLAW_WORKSPACE_DIR=/home/claw/.openclaw/workspace`.
  - Memory artifact present: `/home/claw/.openclaw/workspace/memory/2026-02-20.md`.
  - Git-backed memory writes/forgets verified:
    - `a10791b Pin smoke-memory-entry` (`1 insertion`)
    - `0ae06ac Forget smoke-memory-entry` (`1 deletion`)

Evidence:
- `git -C /home/claw/.openclaw/workspace log --oneline -n 5`
- `git -C /home/claw/.openclaw/workspace show --stat --oneline a10791b`
- `git -C /home/claw/.openclaw/workspace show --stat --oneline 0ae06ac`

Notes:
- Supabase env presence was already validated previously, but Step 2 closure here is based on the currently active runtime memory path (workspace/git-backed), not env presence alone.

### Step 3 — Memory Commands v1

Status: Pass (current runtime evidence)
Date: 2026-02-20
Validation:
- Runtime command wiring still present:
  - `commands-core.ts` includes `handleMemoryCommands` once.
  - Registry contains `pin`, `forget`, `source`.
  - `commands-memory.ts` contains handlers for all three commands.
- Telegram smoke evidence (captured) confirms:
  - `/pin` persisted entry.
  - `/source` returned source.
  - `/forget` removed entry.
  - follow-up `/source` reported removal.
- Runtime substrate corroborates outcome via memory workspace commit history.

Evidence:
- Screenshot evidence: `assets/IMG_9018-46e87e67-bdf9-4e54-ace6-7a7d8381effa.png`
- `rg` checks in `src/auto-reply` for handler/registry presence
- memory workspace git commit evidence listed above

### Step 4 — GitHub Runtime Validation

Status: Pass
Date: 2026-02-20
Validation:
- Runtime token presence confirmed in running gateway container (`GH_TOKEN_SET`).
- Authenticated runtime PR-read operation:
  - `GET /repos/philldeclaw-111/PhillBot/pulls?state=open&per_page=5` -> HTTP `200`.
- Branch write lifecycle proved (branch+PR lane):
  - `GET /repos/philldeclaw-111/PhillBot/git/ref/heads/main` -> `200` (main SHA resolved)
  - `POST /repos/philldeclaw-111/PhillBot/git/refs` -> `201` (probe branch created)
  - `POST /repos/philldeclaw-111/PhillBot/pulls` -> `422` with `"No commits between..."` (request authorized; expected validation failure)
  - `DELETE /repos/philldeclaw-111/PhillBot/git/refs/heads/<probe>` -> `204` (probe branch removed)
- Admin-boundary checks denied:
  - `GET /repos/philldeclaw-111/PhillBot/hooks` -> `403`
  - `GET /repos/philldeclaw-111/PhillBot/branches/main/protection` -> `403`

Evidence:
- `/tmp/step4_create_pr.json`
- `/tmp/step4_hooks.json`
- `/tmp/step4_branch_protection.json`

### Step 5 — Non-AI Watchdog Path

Status: Pending (implementation landed; runtime simulation pending)
Date: 2026-02-20
Validation:
- Added non-AI watchdog event emission on model/auth outages in:
  - `src/auto-reply/reply/agent-runner-execution.ts`
  - Emits system event with context key `watchdog:model-auth` when errors match auth/outage patterns (`401/403`, unauthorized, invalid api key, authentication/forbidden).
- Added regression test coverage in:
  - `src/auto-reply/reply/agent-runner.runreplyagent.test.ts`
  - Asserts watchdog event enqueue on `401 Unauthorized: invalid api key`.
- Lint check for edited files reports no lint errors.

Evidence:
- `git diff -- src/auto-reply/reply/agent-runner-execution.ts src/auto-reply/reply/agent-runner.runreplyagent.test.ts`
- IDE lint snapshot: no errors for both edited files

Pending closure steps:
- Run runtime simulation of model/auth outage and capture operator-visible alert evidence from system events/log path.
