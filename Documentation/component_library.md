# Component Library — PhillBot

Purpose: Define reusable modules and enforce abstraction boundaries so interface changes do not break domain logic, and data/integration changes do not leak into user-facing code.

Status: Active baseline.
Open Items / Remaining Work:
- Convert this from design contract to code-level enforcement (lint + review checklist).
- Add concrete module paths once implementation files are created.

---

## Abstraction Layers (required)

- **Interface layer** — Telegram/webhook/CLI input-output only.
- **Orchestration layer** — command routing, workflow sequencing, policy application.
- **Domain layer** — business rules, invariants, role behaviors.
- **Data layer** — Supabase persistence adapters and queries.
- **Integration layer** — OpenAI/GitHub/provider API adapters.

Status: Locked.
Open Items / Remaining Work:
- Map each implementation file to one primary layer.

---

## Forbidden Dependency Rules (non-negotiable)

- Interface modules must not call SQL/Supabase directly.
- Interface modules must not call OpenAI/GitHub adapters directly.
- Domain modules must not import Telegram SDKs or provider SDKs.
- Data modules must not include command parsing/business branching.
- Integration modules must not mutate domain state directly.
- Orchestration is the only layer allowed to coordinate cross-layer calls.

Status: Locked.
Open Items / Remaining Work:
- Add CI/static checks for forbidden imports when code paths exist.

---

## Component Contracts

### 1) Telegram Interface Adapter

**Layer:** Interface  
**Status:** Partially implemented (runtime operational, command surface evolving).  
**Open Items / Remaining Work:**
- Implement explicit command map for `/status`, `/pin`, `/forget`, `/source`.
- Add standardized error envelope for unauthorized, pairing required, and runtime failure.

**Purpose:** Receive Telegram updates and map them to orchestration commands.  
**Inputs/Props:** Telegram updates (message text, sender metadata, chat context).  
**Outputs/Events:** Normalized command event to orchestration layer; formatted response payloads.  
**States:** paired/unpaired, authorized/unauthorized, healthy/degraded.  
**Usage Rules:** No direct DB or provider API calls.

### 2) Command Router / Orchestrator

**Layer:** Orchestration  
**Status:** In progress.  
**Open Items / Remaining Work:**
- Route `/pin`, `/forget`, `/source` to memory service.
- Route admin-only commands through explicit authorization guard.

**Purpose:** Central dispatcher and policy gate between interface and domain services.  
**Inputs/Props:** Normalized command events.  
**Outputs/Events:** Calls to domain services and integration adapters; response contracts back to interface.  
**States:** command queued/running/failed/retried.  
**Usage Rules:** May coordinate layers, but cannot own storage schema or provider-specific logic.

### 3) Memory Service

**Layer:** Domain  
**Status:** Planned; backend initialized.  
**Open Items / Remaining Work:**
- Implement semantics for `/pin`, `/forget`, `/source`.
- Implement idempotency and source attribution rules.

**Purpose:** Own memory behavior semantics (profile/episodic/knowledge/ops).  
**Inputs/Props:** domain memory commands and payloads.  
**Outputs/Events:** memory write/read requests to data adapter, normalized memory results.  
**States:** write accepted/rejected, retrieval ranked/unranked, soft-deleted/active.  
**Usage Rules:** No SQL/provider SDK imports directly.

### 4) Supabase Memory Adapter

**Layer:** Data  
**Status:** Partially implemented (schema live, env injected).  
**Open Items / Remaining Work:**
- Add production-safe query wrapper + retry/backoff.
- Add migration policy and query observability hooks.

**Purpose:** Execute persistence operations against `memory.entries`.  
**Inputs/Props:** storage operations from memory domain service.  
**Outputs/Events:** query results/errors with typed adapter responses.  
**States:** connected/disconnected, migration level, policy pass/fail.  
**Usage Rules:** No command parsing, no Telegram/OpenAI behavior branching.

### 5) Model Provider Adapter (OpenAI)

**Layer:** Integration  
**Status:** Operational for runtime chat path.  
**Open Items / Remaining Work:**
- Add explicit fallback/error envelope for provider failures.
- Add usage/latency accounting adapter outputs.

**Purpose:** Encapsulate OpenAI API calls and response normalization.  
**Inputs/Props:** model prompt payloads from orchestration/domain.  
**Outputs/Events:** normalized model response/error objects.  
**States:** available/rate-limited/failure/unauthorized.  
**Usage Rules:** Must not write DB or mutate domain state directly.

### 6) GitHub Automation Adapter

**Layer:** Integration  
**Status:** Env-connected; behavior tests pending.  
**Open Items / Remaining Work:**
- Validate authenticated operation from runtime.
- Enforce branch+PR-only actions (no direct main push).

**Purpose:** Encapsulate `gh` or GitHub API operations under least privilege.  
**Inputs/Props:** requested git workflow actions from orchestration layer.  
**Outputs/Events:** operation status + links/artifacts.  
**States:** authenticated/unauthenticated, authorized/denied, success/failure.  
**Usage Rules:** No domain decisioning; no admin-level repo operations.

### 7) Scheduler and Alerting Service

**Layer:** Domain + Orchestration boundary  
**Status:** Planned.  
**Open Items / Remaining Work:**
- Implement daily/weekly schedule triggers.
- Implement non-AI failure alerting path.

**Purpose:** Time-based workflows and operational alerts independent of model response success.  
**Inputs/Props:** schedule definitions and runtime health events.  
**Outputs/Events:** dispatch notifications/commands through interface adapter.  
**States:** scheduled/running/missed/recovered.  
**Usage Rules:** Must operate when model provider is unavailable.

---

## Cross-Cutting Component Standards

- Every component must declare:
  - status,
  - open items,
  - input/output contracts,
  - failure behavior.
- Every component must include at least one test artifact in `test_plan.md`.
- Any cross-layer shortcut requires architecture stamp approval first.

Status: Locked.
Open Items / Remaining Work:
- Add component IDs and link them to scenario tests in `testing_architecture.md`.
