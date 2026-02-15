# no-install-linters-expert

A Claude Code plugin that teaches how to run linters, formatters, and type-checkers **without installing them** as project dependencies or global tools. Uses ephemeral "no-install" runners like `uvx`, `bunx`, `npx`, `pipx run`, `pnpm dlx`, `yarn dlx`, `deno run`, and `docker run`.

## What It Does

This plugin provides:

1. **Expert knowledge** of all major "run without installing" executors across Python, Node.js, Deno, container, and PowerShell ecosystems
2. **A smart executor script** (`smart_exec.py`) that auto-detects available runners on your system and picks the best one for any given tool
3. **An expert agent** that can help you lint, format, and type-check code without adding any project dependencies

The core principle: **no global installs, no project dependency changes**. Runners download and cache packages ephemerally.

## Installation

### From Marketplace

```bash
claude plugin marketplace add https://github.com/Emasoft/emasoft-plugins
claude plugin install no-install-linters-expert@emasoft-plugins --scope user
```

### For Development

```bash
claude --plugin-dir ./no-install-linters-expert
```

## Components

| Type | Name | Description |
|------|------|-------------|
| Agent | `nile-no-install-linters-agent` | Expert agent for no-install linting workflows |
| Skill | `nile-no-install-linters` | Complete reference for all runners and tool compatibility |
| Script | `smart_exec.py` | CLI tool that detects executors and runs tools with the best one |

## Supported Runners

| Ecosystem | Runner | Syntax |
|-----------|--------|--------|
| Python | **uvx** (uv tool run) | `uvx TOOL [args...]` |
| Python | **pipx run** | `pipx run PACKAGE [args...]` |
| Node.js | **bunx** (bun x) | `bunx pkg [args...]` |
| Node.js | **npx** (npm exec) | `npx --yes pkg [args...]` |
| Node.js | **pnpm dlx** | `pnpm dlx pkg [args...]` |
| Node.js | **yarn dlx** | `yarn dlx pkg [args...]` |
| Deno | **deno run npm:** | `deno run -A npm:pkg@latest -- [args...]` |
| Deno | **deno lint/fmt/check** | `deno lint` (built-in, no install) |
| Containers | **docker run --rm** | `docker run --rm -v "$PWD:/w" -w /w image tool [args...]` |
| PowerShell | **Save-Module + Import-Module** | Temp-path pattern for module-based tools |

## Supported Tools

| Category | Tool | Kind | Python Ecosystem | Node.js Ecosystem |
|----------|------|------|-----------------|-------------------|
| Python | **Ruff** | linter+formatter | uvx, pipx run | - |
| Python | **Black** | formatter | uvx, pipx run | - |
| Python | **isort** | import sorter | uvx, pipx run | - |
| Python | **mypy** | type checker | uvx, pipx run | - |
| Python | **pyright** | type checker | uvx, pipx run | - |
| Python | **sqlfluff** | SQL linter+formatter | uvx, pipx run | - |
| Python | **yamllint** | YAML linter | uvx, pipx run | - |
| JS/TS | **ESLint** | linter | - | bunx, npx, pnpm dlx, yarn dlx |
| JS/TS | **Prettier** | formatter | - | bunx, npx, pnpm dlx, yarn dlx |
| JS/TS | **Biome** | linter+formatter | - | bunx, npx, pnpm dlx, yarn dlx |
| JS/TS | **TypeScript (tsc)** | type checker | - | bunx, npx, pnpm dlx, yarn dlx |
| CSS | **stylelint** | linter | - | bunx, npx, pnpm dlx, yarn dlx |
| HTML | **htmlhint** | linter | - | bunx, npx, pnpm dlx, yarn dlx |
| Markdown | **markdownlint-cli2** | linter | - | bunx, npx, pnpm dlx, yarn dlx |
| JSON | **jsonlint** | linter | - | bunx, npx, pnpm dlx, yarn dlx |
| YAML | **yaml-lint** (Node) | linter | - | bunx, npx, pnpm dlx, yarn dlx |
| Shell | **ShellCheck** | linter | - | bunx, npx + docker fallback |
| Dockerfile | **Hadolint** | linter | - | bunx, npx + docker fallback |
| OpenAPI | **Spectral** | linter | - | bunx, npx, pnpm dlx, yarn dlx |
| PowerShell | **PSScriptAnalyzer** | linter | - | - (PowerShell native) |

## Usage Examples

### Using smart_exec.py

```bash
# List available executors on your system
uv run smart_exec.py executors

# Show the built-in tool database
uv run smart_exec.py db

# Check which executor would be used (without running)
uv run smart_exec.py which ruff check .

# Run a tool using the best available executor
uv run smart_exec.py run ruff check .
uv run smart_exec.py run eslint .
uv run smart_exec.py run prettier --check .
uv run smart_exec.py run shellcheck script.sh
```

### Direct runner commands

```bash
# Python tools via uvx
uvx ruff@latest check .
uvx black --check .
uvx mypy .

# Node.js tools via bunx
bunx prettier --check .
bunx eslint .
bunx @biomejs/biome check .

# Node.js tools via npx
npx --yes prettier --check .
npx --yes eslint .

# Docker fallback
docker run --rm -v "$PWD:/w" -w /w koalaman/shellcheck:stable shellcheck script.sh
```

## License

MIT License - Copyright (c) 2026 Emasoft
