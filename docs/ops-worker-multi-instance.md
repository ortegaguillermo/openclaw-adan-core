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

Add these flags in `adan.flags.json`:

```json
{
  "version": 1,
  "features": {
    "ops_worker_mode": false
  },
  "ops_workers": {
    "enabled_instances": []
  }
}
```

Set `ops_worker_mode=true` and include instance IDs only after report-only validation.

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
