# Command Usage Rules

When to use each slash command and what it does.

---

## /git-commit

**Use when:**
- Asking Claude to commit with the correct message format
- Verifying branch before committing
- Formatting a commit message that follows project conventions

**What it does:**
1. Checks current branch — creates a feature branch first if on `main`/`master`
2. Stages only files belonging to the same change type
3. Writes commit message in `<type>: <description>` + `subagent: <agent>` format

**Accepts argument:** branch name or commit description (optional)

---

## /c-coding-standard

**Use when:**
- Asking Claude to review and fix C code in scope against the coding standard
- Before committing `.c`/`.h` files
- After receiving code from an external source that needs to be normalized

**What it does:**
- Checks all 13 rule categories in the c-coding-standard skill
- Applies fixes and presents a checklist before returning

**Accepts argument:** file name or module to check (optional)

---

## /c-doxygen-standard

**Use when:**
- Adding or fixing Doxygen comments in `.c`/`.h` files
- Before committing `.c`/`.h` files
- After adding a new function, struct, or enum

**What it does:**
1. Asks user for `@author` name
2. Applies file header, function doc, struct/enum/macro doc per all rules
3. Presents a checklist before returning

**Accepts argument:** file name or module to apply (optional)

---

## /markdown-converter

**Output directory:** Always save converted files to `output/` (relative to project root). Create `output/` if it does not exist.

**What it does:**
- `any-to-md` — converts PDF / DOCX / PPTX / image / URL → Markdown
- `md-to-html` — renders Markdown → styled HTML (themes: article, report, reading, interactive)
- `html-to-md` — extracts clean Markdown from HTML file or URL
- `md-to-docx` — generates Word document from Markdown

**Example:**
```bash
python .claude/skills/markdown-converter/convert.py any-to-md report.pdf -o output/report.md
```

---

## /script-generator

**Use when:**
- Writing any shell or PowerShell script (`.sh`, `.bash`, `.zsh`, `.ps1`)
- Automating build, deploy, test, or maintenance tasks
- Modifying an existing script
- Generating cross-platform scripts (Bash + PowerShell pair)

**What it does:**
1. Confirms the target environment (Linux Bash, macOS Zsh, POSIX sh, PowerShell 5.1/7, Git Bash, or cross-platform)
2. Confirms the output path
3. Delegates to **script-developer** agent — never generates inline
4. Enforces safety boilerplate (`set -euo pipefail` or `$ErrorActionPreference = 'Stop'`)
5. Adds dependency-check block for all external tools used
6. Sets executable bit for Bash scripts (`chmod +x`)
7. Commits via `/git-commit`

**Accepts argument:** free-text description of the script to generate (optional — agent will ask if not provided)

**Examples:**
```
/script-generator deploy the staging Docker image via SSH
/script-generator cross-platform clean.sh and clean.ps1 that remove build artifacts
/script-generator PowerShell script to set up the dev environment on Windows
```

---

## Order before committing

```
/c-coding-standard → /c-doxygen-standard → /git-commit
```
