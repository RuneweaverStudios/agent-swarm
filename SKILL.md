---
name: agent-swarm
displayName: Agent Swarm | OpenClaw Skill
description: "IMPORTANT: OpenRouter is required. Routes tasks to the right model and always delegates work through sessions_spawn."
version: 1.7.2
---

# Agent Swarm | OpenClaw Skill

## What this skill does

Agent Swarm is a traffic cop for AI models.
It picks the best model for each task, then starts a sub-agent to do the work.

### IMPORTANT: OpenRouter is required

- You must have an OpenRouter API key set in OpenClaw.
- Model IDs must use `openrouter/...`.
- If OpenRouter is not set, delegation will fail.

## Why this helps

- Faster replies (cheap orchestrator, smart sub-agent routing)
- Better quality (code tasks go to code models, writing tasks go to writing models)
- Lower cost (you do not run every task on the most expensive model)

## Core rule (non-negotiable)

For user tasks, the orchestrator must delegate.
It must NOT answer the task itself.

Use this flow every time:

1. Run router:
   ```bash
   python3 /Users/ghost/.openclaw/workspace/skills/agent-swarm/scripts/router.py spawn --json "<user message>"
   ```
2. If `needs_config_patch` is true: stop and report that patch to the user.
3. Otherwise call:
   `sessions_spawn(task=..., model=..., sessionTarget=...)`
4. Wait for `sessions_spawn` result.
5. Return the sub-agent result to the user.

If `sessions_spawn` fails, return only a delegation failure message.
Do not do the task yourself.

## Quick examples

### Single task

Router output:
`{"task":"write a poem","model":"openrouter/moonshotai/kimi-k2.5","sessionTarget":"isolated"}`

Then call:
`sessions_spawn(task="write a poem", model="openrouter/moonshotai/kimi-k2.5", sessionTarget="isolated")`

### Parallel tasks

```bash
python3 /Users/ghost/.openclaw/workspace/skills/agent-swarm/scripts/router.py spawn --json --multi "fix bug and write poem"
```

This returns multiple spawn configs. Start one sub-agent per config.

## Commands

```bash
python scripts/router.py default
python scripts/router.py classify "fix lint errors"
python scripts/router.py spawn --json "write a poem"
python scripts/router.py spawn --json --multi "fix bug and write poem"
python scripts/router.py models
```

## Config basics

Edit `config.json` to change routing:

- `default_model` = orchestrator default
- `routing_rules.<TIER>.primary` = main model for tier
- `routing_rules.<TIER>.fallback` = backups

## Security note

- This skill does not expose gateway secrets.
- Use `gateway-guard` separately for gateway/auth management.
