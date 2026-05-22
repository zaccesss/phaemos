# Pre-commit ruff --fix hook

**Why it matters:** The current commit-msg hook rejects bad commit messages but there is no hook that auto-fixes Python style issues before a commit is attempted. This means commits can fail CI because of trivial ruff violations that could have been fixed automatically.

**Rough approach:**
- Add a `pre-commit` hook to `.githooks/pre-commit`
- Run `ruff check --fix backend/` and stage any changes it makes
- If ruff exits non-zero after fixing (unfixable violations remain), abort the commit with a clear message
- Also run `ruff format backend/` for consistent formatting

**Priority:** low - nice to have but ruff in CI already catches issues
