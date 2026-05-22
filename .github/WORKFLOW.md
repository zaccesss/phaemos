# Development Workflow

Branch protection on `main` requires every change to go through a pull request and pass CI. Nothing broken ever reaches production. This file is the authoritative workflow reference for this project.

---

## Making a change

```bash
# 1. Always start from the latest main
git checkout main && git pull
git checkout -b feat/your-description

# 2. Make changes, then log what you did
#    Update logs/YYYY-MM-DD.md with what changed and why
#    Update prompts/NEXT.md with current state and next steps
#    Public changes also go in CHANGELOG.md under [Unreleased]

# 3. Stage and commit (no em/en dashes, no Oxford commas, no AI attribution)
git add path/to/changed/file
git commit -m "feat: short description of what changed"

# 4. Push the branch
git push -u origin feat/your-description

# 5. Create the PR and enable auto-merge
gh pr create --title "feat: short description" --body "What changed and why."
gh pr merge --squash --delete-branch --auto

# 6. CI (lint, test, build) takes 2-3 minutes. Wait for it to pass.
#    Backend auto-deploys to Render via deploy.yml after CI passes.
#    Frontend auto-deploys to Vercel immediately after merge.
```

Total time from commit to live: about 5 minutes.

---

## Commit message rules

The `.githooks/commit-msg` hook rejects commits that violate these rules. The `.githooks/prepare-commit-msg` hook strips AI co-author lines automatically.

- Use conventional prefixes: `feat:`, `fix:`, `chore:`, `docs:`, `refactor:`, `test:`, `perf:`
- No em dashes (--) or en dashes (-) - use a hyphen (-) instead
- No Oxford comma - write "x, y and z" not "x, y, and z"
- No AI co-author credits or AI attribution of any kind
- Subject line 72 characters or fewer
- UK English throughout

---

## Hook setup (one-time per clone)

```bash
git config core.hooksPath .githooks
```

This activates commit-msg (style enforcement) and prepare-commit-msg (AI credit stripping).

---

## Branch naming

| Type | Example |
|---|---|
| New feature | `feat/websocket-dashboard` |
| Bug fix | `fix/alerts-resolved-column-type` |
| Documentation | `docs/update-api-reference` |
| Refactor | `refactor/split-ml-service` |
| Chore / config | `chore/update-ruff-version` |

---

## Changelog rules

- `CHANGELOG.md` is public-facing. It records what changed between releases.
- `logs/YYYY-MM-DD.md` is the internal engineering record with full detail, reasoning and decisions.
- Never put internal session notes in `CHANGELOG.md`.
- Update `logs/` immediately as you work - do not batch at session end.
- Update `prompts/NEXT.md` after every meaningful action.

---

## Session-end cleanup

```bash
# Delete all local branches except main
git branch | grep -v main | xargs git branch -D

# Delete all remote branches except main
git branch -r | grep -v main | grep -v HEAD | sed 's/origin\///' | xargs -I {} git push origin --delete {}
```

---

## If the PR is not mergeable after CI passes

Main moved ahead while your branch was open. Rebase and push:

```bash
git fetch origin
git rebase origin/main
git push --force-with-lease
```

---

## Checking PR status

```bash
gh pr list                # see all open PRs
gh pr view 42             # see a specific PR
gh pr checks              # see CI status on current branch
```

---

## Release process

1. Update `CHANGELOG.md` with a new `## [X.Y.Z] - DD-MM-YYYY` section
2. Commit and merge to main
3. Tag: `git tag vX.Y.Z && git push origin vX.Y.Z`
4. GitHub Actions `release.yml` validates the CHANGELOG entry and creates the GitHub Release

---

## Environment variables

All secrets live in Render (backend) and Vercel (frontend) project settings. Never commit real values. Add placeholders to `.env.example` when adding a new variable.

| Variable | Service | Purpose |
|---|---|---|
| `DATABASE_URL` | Render | PostgreSQL connection string |
| `REDIS_URL` | Render | Redis connection |
| `SECRET_KEY` | Render | JWT signing secret - must be long and random |
| `ALLOWED_ORIGINS` | Render | CORS allowed origins, comma-separated |
| `DISCORD_WEBHOOK_URL` | Render | Alert notifications - leave empty to disable |
| `SMTP_HOST` / `SMTP_USER` / `SMTP_PASSWORD` | Render | Email notifications |
| `ALERT_EMAIL_TO` | Render | Alert email recipient |
| `FIRMWARE_STORAGE_PATH` | Render | Where .bin files are stored on server |
| `RENDER_DEPLOY_HOOK_URL` | GitHub Actions Secret | Deploy hook URL |
| `NEXT_PUBLIC_API_URL` | Vercel | Backend base URL for frontend axios client |

---

## Suggestions backlog

See `suggestions/` at the repo root. One file per suggestion. Delete the file when implemented.

High-priority suggestions:
- `suggestions/frontend-login-page.md`
- `suggestions/alembic-migrations.md`
- `suggestions/auth-me-endpoint.md`
- `suggestions/rate-limiting.md`
