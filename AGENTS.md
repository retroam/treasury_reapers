# AGENTS.md

## Cursor Cloud specific instructions

### Overview

This is an **Arena Agent** config-only project for the [Sentient AGI Arena](https://github.com/sentient-agi) "grounded-reasoning" competition. It contains no custom Python source code — all agent logic comes from pre-built harness agents orchestrated by the `arena` CLI via `arena.yaml`.

### Key files

- `arena.yaml` — Agent configuration (competition, model, environment settings)
- `pyproject.toml` — Python project metadata; declares `arena-sdk` dependency

### Runtime dependencies

| Dependency | Purpose | Status |
|---|---|---|
| Python 3.11 | Required runtime (see `.python-version`) | Installed via `uv python install 3.11` |
| `uv` | Package manager | Installed at `~/.local/bin/uv` |
| Docker | Runs agent tasks in isolated containers | Installed; requires `sudo dockerd` to start daemon |
| `arena` CLI | Orchestrates test runs, submissions | **Not publicly available** — from private `sentient-agi/arena` repo |
| `arena-sdk` | Python SDK dependency | **Not publicly available** — `uv sync` fails because `github.com/sentient-agi/arena.git` is inaccessible |
| `OPENROUTER_API_KEY` | LLM inference via OpenRouter | Must be set as env var (configured as a Cursor secret) |

### Docker in Cloud Agent VM

Docker runs inside a Firecracker VM, requiring special setup:

1. `fuse-overlayfs` storage driver (`/etc/docker/daemon.json`)
2. `iptables-legacy` alternatives
3. Start daemon with `sudo dockerd &>/tmp/dockerd.log &`

### Gotchas

- `uv sync` will fail until the `sentient-agi/arena` Git repository becomes publicly accessible or credentials are configured. The venv can still be created with `uv venv --python 3.11`.
- The `arena` CLI referenced in `README.md` (`arena auth login`, `arena doctor`, `arena test`) is **not** the `cli-arena` PyPI package. They are different tools.
- No lint, test, or build commands exist in this project — it is purely configuration.
- The `pyproject.toml` uses `[tool.uv.sources]` for git-based `arena-sdk`; standard `pip install` cannot resolve it.

### Quick reference

```bash
# Create/refresh Python 3.11 venv
uv venv --python 3.11 --clear

# Attempt dependency install (requires arena repo access)
uv sync

# Start Docker daemon (Cloud VM)
sudo dockerd &>/tmp/dockerd.log &

# Validate arena.yaml
python -c "import yaml; yaml.safe_load(open('arena.yaml'))"

# Run arena commands (requires arena CLI)
arena doctor
arena test --dry-run
arena test --smoke
```
