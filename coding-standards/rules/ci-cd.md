# Coding Rules - CI/CD and Pre-commit

Automate the checks so standards are enforced by machines, not memory.

## Pre-commit hooks (local, fast)
1. Use a pre-commit framework (`pre-commit` for Python/mixed repos; `husky` + `lint-staged`
   for JS/TS) so checks run before code is committed.
2. Minimum hooks on every repo:
   - **Formatter:** `ruff format` (Python), `prettier` (JS/TS).
   - **Linter:** `ruff check` (Python), `eslint` (JS/TS).
   - **Secret scanner:** `gitleaks` (or `detect-secrets`) to block committed keys/tokens.
   - Whitespace / large-file / merge-conflict-marker checks.
3. Keep hooks fast (seconds). Heavy checks (full type check, tests) run in CI, not pre-commit.

## CI pipeline (GitHub Actions)
4. Run on every push and pull request. Gate merges on green.
5. Standard jobs:
   - **Lint + format check** (`ruff check` / `ruff format --check`, `eslint` / `prettier --check`).
   - **Type check** (`pyright`/`mypy`, `tsc --noEmit`).
   - **Tests** with coverage (`pytest --cov`, `vitest`/`jest`).
   - **Secret scan** (`gitleaks`) and **dependency audit** (`pip-audit`/`uv` audit, `npm audit`,
     or GitHub Dependabot).
   - **Build** (compile / bundle / docker build) to catch breakage before deploy.
6. Pin action versions and the language/runtime version. Cache deps (`uv`, npm) for speed.
7. Fail the build on lint errors, type errors, failing tests, or found secrets. No "continue on
   error" on these gates.

## Secrets in CI
8. Store CI secrets in the platform's secret store (GitHub Actions secrets / OIDC), never in
   workflow files or repo. Prefer short-lived OIDC federation over long-lived cloud keys.
9. Least privilege for the CI role. A deploy job gets only what it needs.

## Releases and deploys
10. Tag releases with SemVer and generate a changelog (see versioning.md). Automate with
    `semantic-release` / `standard-version` where it fits.
11. Separate build from deploy. Deploy from a built, tested artifact, not from unbuilt source.
12. Have a rollback path (previous image/tag) and note it in the deploy job.
13. Protect the default branch: require review + passing checks, disallow force-push.

---

## Quick reference - CI/CD
- Pre-commit: format + lint + gitleaks on every commit (fast)
- CI on every PR: lint, type check, test+coverage, secret scan, dep audit, build
- Gate merges on green; fail on lint/type/test/secret findings
- CI secrets in the platform store; prefer OIDC over static cloud keys; least privilege
- SemVer tags + changelog; build-then-deploy; rollback path; protected branch
