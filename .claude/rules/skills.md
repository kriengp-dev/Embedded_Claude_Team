# Skill Usage Rules

When to use each skill, in what order, and under what conditions.

---

## git-commit

**Use when:**
- Before every `git commit`
- Before opening a Pull Request
- Unsure whether to create a feature branch or commit on the current branch

**Key rules enforced by this skill:**
- Never commit directly to `main` or `master`
- `subagent:` must be on its own line below the subject
- Split commits by type: docs / code / test — never mix in one commit

**Order:** Always call after c-coding-standard and c-doxygen-standard.

---

## c-coding-standard

**Use when:**
- Writing or modifying any `.c` or `.h` file
- After c-developer writes code, before sending for review
- During code review to check compliance

**Covers:** naming, file structure, types, functions, variables, constants,
control flow, memory, pointers, integers, interrupts, comments, includes

**Order:** Call before git-commit and alongside c-doxygen-standard.

---

## c-doxygen-standard

**Use when:**
- Writing or modifying any `.c` or `.h` file
- Adding a new public function, struct, enum, or macro
- During code review to check documentation quality

**Requirement:** Must ask user for `@author` name before applying.

**Order:** Call alongside c-coding-standard — both must be invoked before git-commit.

---

## script-generator

**Use when:**
- Writing any new shell or PowerShell script (`.sh`, `.bash`, `.zsh`, `.ps1`)
- Automating a build, deploy, test, or maintenance task
- Modifying an existing script
- Creating cross-platform scripts (Bash + PowerShell pair)
- Any prompt that asks to "write a script", "automate X", or names a target shell environment

**What it does:**
1. Confirms target environment (Linux Bash, macOS Zsh, POSIX sh, PowerShell 5.1/7, Git Bash, cross-platform)
2. Confirms output path
3. Delegates to **script-developer** agent — never generates scripts inline
4. Enforces safety flags (`set -euo pipefail` / `$ErrorActionPreference = 'Stop'`)
5. Invokes `/git-commit` after the script is written

**Order:** Always call `/git-commit` after the agent completes.

---

## Mandatory order before committing C code

```
c-coding-standard → c-doxygen-standard → git-commit
```

All three must be invoked in every session that modifies `.c` or `.h` files — no exceptions.
