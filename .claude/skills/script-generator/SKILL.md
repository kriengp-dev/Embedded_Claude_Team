---
name: script-generator
description: Shell/Bash script generation workflow for any target environment (Linux, macOS, WSL, Windows Git Bash, PowerShell). Delegates to the script-developer agent. Use when a shell script or PowerShell script needs to be created or modified.
---

## When to Use

- Writing any new shell script (`.sh`, `.bash`, `.zsh`, `.ps1`)
- Automating a build, deploy, test, or maintenance task via script
- Modifying an existing script to fix bugs or add features
- Generating cross-platform scripts that run on both Unix and Windows
- Any prompt that asks to "write a script", "automate X", or "create a helper"

---

## Workflow

### Mandatory: Delegate to script-developer Agent

**Always launch the script-developer agent. Never generate scripts inline.**

Launch **script-developer** agent with:
- Task description from `$ARGUMENTS`
- Any known target environment (pass it in the prompt if stated by the user)
- Any known output path (pass it if stated by the user)

The agent will:
1. Confirm the target environment (or ask once if not specified)
2. Confirm the output path (or ask once if not specified)
3. Explore the repo for existing scripts to reuse or extend
4. Design the script structure
5. Write the script following environment-specific best practices
6. Self-review against the safety checklist
7. Commit via `/git-commit`

---

## Environment Quick Reference

| Environment | Shebang / Header | Safety flags |
|---|---|---|
| Linux / WSL Bash | `#!/usr/bin/env bash` | `set -euo pipefail` |
| macOS Zsh | `#!/usr/bin/env zsh` | `set -euo pipefail` |
| Windows Git Bash | `#!/usr/bin/env bash` | `set -euo pipefail` |
| POSIX sh | `#!/bin/sh` | `set -eu` (no `pipefail` in sh) |
| PowerShell 5.1 | `#Requires -Version 5.1` | `Set-StrictMode -Version Latest` + `$ErrorActionPreference = 'Stop'` |
| PowerShell 7+ | `#Requires -Version 7.0` | `Set-StrictMode -Version Latest` + `$ErrorActionPreference = 'Stop'` |
| Cross-platform | Both `.sh` + `.ps1` | Apply both sets of flags |

---

## Steps the Agent Must Follow

| Step | Action |
|------|--------|
| 0 | Confirm target environment (ask once if not provided) |
| 1 | Confirm output path (ask once if not provided) |
| 2 | Explore repo for existing scripts and reusable helpers |
| 3 | Design script structure (outline functions, flags, dependencies) |
| 4 | Write the script using the correct template and safety flags |
| 5 | Add dependency check block for all external tools used |
| 6 | Handle cross-platform case — produce `.sh` + `.ps1` pair if ENV=7 |
| 7 | Set executable bit (`chmod +x`) for Bash scripts |
| 8 | Self-review (quoting, exit codes, stderr, traps, injection safety) |
| 9 | Commit via `/git-commit` skill |

---

## Mandatory Pre-Commit Gate

After the agent completes, before any commit:

1. Resolve the project git repo root:
   ```bash
   git -C "<script-output-path>" rev-parse --show-toplevel
   ```
2. **Invoke `Skill: git-commit`** — branch rules, commit message format, PR process

---

## Commit Format

```
<type>: <description>
subagent: script-developer
```

Valid types: `feat`, `fix`, `chore`, `refactor`, `docs`

**Examples:**
```
feat: add deploy.sh for automated staging deployment
subagent: script-developer

fix: handle missing AWS_REGION in build-image.sh
subagent: script-developer

chore: add cross-platform clean.sh and clean.ps1
subagent: script-developer
```

---

## Key Principles

1. **Agent-first** — never generate scripts inline; always delegate to script-developer
2. **Environment-explicit** — always confirm the target shell/OS before writing
3. **Defensive always** — `set -euo pipefail` / `$ErrorActionPreference = 'Stop'` — no exceptions
4. **No silent failures** — every command that can fail must be checked
5. **Portable within scope** — write to the idioms of the chosen environment; do not mix bash-isms into sh scripts
6. **Idempotent where possible** — running twice should not break anything
