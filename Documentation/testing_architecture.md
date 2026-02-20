# Testing Architecture — Dark Factory + Thin Twin Model

Purpose: Define the operational testing model for PhillBot using independent agent roles, scenario-driven validation, and lightweight digital twin modules.

Status: Approved for v1 rollout (Builder + Examiner + rule-based Judge).
Open Items / Remaining Work:
- Implement scenario pack generation with seeded randomness.
- Encode invariant checks into executable test harnesses.
- Add chaos dial controls (time/network/event) for automated runs.

---

## 1) Operating Principle

We do not optimize for fixed checklists. We optimize for resilient behavior under realistic scenarios.

- Builder should not know hidden scenario generation logic.
- Examiner should not depend on builder internals.
- Judge should score against invariants and severity gates.

Status: Approved.
Open Items / Remaining Work:
- Enforce artifact boundaries in CI/test execution.

---

## 2) Role Model and Boundaries

### Builder (implementation role)

- Inputs: spec, architecture constraints, public acceptance criteria.
- Outputs: code, artifacts, instrumentation hooks, basic smoke checks.
- Must not access: hidden scenario seeds, hidden edge-case packs, judge internals.

### Examiner (scenario role)

- Inputs: spec, risk profile, threat model, interface contracts.
- Outputs: scenario packs, seed configurations, chaos settings.
- Must not access: builder private notes/reasoning, privileged code internals beyond allowed black-box contracts.

### Judge (scoring role)

- Inputs: logs, scenario outcomes, expected invariants.
- Outputs: pass/fail, severity, reproducibility notes, minimal failure narrative.
- Must not write code or scenario generators.

Status: Approved.
Open Items / Remaining Work:
- Encode these as explicit pipeline permissions and folder access policies.

---

## 3) Artifact Airlock Layout

- `/spec/` shared source of intent.
- `/build_output/` builder artifacts.
- `/scenario_pack/` examiner-owned; hidden from builder internals.
- `/results/` judge outputs and reproducible failures.

Builder receives:
- spec + public acceptance criteria + small public smoke set.

Examiner receives:
- spec + interface boundaries + logging schema.

Judge receives:
- run artifacts + logs + invariant rubric.

Status: Drafted.
Open Items / Remaining Work:
- Implement this folder contract in repo tooling.

---

## 4) Scenario-First Validation Standard

Scenarios are first-class; static checklist tests are secondary.

Every scenario includes:
- Context (actor, objective, constraints).
- Action sequence (including retries/mistakes/interruptions).
- Expected properties (invariants), not brittle exact strings.

Status: Approved.
Open Items / Remaining Work:
- Build scenario template and seed registry.

---

## 5) Invariant Scoring Model

Judge prioritizes these invariants:

- No unhandled crashes.
- State consistency (no phantom writes/duplicates).
- Idempotency under retries.
- Permission integrity and no secret leakage.
- Safety policy adherence.
- Graceful degradation and fallback behavior.
- Timeouts/partial failure handling.

Severity gates:
- P0: security/data-loss/system corruption -> release blocked.
- P1: major functional break -> release blocked unless explicitly deferred with owner/date.
- P2: non-critical behavior drift -> fix queued with date.

Status: Drafted; enforcement required.
Open Items / Remaining Work:
- Encode scoring rubric in CI artifacts and release gate rules.

---

## 6) Thin Twin Universe Strategy

We model pressure points, not a full fake world.

Twin levels:

1. Contract Twin
- Schema and boundary validation.

2. Stub Twin
- Deterministic fake services.

3. Behavior Twin
- Latency, timeout, retry, malformed payload, partial failure simulation.

4. Replay Twin
- Sanitized record/replay from real interactions.

5. Scenario Universe (later)
- Multi-system narratives with controllable chaos.

Status: Approved for phased rollout.
Open Items / Remaining Work:
- Implement Levels 0-2 first; add replay layer after baseline stability.

---

## 7) First Twin Modules (v1)

- Time Twin: controlled clock and scheduling drift.
- Network Twin: latency/timeout/connect reset.
- Event Twin: duplicates, out-of-order, delayed bursts.
- Data Twin: missing/dirty/unicode/oversized input.
- Identity Twin: revoked access/expired token/mismatched identity.

Status: Drafted.
Open Items / Remaining Work:
- Add module ownership and execution toggles.

---

## 8) Execution Loop

1. Spec drop (versioned constraints and non-goals).
2. Build pass (builder artifacts + instrumentation).
3. Scenario generation (examiner with seed + risk profile).
4. Execution (including chaos dial settings).
5. Judgment (severity + repro).
6. Patch (builder fixes + regression anchors).
7. Repeat across seeds until gate criteria pass.

Status: Approved.
Open Items / Remaining Work:
- Automate seed scheduling and run archive retention.

---

## 9) Twin Budget Rule

Only twin something when at least one is true:

- It fails frequently in the real world.
- It is high risk or expensive to test in production.
- It causes subtle state/time/ordering defects.

Status: Locked.
Open Items / Remaining Work:
- None.

---

## 10) Current Rollout Plan

- Phase 1: Builder + Examiner + rule-based Judge.
- Phase 2: Introduce replay twin and P0/P1 hard gates.
- Phase 3: Full Judge automation + red-team scenario pack.

Status: Drafted.
Open Items / Remaining Work:
- Link this to `Documentation/test_plan.md` and `Documentation/status_tracker.md`.
