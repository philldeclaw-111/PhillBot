# Baseline and Dependencies — PhillBot

Purpose: Single source of truth for toolchain setup, baseline version locks, evidence, and dependency policy.

Status: In progress; partial baseline validated in production-like environment.
Open Items / Remaining Work:
- Capture OpenClaw release/tag and image digest as known-good baseline.
- Confirm runtime GitHub operation tests using `GH_TOKEN`.
- Document recovery steps for device token mismatch in local CLI path.
- Complete runtime migration from broad service-role DB key to scoped DB role credentials.

---

## 1) Environment and Toolchain Setup

- **Host:** DigitalOcean Droplet (`phill`), Debian; user `claw`; Docker + Docker Compose.
- **Runtime:** OpenClaw upstream cloned at `/home/claw/openclaw-upstream`, launched via docker compose.
- **Memory backend:** Supabase project configured; `memory` schema + `entries` table created.
- **Secrets loading:** `.env` + `docker-compose.override.yml` used to inject runtime env vars into `openclaw-gateway` and `openclaw-cli`.

Status: Partially validated.
Open Items / Remaining Work:
- Capture command bundle for reproducible bootstrap from clean droplet.
- Add CI parity notes for non-interactive onboarding and channel registration.

Required fields (to fill):
- Base image name and tag
- GitHub CLI install method and version
- Environment variables (non-secret)
- Build/run commands
- Where secrets are supplied (env file, secrets backend)

---

## 2) Baseline Version Lock

All baseline versions must be the newest stable releases compatible with the deployment target. If not, a blocker must be documented.

Status: Drafted; baseline check pending.
Open Items / Remaining Work:
- Verify newest stable versions for Docker, Compose, base runtime, `gh`.
- Record any deferrals with owner and target date.

Baseline table:
- Docker:
  - Version: `26.1.5+dfsg1`
  - Verified date: 2026-02-18
- Docker Compose:
  - Version: `2.26.1-4`
  - Verified date: 2026-02-18
- Runtime stack:
  - Version: OpenClaw `2026.2.18` (reported by runtime startup)
  - Verified date: 2026-02-18
- Host Node/npm:
  - Version: Not installed on host (`node`/`npm` not found), expected because runtime is containerized.
  - Verified date: 2026-02-18

---

## 3) Baseline Evidence

Evidence proves the baseline is real (e.g. `docker --version`, `docker compose version`, `gh --version`).

Status: Partially complete.
Open Items / Remaining Work:
- Add captured log references for onboarding + channel pairing.
- Add Supabase SQL evidence snapshots (schema/table/extension checks).

Evidence captured:
- `tailscale status` showed fedora + phill connectivity.
- `docker --version` and `docker compose version` verified.
- OpenClaw onboarding completed; gateway started and Telegram account added.
- Telegram `/status` responded after pairing approval.
- Supabase SQL checks passed:
  - `create extension if not exists vector;`
  - `create schema if not exists memory;`
  - `memory.entries` table present and writable.
  - role + policy hardening:
    - `bot_runtime`, `bot_judge_ro`, `bot_runtime_staging`
    - `memory_staging.entries` with dedicated sequence + RLS policy

---

## 4) Dependency Ownership Policy

- Managed dependencies (npm/pip packages, base images) must not be edited in place.
- If edits are required, promote to local ownership and document in update log.
- Each dependency must declare source (vendor, upstream, fork).

Status: Active.
Open Items / Remaining Work:
- Confirm ownership model for each dependency class.

---

## 5) Upgrade Cadence and Deferral Protocol

- Run baseline freshness checks at project start and before each milestone.
- If an upgrade is skipped: document reason, owner, target date; add status tracker entry.

Status: Drafted; cadence pending.
Open Items / Remaining Work:
- Choose cadence (recommended: per milestone + monthly review for runtime/image updates).

---

## 6) Dependency Update Playbook

- Record current and target versions; capture why (security, compatibility).
- Create rollback plan before applying update.
- Apply in isolation where possible; re-run baseline checks; record in update log and status tracker.

Status: Active; partially exercised.
Open Items / Remaining Work:
- Add token-rotation regression checks (Telegram/OpenAI/Supabase/GitHub).

---

## 7) Rollback and Recovery Plan

- Maintain known-good build reference (commit + image tag).
- Before major update, backup `.env` and critical config.
- On failure: revert to known-good baseline and re-apply changes one at a time.

Status: Active.
Open Items / Remaining Work:
- Record known-good compose startup state and matching config hash.
- Add "device token mismatch" resolution runbook for local CLI.

---

## 8) Known Exceptions

List any known exceptions to the baseline, with owner and target date.

Status: Drafted.
Open Items / Remaining Work:
- Current known exception: `health --token` flag mismatch in current OpenClaw build; health path uses alternate command flow.
