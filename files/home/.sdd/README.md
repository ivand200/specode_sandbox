# SDD Kit Notes

Spec-Driven Development skills are installed at:

```text
/home/agent/.agents/skills
```

Role reference files are installed at:

```text
/home/agent/.agents/sdd-agents
```

Use `manager` first for implementation requests. It chooses the smallest safe path, writes workflow state under `tasks/<task-name>/`, and stops at approval gates.
