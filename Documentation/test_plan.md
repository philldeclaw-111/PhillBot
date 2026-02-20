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

Status: Pending (auth works; permissions exceed branch+PR-only target)
Date: 2026-02-20
Validation:
- Runtime token presence confirmed in running gateway container (`GH_TOKEN_SET`).
- Authenticated runtime API operation succeeded:
  - `GET /repos/philldeclaw-111/PhillBot/pulls?state=open&per_page=5` -> HTTP `200`
  - Response artifact copied to host: `/tmp/gh_prs.json` (`[]`)
- Repository permission probe shows current token is over-privileged for this gate target:
  - `GET /repos/philldeclaw-111/PhillBot` -> HTTP `200`
  - Response includes `permissions.admin: true` (also `maintain/push/triage/pull: true`)
- `gh` CLI is unavailable in both host and runtime containers (`gh: not found`), so REST API evidence was used.

Evidence:
- `docker compose ... exec openclaw-gateway sh -lc 'test -n \"$GH_TOKEN\" && echo GH_TOKEN_SET || echo GH_TOKEN_MISSING'`
- `docker compose ... exec openclaw-gateway sh -lc 'curl ... /repos/philldeclaw-111/PhillBot/pulls ...'` -> `200`
- `/tmp/gh_prs.json`
- `docker compose ... exec openclaw-gateway sh -lc 'curl ... /repos/philldeclaw-111/PhillBot'` -> `200`
- `/tmp/gh_repo.json` lines showing permissions:
  - `"admin": true`
  - `"maintain": true`
  - `"push": true`
