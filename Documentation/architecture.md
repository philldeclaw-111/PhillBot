# Architecture — PhillBot / Clawbot

Purpose: Records the system architecture and serves as the pre-implementation stamp for every major decision. Must be updated before feature work begins.

Status: Active; major baseline decisions implemented.
Open Items / Remaining Work:
- Finalize memory command execution path (`/pin`, `/forget`, `/source`).
- Add non-AI watchdog + alert execution path.
- Complete release-gate implementation from testing architecture.

---

## Status

Partially implemented and validated.

---

## Open Items / Remaining Work

- Finalize GitHub integration command contract and least-privilege validation.
- Formalize Option B memory write/read pipeline from runtime to Supabase.
- Add explicit rollback references for token mismatch and bot token rotation.

---

## System Overview

- **User** interacts via Telegram (`@PhillDeclaw_bot`) as the primary control surface.
- **Droplet (phill)** runs OpenClaw via Docker Compose; gateway validated and responding.
- **Memory layer** runs externally on Supabase (`memory.entries` in schema `memory`) with vector extension enabled.
- **Runtime env injection** passes `SUPABASE_URL`, `SUPABASE_SERVICE_ROLE_KEY`, `GH_TOKEN`, and `GITHUB_TOKEN` into gateway and CLI services via compose override.
- **Testing architecture** follows dark-factory role separation and thin-twin layering defined in `Documentation/testing_architecture.md`.

Status: Implemented for core path; pending memory command integration.
Open Items / Remaining Work:
- Add sequence diagram for Telegram -> gateway -> memory write/read.
- Add explicit failure-state transitions for provider outage and token mismatch.

---

## Architecture Stamp (Required for Each Major Decision)

Use this template for each major architectural decision.

**Decision Name:**  
**Status:** Drafted / Approved / Implemented  
**Open Items / Remaining Work:**  

**Option Matrix (Minimal / Middle / Max):**
- Minimal:
- Middle (selected):
- Max:

**Design Choice:**  
**Why lesser options were rejected:**  
**Scalability / memory / latency headroom:**  
**Back-end or system impacts:**  
**Testing expectations:**  

---

## Baseline Runtime (Docker / OpenClaw)

- **Decision:** Use OpenClaw upstream Docker workflow as baseline runtime.
- **Rationale:** Aligns with upstream runtime assumptions (Node 22+), reduces drift, and speeds onboarding.

Status: Implemented (gateway running).
Open Items / Remaining Work:
- Validate persistent upgrade path and rollback baseline.
- Capture image/build identifiers in baseline docs.

---

## Subsystems

### 1) Identity & access

- Bot’s own Gmail, GitHub, prepaid card; Telegram token on droplet. Separate API accounts for bot vs user (Cursor).
- Status: Implemented for core auth and Telegram pairing.
- Open Items / Remaining Work: Finalize key rotation runbook and branch protection automation.

### 2) Infrastructure (droplet, Tailscale, Docker)

- Droplet phill; Tailscale-only SSH; Docker + Compose; `~/clawbot`.
- Status: Implemented; validated in active run.
- Open Items / Remaining Work: Record automated provisioning scripts and rollback snapshots.

### 3) Bot runtime (Telegram, teaching, scheduling)

- Telegram gateway path is active and `/status` validated.
- Status: Partially implemented.
- Open Items / Remaining Work: Implement memory commands and scheduled reminder behavior.

### 4) CI / deployment

- PR-based; staging then prod; optional GitHub Actions.
- Status: Planned.
- Open Items / Remaining Work: Implement PR-only merge flow and scenario gate checks.

---

## Data Flow and Interfaces

- User -> Telegram -> OpenClaw gateway -> model provider (OpenAI) -> response.
- Memory path (target): Gateway command handlers -> Supabase (`memory.entries`) for write/read operations.
- Control path: Tailscale SSH for ops + docker compose lifecycle.
- Status: Core data path implemented; memory path pending command integration.
- Open Items / Remaining Work: define command-level schemas and idempotency behavior.

---

## Security and Privacy

- Bot credentials stored in droplet env files with strict file permissions.
- Telegram token rotated after exposure event and re-applied successfully.
- Local CLI device-token mismatch observed for health command path; Telegram runtime unaffected.
- Status: Active with known operational caveat.
- Open Items / Remaining Work: formalize secret rotation cadence and CLI token resync procedure.

---

## Testing Architecture Linkage

Testing architecture for this system is defined in `Documentation/testing_architecture.md`.

Status: Approved.
Open Items / Remaining Work:
- Wire scenario-run outputs into update log and test plan evidence sections.

---

## Exception Protocol

If any architecture decision must be deferred: document with owner and target date; list in status tracker and update log.

Status: Drafted; enforcement required.
Open Items / Remaining Work: None beyond applying the rule.
