# SDD Docker Sandbox Kit

This is a Docker Sandboxes mixin kit for running the local Spec-Driven Development workflow with the built-in `codex` agent.

## Use Locally

```sh
sbx secret set -g openai
sbx run codex --kit ./sdd_kit .
```

For an empty directory, initialize Git first if you want the workflow artifacts and generated project files to be reviewable:

```sh
git init
printf ".sbx/\n" >> .gitignore
sbx run codex --kit /absolute/path/to/sdd_kit .
```

## What `spec.yaml` Does

- `schemaVersion: "1"` selects the Docker kit spec version.
- `kind: mixin` means this kit extends an existing agent such as `codex`; it does not define a new agent image.
- `environment.variables` exposes stable paths that the agent can inspect.
- `files/home/` is copied to `/home/agent/` inside the sandbox.

The copied files install:

- SDD skills under `/home/agent/.agents/skills`
- role reference TOMLs under `/home/agent/.agents/sdd-agents`
- Codex global guidance under `/home/agent/.codex/AGENTS.md`
- a short sandbox note under `/home/agent/.sdd/README.md`

This kit deliberately uses `/home/agent/.codex/AGENTS.md` instead of the Docker kit `memory` field. Docker applies `memory` only when an agent kit has `agent.aiFilename`; for this mixin we want explicit Codex behavior through `CODEX_HOME`.

## Branch Mode

Branch mode is optional for this kit.

Default direct mode is better for the first MVP because the SDD workflow is synchronous and approval-gated:

```sh
sbx run codex --kit ./sdd_kit .
```

Use branch mode when you want Docker to create a separate Git worktree for isolation, parallel runs, or PR-style review:

```sh
sbx run codex --branch auto --kit ./sdd_kit .
```

Branch mode requires a Git repository and a committed baseline. For empty directories, direct mode plus `git init` is simpler.

## Validate

```sh
sbx kit validate ./sdd_kit
sbx kit inspect ./sdd_kit
```
