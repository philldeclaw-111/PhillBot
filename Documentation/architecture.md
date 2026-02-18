# Architecture — PhillBot / Clawbot

Purpose: Records the system architecture and serves as the pre-implementation stamp for every major decision. Must be updated before feature work begins.

Status: Drafted; customized for PhillBot.
Open Items / Remaining Work:
- Replace placeholders with project-specific architecture decisions.
- Ensure every major subsystem has Status + Open Items.
- Add an architecture stamp for each major decision.

---

## Status

Drafted; not validated.

---

## Open Items / Remaining Work

- Confirm baseline compatibility (Docker, base image, `gh`).
- Finalize system boundaries and data flows (Telegram ↔ bot ↔ APIs).
- Add architecture stamps for baseline image and bot runtime.

---

## System Overview

- **User** interacts via Telegram (and optionally other channels). User’s Telegram account owns the bot token; token and config live on the droplet only.
- **Droplet (phill)** runs Docker Compose; bot runs in a container built from a baseline image that includes runtime + GitHub CLI; secrets (Telegram token, API keys, `GH_TOKEN`) supplied at runtime.
- **Bot** uses its own GitHub (and Gmail, Make.com, Anthropic/OpenAI as needed) for automation, teaching pipeline, and scheduled messaging. No bot secrets in Cursor or on the user’s machine.

Status: Drafted; pending validation.
Open Items / Remaining Work:
- Add a simple diagram or narrative of data flow (user → Telegram → bot → APIs).
- Identify critical paths and failure modes.

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

## Baseline Image (Docker)

- **Decision:** GitHub CLI and shared tools live inside the Docker image; auth and secrets at runtime only.
- **Rationale:** Every instance of the bot has the same capabilities; baseline is copyable and repeatable.

Status: Drafted; not implemented.
Open Items / Remaining Work:
- Add full architecture stamp and option matrix for baseline image.
- Define base image (e.g. Node/Python) and install steps for `gh`.

---

## Subsystems

### 1) Identity & access

- Bot’s own Gmail, GitHub, prepaid card; Telegram token on droplet. Separate API accounts for bot vs user (Cursor).
- Status: Drafted; identity created by user; not yet wired into automation.
- Open Items / Remaining Work: Wire bot’s GitHub auth into droplet/container; never expose in Cursor.

### 2) Infrastructure (droplet, Tailscale, Docker)

- Droplet phill; Tailscale-only SSH; Docker + Compose; `~/clawbot`.
- Status: Drafted; infra in place per conversation.
- Open Items / Remaining Work: Clone repo; add baseline image build; document rollback.

### 3) Bot runtime (Telegram, teaching, scheduling)

- To be detailed once repo and stack are fixed.
- Status: Drafted; not implemented.
- Open Items / Remaining Work: Define modules (Telegram handler, teaching pipeline, scheduler); add stamps.

### 4) CI / deployment

- PR-based; staging then prod; optional GitHub Actions.
- Status: Drafted; not implemented.
- Open Items / Remaining Work: Define workflow and gates.

---

## Data Flow and Interfaces

- User → Telegram → bot (container) → Anthropic/OpenAI, Make.com, GitHub as needed. PDFs/sources via Drive or provided inputs.
- Status: Drafted; not validated.
- Open Items / Remaining Work: Define interface boundaries; document error handling and retries.

---

## Security and Privacy

- Bot credentials only on droplet/container; separate accounts for blast-radius containment. No bot API keys in Cursor.
- Status: Drafted; principles locked.
- Open Items / Remaining Work: Document token storage and rotation expectations.

---

## Exception Protocol

If any architecture decision must be deferred: document with owner and target date; list in status tracker and update log.

Status: Drafted; enforcement required.
Open Items / Remaining Work: None beyond applying the rule.
