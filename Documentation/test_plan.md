# Test Plan — PhillBot

Purpose: Defines all test suites and manual checklists. Every suite must be marked Pass/Fail/Pending with dates and evidence links. No subsystem is “complete” unless its tests are Pass.

Status: Drafted; customized for PhillBot (bot/automation; no hardware).
Open Items / Remaining Work:
- Populate test suites for each subsystem.
- Add evidence links for each test run.

---

## Test Plan Rules (Mandatory)

Status: Locked.
Open Items / Remaining Work:
- None.

Rules:
- Each suite must be marked Pass / Fail / Pending.
- Pending entries must include the required steps to close.
- Do not delete Pending entries until evidence is captured.
- Every test requires an evidence link (log, screenshot, command output).
- Add stress scenarios for major subsystems.

---

## Access & connectivity

Status: Pending  
Date: TBD  
Test Session ID / Date: TBD  
Evidence Required: Logs or terminal output.

Checklist:
- Tailscale SSH from laptop to droplet (`tailscale ssh claw@phill`) works.
- Docker and Docker Compose run on droplet.
- Bot’s GitHub auth (PAT or SSH) allows clone/pull on droplet.

---

## Baseline image build & run

Status: Pending  
Date: TBD  
Test Session ID / Date: TBD  
Evidence Required: Build log; `docker run` or `docker compose up` output.

Checklist:
- Dockerfile builds without error.
- Image includes GitHub CLI (`gh --version` inside container).
- Container starts with runtime env (e.g. `GH_TOKEN`) and no secrets in image.

---

## Bot startup & config

Status: Pending  
Date: TBD  
Test Session ID / Date: TBD  
Evidence Required: Logs of `docker compose up` and first bot response.

Checklist:
- `docker compose up -d` succeeds.
- Bot process runs and reads `.env` (no secrets in repo).
- Container has network access (Telegram, APIs as needed).

---

## Telegram integration

Status: Pending  
Date: TBD  
Test Session ID / Date: TBD  
Evidence Required: Logs or screenshots of send/receive.

Checklist:
- Bot receives messages from user’s Telegram.
- Bot can send replies.
- Command parsing and dispatch (when implemented) behave as expected.

---

## Teaching / pipeline (when implemented)

Status: Pending  
Date: TBD  
Test Session ID / Date: TBD  
Evidence Required: Logs or sample outputs.

Checklist:
- Subject + inputs (e.g. PDFs) trigger teaching flow.
- Podcast-style or other outputs generated as designed.
- Errors and retries handled and logged.

---

## GitHub CLI from container (when implemented)

Status: Pending  
Date: TBD  
Test Session ID / Date: TBD  
Evidence Required: Logs of `gh` commands run from inside container.

Checklist:
- `gh auth status` or equivalent shows authenticated (via `GH_TOKEN`).
- Clone/pull or other `gh` operations succeed as needed by bot.

---

## Stress and stability

Status: Pending  
Date: TBD  
Test Session ID / Date: TBD  
Evidence Required: Soak run logs.

Checklist:
- Extended run without crash (e.g. 24h soak).
- Memory/CPU stable under normal load.
- Network loss and reconnection handled.

---

## Security and secrets

Status: Pending  
Date: TBD  
Test Session ID / Date: TBD  
Evidence Required: Config review; no secrets in image or repo.

Checklist:
- No API keys or tokens in image or version control.
- Secrets supplied only via env or mounted config.
- Bot credentials not present in Cursor or user’s dev machine.
