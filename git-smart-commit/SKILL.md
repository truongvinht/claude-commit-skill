---
name: git-smart-commit
description: >
  Generates well-formatted Conventional Commits messages based on the current
  branch name and actual code changes. Parses branch pattern {type}/{ticket-id}
  to extract commit type and ticket ID automatically. Suppresses Co-authored-by
  and other trailers.
triggers:
  - /commit
  - /git-commit
  - /smart-commit
---

# Git Smart Commit Skill

Generate a git commit by analyzing the current branch name and staged/unstaged changes.

## Steps

1. **Get current branch name**
   ```bash
   git branch --show-current
   ```

2. **Parse branch to extract type and ticket ID**

   Branch pattern: `{type}/{ticket-id}` or `{type}/{ticket-id}-optional-slug`

   | Branch | Type | Ticket |
   |--------|------|--------|
   | `feat/TRAN-123` | `feat` | `TRAN-123` |
   | `fix/TRAN-456-login-bug` | `fix` | `TRAN-456` |
   | `refactor/PROJ-42-auth` | `refactor` | `PROJ-42` |
   | `main` / no pattern | _(infer from diff)_ | _(none)_ |

   Regex: `^(feat|fix|docs|style|refactor|test|chore|ci|build|perf)/([A-Z]+-[0-9]+)(-.*)?$`

3. **Analyze changes**
   ```bash
   git diff --staged
   git diff
   git status --short
   ```
   Understand what changed: files added/modified/deleted, purpose of the change.

4. **Compose commit message**

   Format:
   ```
   {type}: {ticket} {subject}

   {optional body — only for complex changes, max 3-4 bullets}
   ```

   Rules:
   - Subject in **imperative mood**: "Add" not "Added", "Fix" not "Fixed"
   - Subject max **72 characters** total (including `type: ticket `)
   - **English only**
   - **No** `Co-authored-by:` trailer
   - **No** `Signed-off-by:` trailer
   - **No** `--no-verify` bypass
   - Body only when the change is non-trivial

   Fallback (branch has no ticket pattern):
   ```
   {inferred-type}: {subject}
   ```

5. **Stage and commit**
   ```bash
   git add -A   # or specific files if partial staging is appropriate
   git commit -m "$(cat <<'EOF'
   {commit message here}
   EOF
   )"
   ```

   Do NOT append any trailers automatically.

## Allowed Types

| Type | When to use |
|------|-------------|
| `feat` | New feature |
| `fix` | Bug fix |
| `docs` | Documentation only |
| `style` | Formatting, whitespace (no logic change) |
| `refactor` | Code restructure without feature/fix |
| `test` | Adding or fixing tests |
| `chore` | Build process, tooling, dependencies |
| `ci` | CI/CD configuration |
| `build` | Build system changes |
| `perf` | Performance improvement |

## Examples

```
feat: TRAN-123 Add login page component
fix: TRAN-456 Fix token expiration check
refactor: PROJ-42 Simplify auth middleware
docs: Update README with installation steps
chore: Upgrade dependencies to latest versions
```

## Notes

- If there are no staged changes, check for unstaged changes and stage relevant files.
- If nothing to commit, report clearly: "Nothing to commit."
- Never guess ticket IDs — only use what is in the branch name.
- Never add `Co-authored-by`, `Signed-off-by`, or any other trailers.
