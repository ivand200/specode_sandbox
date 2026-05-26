# SDD Docker Sandbox Kit

This is a Docker Sandboxes mixin kit for running the local Spec-Driven Development workflow with the built-in `codex` agent.

## Quick Start

Use the published kit from GitHub:

```sh
sbx secret set -g openai
sbx run codex --kit "git+https://github.com/ivand200/specode_sandbox.git" .
```

Use the local checkout while developing the kit:

```sh
sbx secret set -g openai
sbx run codex --kit /absolute/path/to/sdd_kit .
```

For an empty directory, initialize Git first if you want the workflow artifacts and generated project files to be reviewable:

```sh
git init
printf ".sbx/\n" >> .gitignore
sbx run codex --kit /absolute/path/to/sdd_kit .
```

## Authentication And Environment

The correct OpenAI environment variable name is:

```text
OPENAI_API_KEY
```

Prefer Docker Sandboxes stored secrets. The real key stays on the host in the OS keychain; the sandbox receives proxy-managed credentials instead of the raw secret.

Interactive API key setup:

```sh
sbx secret set -g openai
```

Non-interactive API key setup from an existing host environment variable:

```sh
echo "$OPENAI_API_KEY" | sbx secret set -g openai
```

OAuth setup:

```sh
sbx secret set -g openai --oauth
```

Check configured secrets:

```sh
sbx secret ls
```

Alternative for quick local testing: export the key in the host shell before creating the sandbox.

```sh
export OPENAI_API_KEY="sk-..."
sbx run codex --kit "git+https://github.com/ivand200/specode_sandbox.git" .
```

Stored secrets are preferred over plain host environment variables. Do not set the real key manually inside the sandbox, and do not commit it to any file.

### Why `spec.yaml` Does Not Declare OpenAI Credentials

This kit is a `mixin` for Docker's built-in `codex` agent. The built-in Codex agent already declares the OpenAI service identifier and proxy behavior, so users only need:

```sh
sbx secret set -g openai
```

Do not put a real API key in `spec.yaml`.

If we later build a custom agent kit instead of extending Docker's built-in `codex` agent, the credential wiring would look like this:

```yaml
credentials:
  sources:
    openai:
      env:
        - OPENAI_API_KEY

network:
  serviceDomains:
    api.openai.com: openai
  serviceAuth:
    openai:
      headerName: Authorization
      valueFormat: "Bearer %s"

environment:
  proxyManaged:
    - OPENAI_API_KEY
```

That custom-agent shape tells Docker which host-side secret to use, which outbound domain to match, which header to write, and which placeholder environment variable to expose in the sandbox.

## Run Modes

Existing project, direct mode:

```sh
cd /path/to/project
sbx run codex --kit "git+https://github.com/ivand200/specode_sandbox.git" .
```

Existing project, named sandbox:

```sh
cd /path/to/project
sbx run codex --name specode-my-project --kit "git+https://github.com/ivand200/specode_sandbox.git" .
```

Existing project, isolated branch/worktree mode:

```sh
cd /path/to/project
sbx run codex --branch auto --kit "git+https://github.com/ivand200/specode_sandbox.git" .
```

Remote server:

```sh
sbx login
sbx secret set -g openai
cd /srv/my-project
sbx run codex --kit "git+https://github.com/ivand200/specode_sandbox.git" .
```

Pass extra Codex options after `--`:

```sh
sbx run codex --kit "git+https://github.com/ivand200/specode_sandbox.git" . -- --model gpt-5.3-codex
```

## Monitoring And Inspection

List sandboxes:

```sh
sbx ls
sbx ls --json
```

Open a shell inside a sandbox:

```sh
sbx exec -it <sandbox-name> bash
```

Verify that the SDD kit landed inside the sandbox:

```sh
sbx exec <sandbox-name> sh -lc 'test -f /home/agent/.codex/AGENTS.md && test -f /home/agent/.codex/skills/manager/SKILL.md && test -f /home/agent/.codex/agents/developer.toml && echo ok'
```

Inspect network policy decisions:

```sh
sbx policy log
```

Remove a sandbox:

```sh
sbx rm <sandbox-name>
```

Note: `--kit` only applies when a sandbox is created. To apply a changed kit to an existing sandbox, either create a new sandbox or use `sbx kit add <sandbox-name> <kit-ref>`.

## What `spec.yaml` Does

- `schemaVersion: "1"` selects the Docker kit spec version.
- `kind: mixin` means this kit extends an existing agent such as `codex`; it does not define a new agent image.
- `environment.variables` exposes stable paths that the agent can inspect.
- `files/home/` is copied to `/home/agent/` inside the sandbox.

The copied files install:

- SDD skills under `/home/agent/.codex/skills`
- SDD subagent TOMLs under `/home/agent/.codex/agents`
- Codex global guidance under `/home/agent/.codex/AGENTS.md`
- a short sandbox note under `/home/agent/.sdd/README.md`

This kit deliberately uses `/home/agent/.codex/AGENTS.md` instead of the Docker kit `memory` field. Docker applies `memory` only when an agent kit has `agent.aiFilename`; for this mixin we want explicit Codex behavior through `CODEX_HOME`.

## Packaged Workflow Content

The maintainer source of truth for packaged workflow content is the local global Codex home:

- `$CODEX_HOME/skills`
- `$CODEX_HOME/agents`

Keep `files/home/.codex/skills` and `files/home/.codex/agents` synced from those global paths. Do not package system/private skills unless the project intentionally decides to ship them.

Packaged skills:

- `architecture-principles`
- `behavior-tests`
- `breakdown`
- `bugfix-spec`
- `design-doc`
- `glossary`
- `grill-me`
- `make-diagram`
- `manager`
- `module-interface-sketch`
- `oracle-gate`
- `orient`
- `project-specs`
- `research-context`
- `task-requirements`
- `verification-report`

Packaged role adapters:

- `developer.toml`
- `reviewer.toml`
- `tester.toml`

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
