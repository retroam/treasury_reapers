# AGENTS.md

## Cursor Cloud specific instructions

### Overview

This is an **Arena Agent** config-only project for the Sentient AGI Arena "grounded-reasoning" competition. It contains no custom Python source code — all agent logic comes from pre-built harness agents orchestrated by the `arena` CLI via `arena.yaml`.

### Key files

- `arena.yaml` — Agent configuration (competition, model, environment settings)
- `pyproject.toml` — Python project metadata; declares `arena-sdk` dependency
- `cli.tar` — Arena CLI distribution (wheels + install script)

### Development environment setup

See `README.md` for standard arena commands (`arena doctor`, `arena test`, `arena submit`).

The arena CLI is installed from `cli.tar` via its bundled `install.sh` to `~/.arena/`. Add `~/.arena/bin` to `PATH`. Authenticate with `arena auth login --token "$ARENA_CLI_TOKEN"`.

### Docker in Cloud Agent VM (critical gotcha)

Docker runs in a nested Firecracker VM requiring:

1. **fuse-overlayfs** storage driver in `/etc/docker/daemon.json`
2. **iptables-legacy** alternatives (`update-alternatives --set iptables /usr/sbin/iptables-legacy`)
3. Start daemon: `sudo dockerd &>/tmp/dockerd.log &`
4. Fix socket permissions: `sudo chmod 666 /var/run/docker.sock`
5. **cgroup v2 workaround**: The `deploy.resources.limits` (memory/CPU) in harbor's `docker-compose-base.yaml` causes `cannot enter cgroupv2 ... with domain controllers -- it is in threaded mode`. Fix by removing the `deploy:` block from `~/.arena/venv/lib/python3.*/site-packages/harbor/environments/docker/docker-compose-base.yaml`. This must be reapplied after `arena pull` auto-updates the CLI.

### Arena CLI auto-update shebang bug

When `arena pull` auto-updates the CLI (e.g. 0.1.0 to 0.2.8), it renames `~/.arena/venv.new` to `~/.arena/venv` but the shebang in `~/.arena/venv/bin/arena` still references `venv.new`. Fix with:
```bash
sed -i 's|venv\.new|venv|g' ~/.arena/venv/bin/arena
```

### Model format for OpenRouter

The model in `arena.yaml` must be `openrouter/<model_id>`, e.g. `openrouter/qwen/qwen3-coder`. Using just `qwen/qwen3-coder` fails with "Unknown provider qwen" because the harness parses the first segment as the API provider. See the supported providers list in `harbor/agents/installed/opencode.py`.

### Required secrets

| Secret | Purpose |
|---|---|
| `OPENROUTER_API_KEY` | LLM inference via OpenRouter |
| `ARENA_CLI_TOKEN` | Arena platform auth (used with `arena auth login --token`) |

### Quick reference

```bash
# Install arena CLI from bundled tar
cd /workspace && mkdir -p .arena-cli-extract && tar xf cli.tar -C .arena-cli-extract
bash .arena-cli-extract/install.sh
export PATH="$HOME/.arena/bin:$PATH"

# Authenticate
arena auth login --token "$ARENA_CLI_TOKEN"

# Pull sample tasks
arena pull

# Fix harbor compose cgroup issue (after each CLI update)
sed -i '/deploy:/,/memory:.*/d' ~/.arena/venv/lib/python3.*/site-packages/harbor/environments/docker/docker-compose-base.yaml

# Fix shebang if arena command stops working after auto-update
sed -i 's|venv\.new|venv|g' ~/.arena/venv/bin/arena

# Validate setup
arena doctor

# Run tests
arena test --dry-run     # validate config only
arena test --smoke       # run 1 task
arena test --n 3         # run 3 tasks
```

### No lint/test/build

This project has no lint configuration, automated tests, or build step. Validation is done via `arena doctor` and `arena test`.
