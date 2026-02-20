# AgentOS Overview — PhillBot

Purpose: Define a portable agent operating system structure for PhillBot so future bots follow the same role model, file contract, and abstraction boundaries.

Status: Approved for adoption.
Open Items / Remaining Work:
- Create the `AgentOS/` directory structure in-repo when implementation starts.
- Add first orchestrator and sub-agent `soul.md` files from this contract.

---

## 1) Why this exists

PhillBot is moving toward orchestrator-first operations with role separation (Builder, Examiner, Judge, Ops). This document standardizes how those roles are represented and how they interact without leaking context or permissions.

Status: Locked.
Open Items / Remaining Work:
- None.

---

## 2) Core principles

- Orchestrator holds global context and delegates narrow tasks.
- Sub-agents are intentionally under-informed (minimum required context only).
- Interface, orchestration, domain, data, integration remain separated.
- Secrets never appear in agent prompt files or docs.
- Every role obeys least privilege from `Documentation/role_permissions_matrix.md`.

Status: Locked.
Open Items / Remaining Work:
- Add pipeline checks for role boundary compliance.

---

## 3) Standard file contract

### Orchestrator files
- `soul.md` — identity, behavior, delegation rules.
- `agents.md` — directory of available sub-agents and capabilities.
- `user.md` — operator preferences and decision style.
- `memory.md` — memory policy and what belongs in DB vs docs.
- `tools/` — capability references and runbooks.

### Sub-agent files
- `soul.md` — role contract for that agent.
- `tools/` — restricted tool contract for that role.

Sub-agents should not carry global `agents.md` or global memory authority.

Status: Approved.
Open Items / Remaining Work:
- Implement starter templates for orchestrator and first two agents.

---

## 4) Proposed directory layout

```text
/AgentOS/
  /orchestrator/
    soul.md
    agents.md
    user.md
    memory.md
    /tools/
      model_routing.md
      runbooks.md
      ui_spec.md
      env_contract.md
  /agents/
    /builder_ui/
      soul.md
      /tools/
    /examiner/
      soul.md
      /tools/
    /judge/
      soul.md
      /tools/
    /ops_sre/
      soul.md
      /tools/
```

Status: Drafted.
Open Items / Remaining Work:
- Decide final naming for each sub-agent and tool package.

---

## 5) Role-specific non-negotiables

- Builder cannot access hidden scenario generation internals.
- Examiner cannot edit production code.
- Judge cannot author code or scenarios.
- Ops can run runtime controls but does not override branch protections.
- Orchestrator can plan/delegate but must respect role boundaries.

Status: Locked.
Open Items / Remaining Work:
- Add enforcement references in CI and review checklists.

---

## 6) Integration with existing docs

This AgentOS structure extends existing docs, not replaces them:

- Architecture and subsystem boundaries: `Documentation/architecture.md`
- Role permissions and secret scopes: `Documentation/role_permissions_matrix.md`
- Testing model (dark-factory/thin-twin): `Documentation/testing_architecture.md`
- Operational loops and incident model: `Documentation/operating_model.md`

Status: Active.
Open Items / Remaining Work:
- Add cross-links in each referenced document.

---

## 7) Adoption plan

1. Create `AgentOS/` skeleton.
2. Add orchestrator `soul.md` + `agents.md`.
3. Add `builder_ui` and `ops_sre` agents first.
4. Add `examiner` and `judge` as scenario system comes online.
5. Verify each agent against role permissions matrix before use.

Status: Drafted.
Open Items / Remaining Work:
- Schedule implementation window.
