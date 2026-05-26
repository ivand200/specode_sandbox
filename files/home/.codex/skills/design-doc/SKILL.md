---
name: design-doc
description: Create or refine compact `design.md` artifacts for approved work: task-specific module/interface design, diagrams, options, tradeoffs, context research, and oracle proof before coding.
---

# Design Doc

Create or refresh `tasks/<task-name>/design.md`. Do not edit product code, tests, workflow state, or `tasks.md`.

A design doc is a decision artifact: what changes, which option is recommended or chosen, why, what the system shape looks like, and how correctness will be proven.

Use `architecture-principles` for module/interface judgment, `research-context` for decision-changing facts, and `oracle-gate` for proof.

## Use When

- approved requirements or bug analysis need implementation direction
- the work touches module seams, interfaces, data, auth/security/privacy, operations, migrations, performance, concurrency, integrations, or cross-module behavior
- multiple viable implementation options exist
- implementation would likely churn without a design decision
- `breakdown` needs a clear source for vertical implementation slices

Skip or keep tiny for localized, low-risk work.

## Rules

- Start from the affected caller/user-visible seam.
- Trace outward to the system boundary and inward to the owning modules before choosing a design.
- Stop tracing when affected contracts, owners, risks, and proof seams are clear.
- Treat `steering/` as durable project context; call out missing, stale, or conflicting steering.
- Do not duplicate requirements, routing facts, bug maps, or acceptance examples from `task.md`.
- Record only evidence that changes the design decision.
- Compare real options before choosing.
- For each serious option, capture its tradeoff.
- Recommend the smallest safe option; after approval, record it as the chosen option.
- Include diagrams by default for medium/large work.
- Use multiple Mermaid diagrams when they answer different design questions: module shape, sequence, state, data flow, integration, migration, or error flow.
- Each diagram must clarify a design decision; avoid file trees, decorative maps, and helper-call graphs.
- If no diagram is useful, write `Diagram not needed:` with one short reason.
- Put research in `Context Research`; do not create `context.md`.
- Put proof in `Oracle Gate`; prefer 1-3 critical claims over one check per requirement.
- Use `grill-me` for important unresolved product, architecture, interface, or proof questions.
- Report downstream impact, but let `manager` own state and stale marking.

## Workflow

1. Read `task.md` or `bugfix.md`.
2. Read `state.json`, existing `design.md`, and `tasks.md` when present.
3. Read relevant `steering/`; read README/AGENTS/CLAUDE only if decision-changing.
4. Trace outward to system boundary and inward to owning modules from the visible seam.
5. Research only facts that change options or risk.
6. Compare options and recommend the smallest safe one.
7. Add the diagrams needed to explain the design.
8. Define the oracle proof.
9. Write or refresh `design.md`.

## Output Shape

`design.md` should include:

- `Goal`: the design decision this doc settles
- `Context Research`: only decision-changing evidence, options, risk, confidence
- `Design Diagrams`: Mermaid diagrams as needed
- `Module / Interface Design`: modules, seams, caller-visible interfaces, contracts, invariants, error modes, changes, hidden implementation
- `Options`: real alternatives, tradeoffs, and recommended option
- `Decision`: chosen option after approval, why, invariants, tradeoffs
- `Data / Operations`: only relevant data, migration, runtime, security, privacy, performance, concurrency concerns
- `Oracle Gate`: critical proof claims and checks
- `Review Scope`: interface impact, contract impact, risk flags, reviewer mode
- `Open Questions`: only questions that can change implementation
- `Downstream Impact`: whether `tasks.md` is current, stale, or not created
