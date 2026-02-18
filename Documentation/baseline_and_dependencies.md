# Baseline and Dependencies — PhillBot

Purpose: Single source of truth for toolchain setup, baseline version locks, evidence, and dependency policy.

Status: Drafted; must be filled per project.
Open Items / Remaining Work:
- Record all baseline versions with evidence (Docker, Compose, base image, `gh`).
- Define dependency ownership (vendor vs upstream).
- Confirm upgrade cadence and deferral rules.

---

## 1) Environment and Toolchain Setup

- **Host:** DigitalOcean Droplet, Debian; user `claw`; Docker + Docker Compose.
- **Baseline image:** To be defined (e.g. `node:20` or `python:3.11`); includes GitHub CLI (`gh`). Auth (e.g. `GH_TOKEN`) at runtime only.

Status: Drafted; not validated.
Open Items / Remaining Work:
- Add step-by-step Dockerfile and build commands.
- Confirm setup on droplet and in CI (if added).

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

Baseline table (fill in):
- Docker:
  - Version: TBD
  - Verified date: TBD
- Docker Compose:
  - Version: TBD (e.g. 2.26.x)
  - Verified date: TBD
- Base runtime (e.g. Node/Python):
  - Version: TBD
  - Verified date: TBD
- GitHub CLI (`gh`):
  - Version: TBD
  - Verified date: TBD

---

## 3) Baseline Evidence

Evidence proves the baseline is real (e.g. `docker --version`, `docker compose version`, `gh --version`).

Status: Drafted; evidence pending.
Open Items / Remaining Work:
- Add logs or command outputs from droplet.

---

## 4) Dependency Ownership Policy

- Managed dependencies (npm/pip packages, base images) must not be edited in place.
- If edits are required, promote to local ownership and document in update log.
- Each dependency must declare source (vendor, upstream, fork).

Status: Drafted; policy pending.
Open Items / Remaining Work:
- Confirm ownership model for each dependency class.

---

## 5) Upgrade Cadence and Deferral Protocol

- Run baseline freshness checks at project start and before each milestone.
- If an upgrade is skipped: document reason, owner, target date; add status tracker entry.

Status: Drafted; cadence pending.
Open Items / Remaining Work:
- Choose cadence (e.g. monthly / per milestone).

---

## 6) Dependency Update Playbook

- Record current and target versions; capture why (security, compatibility).
- Create rollback plan before applying update.
- Apply in isolation where possible; re-run baseline checks; record in update log and status tracker.

Status: Drafted; enforcement required.
Open Items / Remaining Work:
- Confirm exact steps for this project.

---

## 7) Rollback and Recovery Plan

- Maintain known-good build reference (commit + image tag).
- Before major update, backup `.env` and critical config.
- On failure: revert to known-good baseline and re-apply changes one at a time.

Status: Drafted; enforcement required.
Open Items / Remaining Work:
- Identify last known-good build and recovery steps.

---

## 8) Known Exceptions

List any known exceptions to the baseline, with owner and target date.

Status: Drafted.
Open Items / Remaining Work:
- Record exceptions as they occur.
