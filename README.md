# nemesis8-pokeballs

Sealed container specs for [nemesis8](https://nemesis8.nuts.services). Each pokeball captures a project's runtime, dependencies, and security policy so you can run AI agents against it in isolation.

## Usage

```bash
# Pull a pokeball from the registry
nemesis8 pokeball pull hyperagents

# Build the sealed image locally
nemesis8 pokeball seal hyperagents

# Run AI against it — network isolated, read-only rootfs, all caps dropped
nemesis8 pokeball run hyperagents --prompt "analyze the architecture"
```

## Available Pokeballs

| Name | Language | Source | Description |
|------|----------|--------|-------------|
| [hyperagents](hyperagents/) | Python | [facebookresearch/hyperagents](https://github.com/facebookresearch/hyperagents) | Self-referential self-improving agents |

## Submitting a Pokeball

Capture any project and publish it:

```bash
nemesis8 pokeball capture https://github.com/user/repo
nemesis8 pokeball publish repo-name --description "what it does"
```

This creates a PR on this repo. Once merged, anyone can `nemesis8 pokeball pull` it.

Or submit manually — add a directory with a `pokeball.yaml` spec and open a PR.

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
