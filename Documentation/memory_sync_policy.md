# Memory Synchronization Policy — Supabase + GitHub Journal

Purpose: Define a hybrid memory strategy where operational memory lives in the database and curated, human-readable snapshots are versioned in GitHub.

Status: Approved for implementation.
Open Items / Remaining Work:
- Implement daily/weekly sync jobs.
- Add redaction and secret scanning before every memory-journal commit.

---

## 1) Source of truth model

- **Primary memory source of truth:** Supabase (`memory.entries`).
- **Secondary curated archive:** GitHub memory journal repository.

Database is authoritative for live retrieval and command operations.
GitHub journal is authoritative for historical narrative/audit summaries.

Status: Locked.
Open Items / Remaining Work:
- Finalize journal repository name and branch protections.

---

## 2) Why hybrid

Supabase strengths:
- Queryable, structured, high-churn operational memory.
- Better for retrieval and command-driven updates.

GitHub journal strengths:
- Human-readable version history.
- Easy review/audit and rollback of narrative records.

Status: Locked.
Open Items / Remaining Work:
- None.

---

## 3) What syncs to GitHub (allowlist)

Allowed:
- Daily curated summaries.
- Weekly synthesis.
- Pinned strategic memory items.
- Incident summaries and resolution notes.
- Decision logs and operating policy changes.

Not allowed:
- Raw tokens/API keys/session secrets.
- Full raw provider payload dumps.
- Unredacted sensitive data.
- High-churn transient runtime noise.

Status: Locked.
Open Items / Remaining Work:
- Add automated redaction tests.

---

## 4) Journal repository structure (recommended)

```text
memory-journal/
  daily/
    YYYY-MM-DD.md
  weekly/
    YYYY-WW.md
  pinned/
    <topic>.md
  incidents/
    YYYY-MM-DD-<slug>.md
  decisions/
    YYYY-MM-DD-<slug>.md
```

Status: Drafted.
Open Items / Remaining Work:
- Confirm final file naming and metadata headers.

---

## 5) Sync cadence

- Daily job:
  - summarize last 24h memory deltas/events
  - write/update daily markdown
  - commit via bot GitHub token (PR flow preferred)

- Weekly job:
  - synthesize daily entries into weekly report
  - include unresolved risk/open items summary

- Manual snapshot:
  - operator-triggered “snapshot now” for major milestones/incidents

Status: Drafted.
Open Items / Remaining Work:
- Implement scheduler and job idempotency controls.

---

## 6) Security and governance

- Journal commits must pass redaction rules.
- Branch protection enabled (no direct main pushes).
- Bot token limited to journal repository scope.
- Every sync operation logs:
  - source range
  - rows processed
  - files changed
  - commit/PR reference

Status: Active policy.
Open Items / Remaining Work:
- Add failed-sync alerting path to Telegram.

---

## 7) Failure handling

If sync fails:
1. Record incident event with severity.
2. Alert operator through non-AI path.
3. Retry with backoff.
4. If still failing, queue manual replay job.

No data deletion occurs due to sync failure.

Status: Drafted.
Open Items / Remaining Work:
- Implement replay queue and reprocessing runbook.

---

## 8) Retention and evolution

Current retention:
- Supabase memory kept indefinitely (subject to future policy review).
- Journal kept indefinitely (Git history serves as archive).

Future (Option C transition):
- Tiered archival policy.
- Access-controlled memory classes.
- Approval workflow for sensitive memory promotion.

Status: In progress.
Open Items / Remaining Work:
- Define exact retention windows by memory type.

---

## 9) Validation checklist

- [ ] Supabase write/read operational.
- [ ] Journal repo initialized and protected.
- [ ] Daily sync job writes expected files.
- [ ] Weekly synthesis job runs and links source periods.
- [ ] Redaction scanner blocks sensitive output.
- [ ] Telegram alert triggers on sync failure.

Status: Drafted.
Open Items / Remaining Work:
- Link checklist outcomes into `Documentation/test_plan.md`.
