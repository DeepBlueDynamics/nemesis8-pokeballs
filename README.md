# nemesis8-pokeballs

Sealed container specs for [nemesis8](https://nemesis8.nuts.services).

A pokeball captures a project's runtime, dependencies, and security policy so you can run AI agents against it in isolation.

## What's a Pokeball?

nemesis8 ships with four built-in providers: **Codex**, **Gemini**, **Claude Code**, and **OpenClaw**. These are just pokeballs that come pre-installed — same pattern, same container isolation, same tool injection.

A pokeball is any AI environment you can capture, seal, and run. The built-in ones are shorthand for the most common CLIs. Everything in this registry is a pokeball anyone can make. Name it something cool.

## Usage

```bash
# Pull a pokeball from the registry
nemesis8 pokeball pull hyperagents

# Build the sealed image locally
nemesis8 pokeball seal hyperagents

# Run AI against it — network isolated, read-only rootfs, all caps dropped
nemesis8 pokeball run hyperagents --prompt "analyze the architecture"
```

## Built-in Providers

These ship with nemesis8 and don't need a pokeball:

| Provider | Command | What it is |
|----------|---------|------------|
| Codex | `nemesis8 interactive` | OpenAI Codex CLI |
| Gemini | `nemesis8 --provider gemini interactive` | Google Gemini CLI |
| Claude | `nemesis8 --provider claude interactive` | Anthropic Claude Code |
| OpenClaw | `nemesis8 --provider openclaw interactive` | OpenClaw agent platform |

Same container, same 69 MCP tools, same session persistence. Just different brains.

## Community Pokeballs

| Name | Language | Source | Description |
|------|----------|--------|-------------|
| [hyperagents](hyperagents/) | Python | [facebookresearch/hyperagents](https://github.com/facebookresearch/hyperagents) | Self-referential self-improving agents |
| [trustgraph](trustgraph/) | Python | [trustgraph-ai/trustgraph](https://github.com/trustgraph-ai/trustgraph) | Graph-native knowledge platform with MCP tools |

## Make Your Own

Capture any project and publish it:

```bash
nemesis8 pokeball capture https://github.com/user/repo
nemesis8 pokeball publish repo-name --description "what it does"
```

This creates a PR on this repo. Once merged, anyone can pull it. Or submit manually — add a directory with a `pokeball.yaml` spec and open a PR.

## Spec Format

```yaml
apiVersion: pokeball/v1
metadata:
  name: project-name
  description: what this project does
source:
  git:
    url: https://github.com/org/repo
runtime:
  base_image: python:3.12-slim-bookworm
  language: python
  package_manager: pip
build:
  install_cmd: pip install -r requirements.txt
  system_packages: [git, curl, tini]
provider:
  name: anthropic
  model: claude-sonnet-4-20250514
  api_key_env: ANTHROPIC_API_KEY
tools:
  allow:
    - name: bash
    - name: file_read
    - name: file_write
    - name: grep
    - name: glob
security:
  network:
    policy: deny
  filesystem:
    read_only_root: true
    work_mount: rw
  process:
    user: pokeball
    no_new_privileges: true
    drop_capabilities: [ALL]
resources:
  memory_mb: 4096
  pids_limit: 256
  timeout_minutes: 60
```

## Security

Every pokeball runs in a hardened container:
- **Network disabled** — no internet access
- **Read-only root filesystem** — can't modify system files
- **All capabilities dropped** — no privilege escalation
- **Non-root user** — runs as `pokeball`
- **Resource limits** — 4GB RAM, 256 PIDs, 60 minute timeout

AI model access goes through a broker on the host — the container never touches the network.

---

[nemesis8](https://github.com/DeepBlueDynamics/nemesis8) | [Website](https://nemesis8.nuts.services) | [Deep Blue Dynamics](https://github.com/DeepBlueDynamics)
