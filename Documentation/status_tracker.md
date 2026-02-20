# Status Tracker — PhillBot (Runtime Context Copy)

Status: Active.
Open Items / Remaining Work:
- Add new dated evidence links whenever smoke tests are re-run.

## OpenClaw runtime baseline

Completion: ~86%
Last validation date: 2026-02-20
Blocking tasks / owners / target dates:
- Reduce runtime GitHub token to branch+PR-only scope and re-validate Step 4. Owner: AI + Jon. Date: TBD.
- Step 5 non-AI watchdog path. Owner: AI. Date: TBD.
Latest test evidence or logs:
- Runtime recreate succeeded (`docker compose ... up -d --force-recreate`).
- Gateway startup logs show no immediate crash stack traces after recreate.
- Runtime-authenticated GitHub REST call succeeded (`pulls` endpoint returned HTTP 200), but repo permission probe reports admin-capable token.

## Memory platform (active runtime path)

Completion: ~74%
Last validation date: 2026-02-20
Blocking tasks / owners / target dates:
- Align docs language if/when runtime memory backend switches from workspace/git-backed flow to Supabase-backed flow.
- Add explicit backend-switch test case to gate checklist.
Latest test evidence or logs:
- Active runtime memory evidence points to workspace/git-backed flow:
  - `/home/claw/.openclaw/workspace/memory/2026-02-20.md`
  - Commit `a10791b` (pin write) and `0ae06ac` (forget delete)
- Supabase memory env vars exist in runtime, but active command-path proof collected here is workspace/git-backed.

## Bot runtime memory commands (`/pin`, `/forget`, `/source`)

Completion: ~90%
Last validation date: 2026-02-20
Blocking tasks / owners / target dates:
- Capture one new post-recreate live Telegram smoke run in this same session window.
Latest test evidence or logs:
- Telegram smoke evidence screenshot confirms pin/source/forget/source behavior.
- Code wiring remains intact in:
  - `src/auto-reply/reply/commands-core.ts`
  - `src/auto-reply/reply/commands-memory.ts`
  - `src/auto-reply/commands-registry.data.ts`

## GitHub runtime scope gate (Step 4)

Completion: ~60%
Last validation date: 2026-02-20
Blocking tasks / owners / target dates:
- Rotate/re-scope runtime token to remove admin/maintain-level permissions. Owner: Jon. Date: TBD.
- Re-run runtime proof with branch/PR-only scopes and capture updated evidence. Owner: AI + Jon. Date: TBD.
Latest test evidence or logs:
- `GH_TOKEN` present in runtime.
- Authenticated PR-list API request succeeded (HTTP 200).
- Repo metadata response shows `permissions.admin: true`, so gate target is not yet met.
