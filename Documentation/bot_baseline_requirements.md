# Bot Baseline – Requirements & Setup (from conversation)

This document captures the agreed requirements and design for the bot (Clawbot / PhillBot) and its baseline image, so the setup is unambiguous and repeatable.

---

## 1. Purpose & scope of the bot

- **Teaching/learning automation:** Accept a subject, PDFs, and similar inputs; teach the user; generate podcast-style audio (Notebook LM–style) and other outputs.
- **Regular contact:** Text/notify the user on a schedule (e.g. via Telegram).
- **Scaffolding & prototyping:** Use Make.com for flows and conceptual builds.
- **Constitution/behavior:** The bot follows a defined constitution and behavior rules (stored in repo or docs, not a separate account).
- **Baseline image:** One repeatable environment (identity + infra + image) that can be copied later with “most instructions and what it means” already in place.

---

## 2. Identity (bot’s own accounts)

- **Gmail (Google account):** Bot’s primary identity. Used for: signup/recovery, Notebook LM, Drive (PDFs), and any other Google services. Do not use the user’s personal Google for bot operations.
- **GitHub:** Bot’s own account. Used for: repos, automation, and GitHub CLI (`gh`) operations. Recovery/secondary email = bot’s Gmail.
- **Prepaid card (reloadable):** Bot’s sole payment method for: DigitalOcean, Make.com, API usage (e.g. OpenAI/Anthropic), and any other paid services. Keeps spend bounded and separate from the user’s personal spending.
- **Make.com:** One account under the bot’s Gmail; billing with the prepaid card when on paid plans.
- **Telegram:** User’s existing (blank-slate) account owns the bot token. No separate Telegram account for the bot. Bot token and config live on the droplet/container only.
- **Anthropic (and similar):** Bot has its **own** Anthropic (and optionally OpenAI) account: bot’s Gmail + prepaid card. User keeps a **separate** Anthropic account for Cursor (budget and security isolation). Bot’s API keys never go into Cursor or the user’s personal tools.

---

## 3. Infrastructure (target state)

- **VPS:** DigitalOcean Droplet, Debian; non-root user `claw` with sudo.
- **Access:** Tailscale on droplet (device name `phill`); SSH only over Tailscale (UFW: allow SSH on `tailscale0` only; no public SSH). User reaches droplet via `tailscale ssh claw@phill` from Mac or Linux laptop (fedora).
- **Runtime:** Docker + Docker Compose on the droplet; user `claw` in the `docker` group. Bot runs as a container (or stack) from a baseline image.
- **Workspace on droplet:** Repo and app live under `~/clawbot` (or equivalent) for the `claw` user.

---

## 4. Where things run – clarified

- **GitHub CLI (`gh`):** Installed **inside the Docker image** that defines the bot’s baseline. Every container built from that image has `gh` available as a standard capability. Auth is **not** baked into the image; it is supplied at runtime (e.g. `GH_TOKEN` in the container environment or mounted config).
- **Bot code and automation:** Run inside containers based on that baseline image (on the droplet).
- **Secrets (Telegram token, API keys, `GH_TOKEN`):** Supplied at runtime via environment or secrets (e.g. `.env`, Docker env, or a secrets backend). Never committed into the repo or baked into the image.

---

## 5. Baseline image (Docker)

- **Base:** Whatever runtime the bot needs (e.g. Node, Python) plus system dependencies.
- **Included in image:** GitHub CLI (`gh`) and any other tools that “every instance” of the bot should have. This is the baseline that gets copied and reused.
- **Not in image:** Secrets, tokens, or keys; auth is runtime-only.

---

## 6. Repo and GitHub auth

- **Repo access:** The bot’s GitHub account must have access to the bot’s repo (invite as collaborator if repo is under user’s account, or create/use repo under bot’s account).
- **Clone/auth on droplet:** Use the bot’s credentials only: PAT (HTTPS) or SSH key added to the bot’s GitHub. PAT or key is stored in the environment or a secure store on the droplet, not in Cursor or the user’s laptop for production use.
- **Clone location:** Repo cloned on the droplet (e.g. `~/clawbot`) for deployment. Optionally also on the laptop for development; if so, keep bot credentials off the laptop or use a separate clone with bot auth only where needed.

---

## 7. Security and separation

- **Blast radius:** A leak in Cursor or the user’s tools must not expose the bot. Therefore: separate Anthropic (and similar) accounts, and bot API keys/tokens only on the droplet (or in the container runtime), never in Cursor or user-facing tools.
- **Budget separation:** User’s allowance (e.g. for Cursor/Anthropic) is separate from the bot’s allowance (prepaid card). Two accounts per service where that matters (e.g. Anthropic).

---

## 8. Deployment and ops (target, not yet implemented)

- Use Pull Requests for deployment; bot does most of the work, user does final approval.
- Staging then production; CI (e.g. GitHub Actions) and deployment steps to be added later.
- Firebase or other backends as needed later; not required for the initial baseline.

---

## 9. Bullet summary – what we are doing

- **Identity:** Bot has its own Gmail, GitHub, prepaid card, Make.com, and (where applicable) its own Anthropic/OpenAI accounts; Telegram uses user’s account, token on droplet only.
- **Infrastructure:** Droplet (phill) with Tailscale-only SSH, Docker + Compose, workspace at `~/clawbot`.
- **Baseline image:** Docker image that includes the bot runtime **and** GitHub CLI (`gh`); auth and secrets only at runtime.
- **Repo:** Bot’s GitHub has access to the bot repo; clone on droplet with bot’s PAT or SSH key; no bot secrets in Cursor or on the user’s machine.
- **Security:** Separate accounts and credentials for bot vs user; bot keys only on droplet/container.
- **Purpose:** Teaching/learning automation, podcast-style output, regular texting (Telegram), Make.com for prototyping, constitution-driven behavior, all from a single copyable baseline.

---

*Document derived from conversation; update as decisions change.*
