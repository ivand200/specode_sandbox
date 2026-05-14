# Spec-Driven Development Workflow

This Docker Sandbox includes a Spec-Driven Development workflow pack.

Use installed skills from `/home/agent/.agents/skills` when their descriptions match the user's request. The main workflow skills are:

- `manager`: choose the smallest safe workflow path, persist `tasks/<task-name>/state.json`, run one step, then stop for approval.
- `task-requirements`: create feature `tasks/<task-name>/task.md`.
- `bugfix-spec`: create defect-focused `tasks/<task-name>/task.md`.
- `design-doc`: create `tasks/<task-name>/design.md` with context research and oracle proof when needed.
- `breakdown`: create `tasks/<task-name>/tasks.md` only when an accepted design needs explicit implementation slices.
- `behavior-tests`: write or review tests through stable behavior or public contract boundaries.
- `verification-report`: summarize final proof in `tasks/<task-name>/verification-report.md`.

Reference role definitions are available at `/home/agent/.agents/sdd-agents`.

Workflow rules:

- Treat `state.json` and current task artifacts as source of truth.
- For new feature work, start with `manager` and `task-requirements`.
- For defects or regressions, start with `manager` and `bugfix-spec`.
- For medium or risky work, use `design-doc`; keep `Context Research` and `Oracle Gate` inside `design.md`.
- Run exactly one planning step at a time and stop for the engineer's approval at gates.
- Do not create extra handoff files unless the workflow artifact explicitly calls for them.
- If the workspace is not a git repository and the user wants to start an SDD workflow in this directory, initialize git before creating workflow artifacts.
- Do not use Docker Sandbox branch mode unless the user or launcher explicitly requested an isolated worktree run.
