---
name: nile-no-install-linters
description: >-
  Trigger with "lint without installing" or "format without dependencies".
  Use when running linters, formatters, or type-checkers without installing them.
version: 1.0.0
license: MIT
tags: [linters, formatters, type-checkers, uvx, bunx, npx, ruff, eslint, prettier, mypy, no-install]
---

# NO-INSTALL-LINTERS : run linters / formatters / type-checkers without installing them

## Overview

This skill teaches how to use **all major "run it without pre-installing it" executors** across ecosystems to lint, format, and type-check code without adding project dependencies or global installs. Supported ecosystems:

- Node.js: `bunx`, `npx`/`npm exec`, `pnpm dlx`, `yarn dlx`, `deno run npm:...`
- Python: `uvx` (`uv tool run`), `pipx run`
- Deno built-ins: `deno lint`, `deno fmt`, `deno check` (no package install)
- Containers: `docker run --rm ...`
- Windows PowerShell module one-offs: `Save-Module` + `Import-Module` (temp path)

> Most of these **download + cache** somewhere (global cache, temp dir, tool cache), but they **don't require you to pre-install the tool as a project dependency or global app**.

## Prerequisites

- At least one "no-install" runner must be available on the system: `uvx`, `bunx`, `npx`, `pnpm`, `yarn`, `deno`, `docker`, or `pwsh`
- For Python tools (ruff, black, mypy, pyright, etc.): `uvx` (from uv) or `pipx` must be installed
- For Node.js tools (eslint, prettier, biome, etc.): `bunx`, `npx`, `pnpm`, or `yarn` must be installed
- For container-based tools: Docker must be installed and running
- The `smart_exec.py` script (included in `scripts/`) auto-detects available runners

## Instructions

1. **Identify the file type** you need to lint or format (Python, JS/TS, CSS, YAML, Dockerfile, etc.)
2. **Consult the runner table below** to find which tool handles that file type
3. **Pick the best available runner** from the columns marked `[OK]` for your tool
4. **Run the command** using the syntax shown in the table cell
5. **Alternatively**, use `smart_exec.py run <tool> [args]` to auto-detect the best runner

### All major "execute without installing" runners

| Ecosystem | Runner | What it runs | Typical syntax | Notes |
|---|---|---|---|---|
| Python | **uvx** (`uv tool run`) | Python CLI packages | `uvx TOOL [args...]` / `uvx TOOL@latest ...` / `uvx --from 'pkg==1.2.3' cmd ...` | Runs tools in an **ephemeral isolated env**; `uvx` is an alias for `uv tool run`. |
| Python | **pipx run** | Python CLI packages | `pipx run PACKAGE [args...]` | "npx-like" for Python CLIs; executes from a managed environment/cache. |
| Node.js | **npx** (or **npm exec**) | npm packages with binaries | `npx --yes pkg ...` / `npm exec --yes -- pkg ...` | `--yes` suppresses prompts. `npm exec` is the underlying command on modern npm. |
| Node.js | **pnpm dlx** | npm packages with binaries | `pnpm dlx pkg ...` / `pnpm dlx pkg@ver ...` | Fetches package **without adding it** as a dependency; runs its binary. |
| Node.js | **bunx** (`bun x`) | npm packages with binaries | `bunx pkg ...` | Bun's npx-equivalent. |
| Node.js | **yarn dlx** | npm packages with binaries | `yarn dlx pkg ...` / `yarn dlx -p pkg cmd ...` | Runs a package binary in a **temporary environment**; supports `-p/--package`. |
| Deno | **deno lint / fmt / check** | Deno built-in tools | `deno lint` / `deno fmt --check` / `deno check` | No package install; runs directly. Great baseline for Deno projects. |
| Deno | **deno run npm:** | npm package CLIs | `deno run -A npm:eslint@9 -- .` | Runs npm CLIs via `npm:` specifier without `npm install`. Some interactive CLIs may behave differently. |
| Containers | **docker run --rm** | Anything in a container | `docker run --rm ... image tool args` | Best "works anywhere" escape hatch; widely used in CI. |
| PowerShell (Windows) | **Save-Module + Import-Module (temp path)** | PowerShell modules | `Save-Module ... -Path $env:TEMP...; Import-Module <path>; <cmdlet>` | Downloads module **without installing into system module paths**; good for one-off runs. |



## CHOOSE THE BEST RUNNER. Here is a table with every tools + every "run without installing" executers

**Legend**
- [OK] = practical, common, and works
- [~] = works, but usually needs extra flags / awkward / less common
- [-] = not applicable

Commands are examples; replace `.` / globs / filenames as needed.

| Category | Language / Format | Tool | Kind | Package name (where applicable) | bunx | npx / npm exec | pnpm dlx | yarn dlx | deno (built-in) | deno (npm:) | uvx (Python) | pipx run (Python) | docker run --rm | Windows / PowerShell (no install) |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| Universal | JS/TS/JSON/CSS/HTML | **Biome** | linter+formatter | `@biomejs/biome` | [OK] `bunx @biomejs/biome check .` | [OK] `npx --yes @biomejs/biome check .` / `npm exec --yes -- @biomejs/biome check .` | [OK] `pnpm dlx @biomejs/biome check .` | [OK] `yarn dlx @biomejs/biome check .` | [-] | [OK] `deno run -A npm:@biomejs/biome@latest -- check .` | [-] | [-] | [~] | [-] |
| Universal | multi-doc | **Prettier** | formatter | `prettier` | [OK] `bunx prettier --check .` | [OK] `npx --yes prettier --check .` / `npm exec --yes -- prettier --check .` | [OK] `pnpm dlx prettier --check .` | [OK] `yarn dlx prettier --check .` | [-] | [OK] `deno run -A npm:prettier@latest -- --check .` | [-] | [-] | [~] | [-] |
| Web core | JS/TS | **ESLint** | linter | `eslint` | [OK] `bunx eslint .` | [OK] `npx --yes eslint .` / `npm exec --yes -- eslint .` | [OK] `pnpm dlx eslint .` | [OK] `yarn dlx eslint .` | [-] | [OK] `deno run -A npm:eslint@latest -- .` | [-] | [-] | [~] | [-] |
| Web core | JS/TS | **TypeScript (tsc)** | type check | `typescript` | [OK] `bunx tsc -p .` | [OK] `npx --yes tsc -p .` / `npm exec --yes -- tsc -p .` | [OK] `pnpm dlx tsc -p .` | [OK] `yarn dlx tsc -p .` | [-] | [OK] `deno run -A npm:typescript@latest -- tsc -p .` | [-] | [-] | [~] | [-] |
| Web core | JS/TS | **ts-node** | exec / checks | `ts-node` | [OK] `bunx ts-node file.ts` | [OK] `npx --yes ts-node file.ts` | [OK] `pnpm dlx ts-node file.ts` | [OK] `yarn dlx ts-node file.ts` | [-] | [~] | [-] | [-] | [-] | [-] |
| Web core | CSS | **stylelint** | linter | `stylelint` | [OK] `bunx stylelint "**/*.css"` | [OK] `npx --yes stylelint "**/*.css"` | [OK] `pnpm dlx stylelint "**/*.css"` | [OK] `yarn dlx stylelint "**/*.css"` | [-] | [OK] `deno run -A npm:stylelint@latest -- "**/*.css"` | [-] | [-] | [~] | [-] |
| Documents | Markdown | **markdownlint-cli2** | linter | `markdownlint-cli2` | [OK] `bunx markdownlint-cli2 "**/*.md"` | [OK] `npx --yes markdownlint-cli2 "**/*.md"` | [OK] `pnpm dlx markdownlint-cli2 "**/*.md"` | [OK] `yarn dlx markdownlint-cli2 "**/*.md"` | [-] | [OK] `deno run -A npm:markdownlint-cli2@latest -- "**/*.md"` | [-] | [-] | [~] | [-] |
| Documents | Text / Prose | **textlint** | linter | `textlint` | [OK] `bunx textlint file.txt` | [OK] `npx --yes textlint file.txt` | [OK] `pnpm dlx textlint file.txt` | [OK] `yarn dlx textlint file.txt` | [-] | [OK] `deno run -A npm:textlint@latest -- file.txt` | [-] | [-] | [~] | [-] |
| Data | JSON | **jsonlint** | linter/validator | `jsonlint` | [OK] `bunx jsonlint data.json` | [OK] `npx --yes jsonlint data.json` | [OK] `pnpm dlx jsonlint data.json` | [OK] `yarn dlx jsonlint data.json` | [-] | [OK] `deno run -A npm:jsonlint@latest -- data.json` | [-] | [-] | [~] | [-] |
| Data | YAML | **yaml-lint** (Node) | linter | `yaml-lint` | [OK] `bunx yaml-lint file.yaml` | [OK] `npx --yes yaml-lint file.yaml` | [OK] `pnpm dlx yaml-lint file.yaml` | [OK] `yarn dlx yaml-lint file.yaml` | [-] | [OK] `deno run -A npm:yaml-lint@latest -- file.yaml` | [-] | [-] | [~] | [-] |
| Data | YAML | **yamllint** (Python) | linter | `yamllint` (PyPI) | [-] | [-] | [-] | [-] | [-] | [-] | [OK] `uvx yamllint .` | [OK] `pipx run yamllint .` | [~] | [-] |
| API | OpenAPI | **Spectral** | linter | `@stoplight/spectral` | [OK] `bunx @stoplight/spectral lint api.yaml` | [OK] `npx --yes @stoplight/spectral lint api.yaml` | [OK] `pnpm dlx @stoplight/spectral lint api.yaml` | [OK] `yarn dlx @stoplight/spectral lint api.yaml` | [-] | [OK] `deno run -A npm:@stoplight/spectral@latest -- lint api.yaml` | [-] | [-] | [~] | [-] |
| Project | package.json | **npm-package-json-lint** | linter | `npm-package-json-lint` | [OK] `bunx npm-package-json-lint .` *(see note below)* | [OK] `npx --yes npm-package-json-lint .` *(see note below)* | [OK] `pnpm dlx npm-package-json-lint .` *(see note below)* | [OK] `yarn dlx npm-package-json-lint .` *(see note below)* | [-] | [OK] `deno run -A npm:npm-package-json-lint@latest -- .` | [-] | [-] | [~] | [-] |
| Project | package.json | **sort-package-json** | formatter/sorter | `sort-package-json` | [OK] `bunx sort-package-json "package.json"` | [OK] `npx --yes sort-package-json "package.json"` | [OK] `pnpm dlx sort-package-json "package.json"` | [OK] `yarn dlx sort-package-json "package.json"` | [-] | [OK] `deno run -A npm:sort-package-json@latest -- "package.json"` | [-] | [-] | [~] | [-] |
| Python | Python | **Ruff** | linter+formatter | `ruff` (PyPI) | [-] | [-] | [-] | [-] | [-] | [-] | [OK] `uvx ruff@latest check .` / `uvx ruff@latest format .` | [OK] `pipx run ruff check .` | [~] | [-] |
| Python | Python | **Black** | formatter | `black` (PyPI) | [-] | [-] | [-] | [-] | [-] | [-] | [OK] `uvx black .` | [OK] `pipx run black .` | [~] | [-] |
| Python | Python | **isort** | formatter | `isort` (PyPI) | [-] | [-] | [-] | [-] | [-] | [-] | [OK] `uvx isort .` | [OK] `pipx run isort .` | [~] | [-] |
| Python | Python | **mypy** | type checker | `mypy` (PyPI) | [-] | [-] | [-] | [-] | [-] | [-] | [OK] `uvx mypy .` | [OK] `pipx run mypy .` | [~] | [-] |
| Python | Python | **pyright** (Python CLI) | type checker | `pyright` (PyPI) | [-] | [-] | [-] | [-] | [-] | [-] | [OK] `uvx pyright` | [OK] `pipx run pyright` | [~] | [-] |
| SQL | SQL | **sqlfluff** | linter+formatter | `sqlfluff` (PyPI) | [-] | [-] | [-] | [-] | [-] | [-] | [OK] `uvx sqlfluff lint file.sql` / `uvx sqlfluff fix file.sql` | [OK] `pipx run sqlfluff lint file.sql` | [~] | [-] |
| DevOps | Shell | **ShellCheck** | linter | npm: `shellcheck` / native | [OK] `bunx shellcheck script.sh` | [OK] `npx --yes shellcheck script.sh` | [OK] `pnpm dlx shellcheck script.sh` | [OK] `yarn dlx shellcheck script.sh` | [-] | [OK] `deno run -A npm:shellcheck@latest -- script.sh` | [~] (PyPI wrappers exist; depends on wrapper) | [~] | [OK] `docker run --rm -v "$PWD:/w" -w /w koalaman/shellcheck:stable shellcheck script.sh` | [OK] via Docker/WSL; otherwise use native install |
| DevOps | Dockerfile | **Hadolint** | linter | npm: `hadolint` / native | [OK] `bunx hadolint Dockerfile` | [OK] `npx --yes hadolint Dockerfile` | [OK] `pnpm dlx hadolint Dockerfile` | [OK] `yarn dlx hadolint Dockerfile` | [-] | [OK] `deno run -A npm:hadolint@latest -- Dockerfile` | [~] (PyPI wrappers exist) | [~] | [OK] `docker run --rm -i hadolint/hadolint < Dockerfile` | [OK] via Docker |
| DevOps | GitHub Actions | **actionlint** | linter | (Go binary; wrappers exist) | [~] | [~] | [~] | [~] | [-] | [~] | [~] | [~] | [OK] common via container in CI | [OK] via Docker; or download binary |
| DevOps | Terraform | **tflint** | linter | (Go binary; wrappers exist) | [~] | [~] | [~] | [~] | [-] | [~] | [~] | [~] | [OK] `docker run --rm -v "$PWD:/data" -w /data ghcr.io/terraform-linters/tflint ...` | [OK] via Docker; or download binary |
| DevOps | Terraform | **terraform fmt** | formatter | Terraform CLI | [-] | [-] | [-] | [-] | [-] | [-] | [-] | [-] | [OK] `docker run --rm -v "$PWD:/w" -w /w hashicorp/terraform:light fmt -check -recursive` | [OK] via Docker; or download terraform |
| HTML | HTML | **htmlhint** | linter | `htmlhint` | [OK] `bunx htmlhint "**/*.html"` | [OK] `npx --yes htmlhint "**/*.html"` | [OK] `pnpm dlx htmlhint "**/*.html"` | [OK] `yarn dlx htmlhint "**/*.html"` | [-] | [OK] `deno run -A npm:htmlhint@latest -- "**/*.html"` | [-] | [-] | [~] | [-] |
| HTML | HTML | **tidy** (tidy-html5) | linter/formatter | native (`tidy`) | [-] | [-] | [-] | [-] | [-] | [-] | [-] | [-] | [OK] example: `docker run --rm -v "$PWD:/w" -w /w alpine sh -lc "apk add --no-cache tidyhtml && tidy -errors -q file.html"` | [OK] via Docker |
| XML | XML | **xmllint** | linter/validator | native (`libxml2`) | [-] | [-] | [-] | [-] | [-] | [-] | [-] | [-] | [OK] `docker run --rm -v "$PWD:/w" -w /w alpine sh -lc "apk add --no-cache libxml2-utils && xmllint --noout file.xml"` | [OK] via Docker |
| Deno | JS/TS | **deno lint** | linter | built-in | [-] | [-] | [-] | [-] | [OK] `deno lint` | [-] | [-] | [-] | [~] | [OK] (native) |
| Deno | JS/TS/JSON/MD | **deno fmt** | formatter | built-in | [-] | [-] | [-] | [-] | [OK] `deno fmt --check` | [-] | [-] | [-] | [~] | [OK] (native) |
| Deno | TS | **deno check** | type check | built-in | [-] | [-] | [-] | [-] | [OK] `deno check **/*.ts` | [-] | [-] | [-] | [~] | [OK] (native) |
| PowerShell | PowerShell | **PSScriptAnalyzer** | linter | PowerShell module | [-] | [-] | [-] | [-] | [-] | [-] | [-] | [-] | [~] (possible) | [OK] (see template below) |

---

## Notes on quirks and peculiarities of the tools

### `npm-package-json-lint`: command name vs package name
The **package name** is `npm-package-json-lint`, but the **CLI command** documented by the project is typically:

```bash
npmPkgJsonLint .
```

When you run via `bunx`/`npx`/`pnpm dlx`/`yarn dlx`, you can reliably do:

```bash
npx --yes npm-package-json-lint -- npmPkgJsonLint .
# or:
pnpm dlx npm-package-json-lint npmPkgJsonLint .
```

(If `bunx npm-package-json-lint .` works in your environment, keep it -- but the `npmPkgJsonLint` form is the official CLI name.)

### YAML: Node vs Python options
- `yaml-lint` exists on npm (works with bunx/npx/pnpm dlx/yarn dlx/deno npm:).
- `yamllint` is a common Python YAML linter (works with `uvx`/`pipx run`).

### "No install" does not mean "no download"
Even "instant-run" tools download packages/binaries to caches (npm cache, uv cache, pipx venv cache, Docker image cache, etc.). The goal here is: **no global install and no project dependency changes**.

---

## PowerShell "npx-like" one-off runner template (Windows)

This is the most reliable "no-install" approach for PowerShell-module-based linters:

```powershell
$dir = Join-Path $env:TEMP ("psmods_" + [guid]::NewGuid().ToString("n"))
Save-Module -Name PSScriptAnalyzer -Path $dir -Force

# Resolve the module path (works across versions):
$psd1 = Get-ChildItem -Path (Join-Path $dir "PSScriptAnalyzer") -Recurse -Filter "PSScriptAnalyzer.psd1" |
  Select-Object -First 1 -ExpandProperty FullName
Import-Module $psd1 -Force

Invoke-ScriptAnalyzer -Path . -Recurse
```

`Save-Module` saves to a path and **isn't installed** into system module paths.

# Provided script to automate the choice of runners
For common tools, you can use the provided python script to automate the choice of runners from those available on the user system.
Run it from the scripts subdir inside this skill directory:
```
./scripts/smart_exec.py
```
smart_exec.py is a "smart runner" that:
- Detects available executors on this machine (uvx/uv, pipx, bunx/bun x, pnpm dlx, npx, npm exec, yarn dlx, deno, docker, pwsh/powershell)
- Chooses the best executor for a requested tool (linter/formatter/type-checker/etc.)
- Executes it via subprocess, preserving exit code

Notes:
- Uses Bun's documented `-p/--package` support when binary name != package name (e.g. @stoplight/spectral -> spectral).  (see: https://bun.com/docs/pm/bunx)
- Uses npm's recommended `npm exec --package=... -- <cmd>` form.  (see: https://docs.npmjs.com/cli/v8/commands/npm-exec)
- Supports Deno built-ins (`deno lint/fmt/check`) as truly "no install" tools, plus `deno run npm:` for npm CLIs
- Support PowerShell "download to temp + import" execution for module-based tools (e.g. PSScriptAnalyzer)
- Support special commands: `executors`, `db`, and `which` subcommands + JSON output + dry-run mode
- "No install" here means: no project dependency changes and no global install of the tool itself.
  Executors may still download/cache packages/binaries (npm cache, uv cache, docker images, etc.).
- Heuristics are adjustable: edit TOOL_DB and PRIORITY.

Usage Examples:
```
  smart_exec.py executors
  smart_exec.py db
  smart_exec.py which ruff check .
  smart_exec.py run ruff check .
  smart_exec.py run eslint .
  smart_exec.py run prettier --check .
  smart_exec.py run npm-package-json-lint .
  smart_exec.py run deno-fmt -- --check
  smart_exec.py run Invoke-ScriptAnalyzer -- -Path . -Recurse
```

## Output

All linters, formatters, and type-checkers produce output on **stdout/stderr** with a non-zero exit code on failure. Typical output patterns:

- **Linters** (eslint, ruff check, shellcheck): list of diagnostics with file, line number, rule ID, and message. Exit code 1 means issues found.
- **Formatters** (prettier --check, ruff format --check, biome check): list of files that would be reformatted. Exit code 1 means files need formatting.
- **Type checkers** (mypy, pyright, tsc --noEmit): list of type errors with file, line, and description. Exit code 1 means type errors found.
- **smart_exec.py**: preserves the tool's original exit code and output. With `--json` flag, wraps the result in a JSON envelope containing `executor`, `command`, `exit_code`, and `output`.

## Error Handling

- If **no runner is available** for the requested tool, `smart_exec.py` exits with an error message listing which runners it looked for and why each was unavailable.
- If the **tool package does not exist** in the registry (npm, PyPI), the runner itself will print a "not found" error. Check the package name in the runner table above.
- If a **runner times out** or hangs, kill the process and try a different runner from the table.
- If `bunx` fails with permission errors, try `npx --yes` instead (some systems restrict bun's cache directory).
- If `uvx` cannot resolve a tool, ensure `uv` is version 0.4+ (older versions lack `uvx` alias support).
- If `docker run` fails, check that the Docker daemon is running (`docker info`) and that the image exists.

## Examples

### Example 1: Lint Python files with ruff (no install)

```bash
uvx ruff@latest check .
```

### Example 2: Format JavaScript with prettier (no install)

```bash
bunx prettier --write "src/**/*.js"
```

### Example 3: Type-check TypeScript (no install)

```bash
npx --yes tsc --noEmit -p tsconfig.json
```

### Example 4: Lint a Dockerfile with hadolint via docker

```bash
docker run --rm -i hadolint/hadolint < Dockerfile
```

### Example 5: Use smart_exec.py to auto-pick the runner

```bash
./scripts/smart_exec.py run ruff check .
./scripts/smart_exec.py run eslint src/
./scripts/smart_exec.py which prettier --check .
```

### Example 6: Lint shell scripts with ShellCheck (no install)

```bash
npx --yes shellcheck script.sh
```

## Workflow Checklist

Copy this checklist and track your progress when running a linter, formatter, or type-checker without installing it:

- [ ] Identify the tool to run (e.g. ruff, eslint, prettier, mypy, shellcheck)
- [ ] Determine the tool's ecosystem (Python, Node.js, Deno, native, container)
- [ ] Check which runners are available on the system (uvx, bunx, npx, pnpm dlx, yarn dlx, deno, docker, pwsh)
- [ ] Consult the runner table above to find the best runner for the tool
- [ ] Run the tool using the chosen runner's syntax from the table
- [ ] Alternatively, use `smart_exec.py run <tool> [args]` to auto-detect the best runner
- [ ] Review the tool's output (diagnostics, formatting diffs, type errors)
- [ ] Fix any reported issues in the source files
- [ ] Re-run the tool to confirm all issues are resolved (exit code 0)

## Resources

- `uvx` is an alias of `uv tool run`: https://docs.astral.sh/uv/concepts/tools/
- `bunx`: https://bun.com/docs/pm/bunx
- `npx` / `npm exec`: https://docs.npmjs.com/cli/v8/commands/npx
- `pnpm dlx`: https://pnpm.io/cli/dlx
- `yarn dlx`: https://yarnpkg.com/cli/dlx
- Deno lint/format: https://docs.deno.com/runtime/fundamentals/linting_and_formatting/
- `Save-Module` (PowerShellGet): https://learn.microsoft.com/en-us/powershell/module/powershellget/save-module?view=powershellget-2.x
