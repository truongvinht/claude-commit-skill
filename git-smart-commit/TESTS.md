# Test Scenarios for git-smart-commit

Manual test cases validating branch parsing, diff analysis, and commit message generation.

---

## Scenario 1 — Feature branch with ticket

**Branch:** `feat/TRAN-123`

**Staged changes:**
```diff
+++ b/src/components/LoginPage.tsx
@@ -0,0 +1,42 @@
+export function LoginPage() {
+  return <form>...</form>
+}
```

**Expected commit:**
```
feat: TRAN-123 Add LoginPage component
```

---

## Scenario 2 — Fix branch with slug

**Branch:** `fix/TRAN-456-token-expiry`

**Staged changes:**
```diff
--- a/src/auth/token.ts
+++ b/src/auth/token.ts
@@ -12,7 +12,7 @@
-  if (expiresAt > Date.now()) {
+  if (expiresAt > Date.now() / 1000) {
```

**Expected commit:**
```
fix: TRAN-456 Fix token expiration check
```

---

## Scenario 3 — Refactor branch with slug

**Branch:** `refactor/PROJ-42-auth`

**Staged changes:**
```diff
--- a/src/middleware/auth.ts
+++ b/src/middleware/auth.ts
@@ -1,30 +1,15 @@
-// old verbose auth middleware
+// simplified auth middleware
```

**Expected commit:**
```
refactor: PROJ-42 Simplify auth middleware
```

---

## Scenario 4 — No ticket pattern (main / hotfix branch)

**Branch:** `main`

**Staged changes:**
```diff
--- a/README.md
+++ b/README.md
@@ -3,7 +3,7 @@
-A tool for manging commits.
+A tool for managing commits.
```

**Expected commit:**
```
docs: Fix typo in README
```
_(type inferred from diff: only .md file changed → `docs`)_

---

## Scenario 5 — Chore: dependency update

**Branch:** `chore/PROJ-99-upgrade-deps`

**Staged changes:**
```diff
--- a/package.json
+++ b/package.json
@@ -10,3 +10,3 @@
-    "typescript": "^5.2.0"
+    "typescript": "^5.4.0"
```

**Expected commit:**
```
chore: PROJ-99 Upgrade TypeScript to 5.4
```

---

## Scenario 6 — Complex change with body

**Branch:** `feat/TRAN-789-oauth`

**Staged changes:** Multiple files — new OAuth provider, updated config, added tests.

**Expected commit:**
```
feat: TRAN-789 Add OAuth2 Google provider

- Integrate passport-google-oauth20 strategy
- Add callback route and session handling
- Store provider tokens in user profile
```

---

## Scenario 7 — Nothing to commit

**Branch:** `fix/TRAN-000`

**git status:** clean

**Expected output:**
```
Nothing to commit — working tree is clean.
```

---

## Branch Parsing Regex

```
^(feat|fix|docs|style|refactor|test|chore|ci|build|perf)/([A-Z]+-[0-9]+)(-.*)?$
```

| Input | type | ticket | slug |
|-------|------|--------|------|
| `feat/TRAN-123` | `feat` | `TRAN-123` | — |
| `fix/TRAN-456-login-bug` | `fix` | `TRAN-456` | `-login-bug` |
| `refactor/PROJ-42-auth` | `refactor` | `PROJ-42` | `-auth` |
| `main` | _(no match → infer)_ | — | — |
| `feature/abc` | _(no match → infer)_ | — | — |
