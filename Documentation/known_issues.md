# Known Issues — PhillBot

Purpose: Track active bugs, platform constraints, and recovery paths.

Status: Drafted; active issues must be logged here.
Open Items / Remaining Work:
- Record issues as they are discovered.

---

## Issues

(No issues logged yet.)

### Issue template (use when adding)

**Issue Title:**  
**Impact:**  
**Repro:**  
**Root Cause:**  
**Recovery:**  

---

## Recovery and rollback

- Last known-good build/image: TBD (document when first stable deploy exists).
- Rollback: Revert to last known-good commit; rebuild image if needed; restore `.env` from backup if changed.
- See `Documentation/baseline_and_dependencies.md` for rollback playbook.
