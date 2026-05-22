# NEXT - Continuation Prompt

Read this file at the start of every new session to pick up exactly where we left off.

**Last updated:** 2026-05-23 - governance session complete

---

## Current status

All governance and rules work is DONE. The repo is in a clean, well-organised state.

**Working and committed (new this session):**
- `.githooks/commit-msg` and `.githooks/prepare-commit-msg`
- `.prettierrc`
- `.github/WORKFLOW.md`
- `logs/` folder with `README.md` and `2026-05-22.md` session log
- `prompts/` folder with `README.md` and `NEXT.md` (this file)
- `suggestions/` folder with 11 suggestion files
- `sql/` folder with queries, seed data and migration guidance
- `CONTRIBUTING.md` updated with hooks, first-person rule, UK English, log rules
- `LOG.md` at root updated to index pointing to `logs/`
- All code comments updated to first-person UK English across backend, firmware and frontend

**Local only (gitignored):**
- `.claude/CLAUDE.md` - master rules file (gitignored via .claude/)
- `.agents/RULES.md`, `.agents/CONTEXT.md`, `.agents/WORKFLOW.md`
- `extract/` - reference portfolio project

---

## One-time setup (do this on every fresh clone)

```bash
git config core.hooksPath .githooks
```

---

## Next steps (in order of priority)

1. **Rate limiting** - `suggestions/rate-limiting.md` - high priority before any public deployment
2. **Frontend login page** - `suggestions/frontend-login-page.md` - high priority, blocks all user access
3. **Alembic migrations** - `suggestions/alembic-migrations.md` - must do before production with real data
4. **`/auth/me` endpoint** - `suggestions/auth-me-endpoint.md` - blocks Phase 2 features
5. Start building hardware and testing the firmware-to-backend flow end to end

---

## Blockers

None. All governance work is complete.

---

## Most recently changed files (2026-05-23)

- `.gitignore` - CLAUDE.md and .claude/ added as gitignored
- `.claude/CLAUDE.md` - rules moved here from root (local only)
- `backend/app/routes/auth.py` - comments updated to first-person
- `backend/app/services/alert_service.py` - comment updated to first-person
- `backend/app/services/ml_service.py` - all comments updated to first-person
- `logs/2026-05-22.md` - updated with comment rewrite summary
- `prompts/NEXT.md` - this file

---

## Key rules reminder (full rules in .claude/CLAUDE.md)

- UK English (colour/behaviour/organisation/licence)
- No em/en dashes - hyphens only
- No Oxford commas
- First-person code comments only
- No AI co-author credits - ever
- Always update this file and logs/YYYY-MM-DD.md after every action
- Never commit directly to main
- Run `git config core.hooksPath .githooks` on every fresh clone
