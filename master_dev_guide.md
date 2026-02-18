# Master Development Guide — PhillBot / Clawbot

Purpose: This is the canonical source of truth for the project. It defines scope,
architecture intent, dependency baselines, testing requirements, and operational
guardrails. Every other document must align with this guide.

Status: Drafted; customized for PhillBot.
Open Items / Remaining Work:
- Replace remaining placeholders with project-specific decisions.
- Record baseline versions in `Documentation/baseline_and_dependencies.md`.
- Link to bot identity and infra decisions in `Documentation/bot_baseline_requirements.md`.

---

## 0) Target Experience (Definition of Done)

- **Teaching/learning automation:** User can give a subject and PDFs; the bot teaches, generates podcast-style audio (Notebook LM–style), and texts the user on a schedule.
- **Single baseline image:** One repeatable Docker-based environment (runtime + GitHub CLI + tools) that can be copied; auth and secrets supplied at runtime.
- **Secure, isolated identity:** Bot has its own Gmail, GitHub, prepaid card, and API accounts; no bot secrets in Cursor or on the user’s machine.

Status: Drafted; not validated.
Open Items / Remaining Work:
- Specify end-state flows in concrete steps.
- Confirm scope of Make.com and Telegram integration.

---

## 1) Locked Decisions (Current Truth)

- **Identity:** Bot has its own Gmail, GitHub, prepaid card; Telegram uses user’s account; bot token on droplet only. Separate Anthropic (and similar) accounts for Cursor vs bot.
- **Infrastructure:** DigitalOcean Droplet (phill), Tailscale-only SSH, Docker + Compose, workspace `~/clawbot`.
- **GitHub CLI:** Installed inside the Docker baseline image; auth via `GH_TOKEN` (or equivalent) at runtime only.
- **Repo and auth:** Bot’s GitHub has repo access; clone on droplet with bot’s PAT or SSH key.

Status: Drafted; key decisions captured.
Open Items / Remaining Work:
- Lock any remaining tool choices (runtime language, Make.com scopes).

---

## 2) Technology Baseline

Baseline versions and toolchain are documented in `Documentation/baseline_and_dependencies.md`.  
Identity, infra, and “where things run” are in `Documentation/bot_baseline_requirements.md`.

Status: Drafted; baseline check required.
Open Items / Remaining Work:
- Run baseline freshness gate and record evidence (Docker, Compose, base image, `gh`).

---

## 3) Option Matrix and Architecture Stamp

Every major feature or architectural change must include an option matrix and an architecture stamp in `Documentation/architecture.md`.

Status: Drafted; enforcement required.
Open Items / Remaining Work:
- Ensure architecture stamps are completed before implementation.

---

## 4) UI/UX Contract (if applicable)

For a Telegram/CLI bot: command set, message formats, and allowed flows. Link to `Documentation/component_library.md` or a dedicated contract doc if added.

Status: Drafted; not validated.
Open Items / Remaining Work:
- Define command set and response contract.
- Add test expectations for core flows.

---

## 5) Subsystem Index

- **Identity & infra** — See `Documentation/bot_baseline_requirements.md`.
- **Baseline image (Docker)** — Runtime + `gh`; see `Documentation/architecture.md` and `Documentation/baseline_and_dependencies.md`.
- **Bot runtime** — Telegram, teaching pipeline, scheduling; to be detailed in architecture.
- **CI/deploy** — PR-based deployment, staging → prod; to be implemented.

Status: Drafted; not fully populated.
Open Items / Remaining Work:
- Add each subsystem with Status + Open Items in architecture and status tracker.

---

## 6) Testing and Validation Rules

All features must have a mini test plan before implementation and must be validated in `Documentation/test_plan.md`.

Status: Locked.
Open Items / Remaining Work:
- None.

Rules:
- Do not mark a subsystem complete unless its tests are recorded as Pass.
- Stress scenarios are mandatory for major subsystems.
- Evidence must be captured (logs, screenshots, or command output).

---

## 7) Documentation Rules

Required docs:
- `Documentation/architecture.md`
- `Documentation/component_library.md`
- `Documentation/known_issues.md`
- `Documentation/update_log.md`
- `Documentation/status_tracker.md`
- `Documentation/test_plan.md`
- `Documentation/time_log.md`
- `Documentation/handoff.md`
- `Documentation/handoff_methodologies.md`
- `Documentation/doc_reconciliation_checklist.md`
- `Documentation/baseline_and_dependencies.md`
- `Documentation/bot_baseline_requirements.md`

Status: Locked.
Open Items / Remaining Work:
- None.

Rules:
- Every major section in docs must include Status + Open Items.
- Update log entries must include Summary, Validation, Open Items, Confidence.
- Status tracker must include completion %, blockers, owners, dates.
- Test plan must show Pass/Fail/Pending with evidence links.

---

## 8) Rollback and Recovery

Every project must document a rollback path to the last known-good state.

Status: Drafted; enforcement required.
Open Items / Remaining Work:
- Identify last known-good build/image and how to restore it.
- Document rollback trigger conditions.

Rules:
- Always record the last known-good build (commit hash + image/tag).
- Back up critical config (e.g. `.env`) before risky changes.
- Include recovery steps in `Documentation/known_issues.md`.

---

## 9) Dependency Update Policy (High-Level)

Status: Drafted; enforcement required.
Open Items / Remaining Work:
- Document detailed procedure in `Documentation/baseline_and_dependencies.md`.

Rules:
- Updates must be planned (not ad-hoc) and recorded in the update log.
- If an update is deferred, log the blocker, owner, and target date.
- Updates that impact Docker or build require a rollback plan.

---

## 10) Rule Synchronization Requirement (Global Rule Pack)

If a new guardrail or documentation rule is created in this project, update the Global Rule Pack repository in the same session and record the update in `Documentation/update_log.md`.

Status: Locked.
Open Items / Remaining Work:
- None.

---

## 11) Open Questions

- Exact bot repo URL and final clone path on droplet.
- Make.com scope and which flows are in scope for v1.

Status: Drafted.
Open Items / Remaining Work:
- Resolve each question before implementation.
