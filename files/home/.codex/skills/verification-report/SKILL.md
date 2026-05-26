---
name: verification-report
description: Create a tiny human acceptance brief for completed agent work, mapping key claims to proof and naming the QA/code-review checks worth doing before acceptance.
---

# Verification Report

Create or refresh `tasks/<task-name>/verification-report.md`.

This is not a code review, test inventory, or change summary. It is a QA/code-review plan for the engineer: what is proven, what is weak, and where human attention should go.

## Rules

- Do not edit code, tests, workflow state, or accepted artifacts.
- Do not review every line or summarize every artifact.
- Start from `Oracle Gate`; use task/design only when needed.
- Prefer risk-carrying seams over file lists.
- Report only proof and checks that change acceptance confidence.
- If proof is weak, missing, stale, or only claimed by an agent, say so.

## Workflow

1. Pick the 1-3 claims that decide acceptance.
2. Map each claim to the strongest evidence.
3. Pick the 1-3 human QA/code-review checks that matter.
4. Name residual risk.
5. Give a verdict: `ready`, `inspect`, or `blocked`.

## Output

```md
# Verification Report: [Task Name]

## Verdict
- `ready | inspect | blocked`

## Evidence

| Claim | Evidence | Status |
| --- | --- | --- |
| ... | ... | `passed | weak | missing | failed` |

## Human QA Plan

- Check ...
- Try ...
- Inspect ...

## Residual Risk

- ...
```

Keep it short. If this needs a long report, the task needed a stronger oracle, smaller slices, or targeted review.
