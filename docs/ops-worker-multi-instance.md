# Ops Worker Multi-Instance Guide

This guide defines how to instantiate independent operations workers per project (including multi-repo projects).

## Goals

- Reuse one worker module across many projects.
- Keep instances isolated so one project cannot block another.
- Support mono-repo and multi-repo scopes.

## Files

- Module: `modules/ops-worker/`
- Instance declarations: `ops-workers/*.yaml` (workspace-local)
- Flag gate: `adan.flags.json`

## Enablement

1. Enable the feature flag in `~/.openclaw/workspace/adan.flags.json`:

```json
{
  "version": 1,
  "features": {
    "ops_worker_mode": true
  }
}
```

> **Note on `ops_worker_instances`:** Some older documentation (for example, `modules/ops-worker/README.md`) references an additional `ops_worker_instances` feature flag that lists enabled instance IDs. For the multi-instance workflow described in this guide, instances are **auto-discovered** from `~/.openclaw/workspace/ops-workers/*.yaml`, and `ops_worker_instances` is not required and can be treated as deprecated/ignored.

2. Add an instance YAML in `~/.openclaw/workspace/ops-workers/<instance-id>.yaml`:

See the next section for instance schema and examples.

**Workflow:** Start with `enabled: false` in the instance YAML for report-only validation. After 24-48h of healthy runs, set `enabled: true` to begin autonomous action.

## Instance Lifecycle

1. Declare instance YAML.
2. Validate schema.
3. Start in report-only mode.
4. Enable write actions after 24-48h healthy runs.

## Multi-Repo Execution Notes

- Process each repo independently with per-repo locks when needed.
- For cross-repo tickets, keep one parent issue and one PR per repo.
- Publish a single final summary referencing all affected PRs.

## Issue Comment Attribution (Cron/Worker Prompts)

Ensure worker cron prompts and runbooks include this mandatory footer for every GitHub issue status/closure comment:

`Model used: <provider/model>`

If the model id cannot be resolved at runtime, workers must emit:

`Model used: unknown`


## Update Strategy Across OpenClaw Installs

- Ship module updates from Adan Core repository.
- Keep instance files local (`ops-workers/*.yaml`) and never overwrite automatically.
- Maintain schema backward compatibility for at least one previous version.
