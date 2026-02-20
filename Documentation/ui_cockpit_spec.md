# UI Cockpit Specification — PhillOps (Telegram-First)

Purpose: Define a GUI-based operations cockpit that reduces terminal dependence while keeping Telegram as the primary chat/control surface in early phases.

Status: Approved for phased implementation.
Open Items / Remaining Work:
- Confirm frontend stack and backend-for-frontend (BFF) stack.
- Implement v0 without embedded chat panel.

---

## 1) Product decision

Current decision:
- Keep Telegram as primary chat interface.
- Do not ship embedded UI chat in v0.
- Add optional embedded chat in v1.5 only if operational need is proven.

Rationale:
- Reduces complexity while core runtime/memory stack is still stabilizing.
- Prevents duplicate channel behavior and drift between Telegram and UI.

Status: Locked.
Open Items / Remaining Work:
- Define criteria that justify enabling UI chat in v1.5.

---

## 2) Scope by phase

### v0 (now) — No embedded chat

Routes:
- `/` dashboard (health + timeline + quick actions)
- `/work` Kanban board
- `/incidents` incident management
- `/checks` health checks view

Must-have:
- Runtime health tiles (gateway, Telegram, Supabase).
- Events timeline.
- Kanban CRUD + lane movement.
- Incident list/detail with evidence links.
- Check statuses with timestamps.

### v1

- Memory operation visibility (pin/forget/source outcomes).
- Runbook buttons for common operator actions.
- Better evidence linking and filtering.

### v1.5 (optional)

- Embedded chat panel via BFF proxy.
- Session handling (`New Session`) without touching long-term memory.

Status: Approved.
Open Items / Remaining Work:
- Define exact acceptance gates for each phase.

---

## 3) Architectural constraints (non-negotiable)

- Frontend contains no secrets.
- Frontend never talks directly to provider APIs with privileged keys.
- Frontend talks only to BFF endpoints.
- BFF performs orchestration and adapter routing.
- Domain logic does not live in UI components.
- Persistence only through data adapters.

Status: Locked.
Open Items / Remaining Work:
- Add architecture stamp references once implementation starts.

---

## 4) Core UI objects

- `HealthTile`
- `EventTimelineItem`
- `EvidenceDrawer`
- `KanbanBoard`
- `WorkCard`
- `IncidentDetailPanel`
- `CheckStatusTable`
- `RunbookActionButton` (v1+)

Status: Drafted.
Open Items / Remaining Work:
- Define component props and interaction contracts.

---

## 5) Data model (v0)

Required tables/entities:
- `work_items`
- `events`
- `incidents`
- `checks`

Optional (later):
- `chat_messages`
- `chat_sessions`

Status: Drafted.
Open Items / Remaining Work:
- Confirm schema ownership and migration plan.

---

## 6) BFF endpoint contract (v0)

Health:
- `GET /api/health`

Events:
- `GET /api/events`
- `GET /api/events/:id`

Work:
- `GET /api/work-items`
- `POST /api/work-items`
- `PATCH /api/work-items/:id`
- `POST /api/work-items/:id/move`

Incidents:
- `GET /api/incidents`
- `POST /api/incidents`
- `PATCH /api/incidents/:id`

Checks:
- `GET /api/checks`

Optional later:
- `POST /api/chat/send`
- `POST /api/chat/command`

Status: Drafted.
Open Items / Remaining Work:
- Define response schemas and error envelopes.

---

## 7) Security and role behavior

- Operator role: can mutate work/incidents and execute approved runbooks.
- Non-operator roles: read-only in cockpit unless explicitly elevated.
- Every mutation writes an audit event.
- Secrets remain server-side only.

Status: Active policy.
Open Items / Remaining Work:
- Add role auth implementation details.

---

## 8) Acceptance criteria

v0 is complete when:
- Health tiles show meaningful status.
- Kanban board persists and survives refresh.
- Incidents and checks views work with real data.
- Events timeline captures operator actions.
- No secrets leak to frontend or public endpoints.

Status: Drafted.
Open Items / Remaining Work:
- Add concrete test IDs to `Documentation/test_plan.md`.

---

## 9) Deferred decisions

- Embedded chat in UI (deferred to v1.5).
- Full multi-user auth model (single-user acceptable in early phase).
- Real-time push vs polling for events/checks.

Status: Deferred by design.
Open Items / Remaining Work:
- Reassess after v0 stabilization.
