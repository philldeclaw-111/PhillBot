# Status Tracker — PhillBot (Runtime Context Copy)

Status: Active.
Open Items / Remaining Work:
- Add new dated evidence links whenever smoke tests are re-run.

## OpenClaw runtime baseline

Completion: ~86%
Last validation date: 2026-02-20
Blocking tasks / owners / target dates:
- Step 5 non-AI watchdog path. Owner: AI. Date: TBD.
Latest test evidence or logs:
- Runtime recreate succeeded (`docker compose ... up -d --force-recreate`).
- Gateway startup logs show no immediate crash stack traces after recreate.
- Runtime-authenticated GitHub branch/PR probes succeeded; admin-boundary probes returned 403.

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

Completion: ~100%
Last validation date: 2026-02-20
Blocking tasks / owners / target dates:
- Keep token policy reviewed during key rotation; preserve branch/PR-focused runtime path. Owner: Jon + AI. Date: ongoing.
Latest test evidence or logs:
- `GH_TOKEN` present in runtime.
- Probe branch lifecycle validated from runtime token:
  - create ref `201`, PR create attempt `422` (authorized but no commits), delete ref `204`.
- Admin-boundary endpoints denied (`hooks` and `branch protection` both `403`).
