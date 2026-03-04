# git-smart-commit

A Claude Code skill that generates well-formatted [Conventional Commits](https://www.conventionalcommits.org/) messages by parsing your branch name and analyzing actual code changes.

## The Problem

- Claude Code appends `Co-authored-by: Claude...` to every commit
- Commit messages don't reflect your branch naming conventions
- Manual commit message formatting is tedious and inconsistent

## The Solution

`git-smart-commit` automatically:

1. Parses the current branch to extract **type** and **ticket ID**
2. Analyzes `git diff` to understand what changed
3. Composes a Conventional Commits formatted message
4. Commits without any `Co-authored-by` or other trailers

## Branch Pattern

```
{type}/{ticket-id}
{type}/{ticket-id}-optional-slug
```

### Examples

| Branch | Generated Commit |
|--------|-----------------|
| `feat/TRAN-123` | `feat: TRAN-123 Add login page component` |
| `fix/TRAN-456-login-bug` | `fix: TRAN-456 Fix token expiration check` |
| `refactor/PROJ-42-auth` | `refactor: PROJ-42 Simplify auth middleware` |
| `main` / no pattern | `docs: Fix typo in README` _(type inferred from diff)_ |

## Installation

### Global (recommended — works in all projects)

```bash
cp -r git-smart-commit/ ~/.claude/skills/
```

### Project-specific

```bash
cp -r git-smart-commit/ .claude/skills/
```

## Usage

In any Claude Code session, run:

```
/commit
```

or any of the aliases:

```
/git-commit
/smart-commit
```

Claude will:
1. Read the branch name
2. Analyze staged and unstaged changes
3. Generate a commit message
4. Stage and commit (asking for confirmation if needed)

## Commit Format Rules

- **Imperative mood**: "Add" not "Added", "Fix" not "Fixed"
- **Max 72 characters** on the subject line
- **English only**
- **No** `Co-authored-by:` trailer
- **No** `Signed-off-by:` trailer
- Body included only for complex, non-trivial changes (max 3–4 bullets)

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

## License

MIT © Vinh Tran
