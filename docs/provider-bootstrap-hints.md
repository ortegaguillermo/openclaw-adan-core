# Provider Bootstrap Hints (File-Only)

This guide defines provider bootstrap hints for onboarding without platform-specific commands.

## Purpose

Help users declare provider readiness and required follow-up actions in a structured way.

## Principles

- File-first onboarding only
- No hardcoded shell commands
- No assumptions about OS, package manager, or terminal tooling
- Explicitly separate `available`, `configured`, and `validated`

## Recommended States

For each provider/tool integration track three booleans:

- `available`: the user has access to the account/tool
- `configured`: credentials/config are present in OpenClaw
- `validated`: health/status has been checked successfully

## Providers to Track

- `github-copilot`
- `google-antigravity`
- `openai-codex`
- `local-runtime` (ollama/lmstudio/vllm)

## VCS to Track

- `github`
- `gitlab`
- `bitbucket`
- `azure-devops`
- `other`

## Minimal Validation Record

When a user confirms readiness, store:

- date
- owner
- environment label (local/dev/staging/prod)
- known limitations

## Safety Rule

If `configured=false` or `validated=false`, Adan should avoid workflows that depend on that provider and select an available fallback.
