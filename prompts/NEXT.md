# NEXT - Continuation Prompt

Read this file at the start of every new session to pick up exactly where we left off.

**Last updated:** 2026-06-02

---

## Start-of-session prompt (copy this in verbatim)

> Continue building Phaemos. Check your memory for full context. Pull and read suggestions/README.md for the backlog, docs/VERIFICATION.md for what is verified, and logs/LOG.md for session history. Start today's date log immediately before touching any file.
>
> Priority order from the backlog:
> 1. Multi-tenant device ownership (nullable owner_id FK on devices, alembic migration, role-filtered GET /devices)
> 2. Housekeeping already done this session - skip to new features
>
> Rules: update the session log after every file change. PRs auto-merge (configured). No AI attribution in commits. First-person WHY comments in code. Plain ASCII hyphens only. UK English throughout. Run pytest + ruff before every backend commit. Run npm run lint + npm run build before every frontend commit.

---

## Current status (as of 2026-06-02)

**Done this session (PRs 77-85):**
- PR 77 MERGED: WebSocket reconnect hook with exponential backoff, TelemetryChart live push wiring
- PR 78 MERGED: git hook execute bit committed
- PR 79 MERGED: Alert rule evaluation extended to all v2 sensors
- PR 80 MERGED: ML retrain endpoint POST /api/v1/ml/retrain (admin, 202, 1-hour cooldown, background task)
- PR 81 MERGED: Live sensor grid auto-update on device detail (5s poll)
- PR 82 MERGED: alerts.resolved ORM column String -> Boolean fix
- PR 83 MERGED: AlertBanner Create Ticket button with prefilled modal
- PR 84 MERGED: Pagination on tickets and devices (skip/limit backend, Prev/Next frontend)
- PR 85 OPEN: Session housekeeping docs (docs/security.md, Makefile, SUPPORT.md, CHANGELOG, decisions.md, api-reference.md, VERIFICATION.md, SECURITY.md, NEXT.md)

**What works:**
- Dashboard, compare page, device detail, admin panel, alerts, tickets, devices
- JWT auth end-to-end (login form, middleware guard, Bearer token for API, WS JWT)
- WebSocket live telemetry with exponential backoff reconnect
- Light/dark toggle switches the entire UI
- Security: rate limiting, lockout, WS auth, telemetry auth, password strength, CORS
- Backend: audit log, demo mode, data retention, CSV export, ML pipeline, firmware OTA, ML retrain
- Docker Compose runs end-to-end locally
- Pagination on tickets and devices

**Remaining backlog (see suggestions/README.md for full detail):**
1. Multi-tenant device ownership - LOW (nullable owner_id FK on devices, alembic migration)

**Hardware-blocked (do not start):**
- Custom node enclosure design
- Hardware testing
- Train Isolation Forest

---

## Start backend locally

```bash
export PATH="/Applications/Docker.app/Contents/Resources/bin:$PATH"
docker compose up db redis backend -d
cd frontend && npm run dev
curl http://localhost:8000/  # should return {"status":"ok"}
```

Or use `make dev` from the repo root (see Makefile).

Run migration on existing DB if backend crashes on login:

```bash
docker exec phaemos-db-1 psql -U postgres -d phaemos -c "ALTER TABLE users ADD COLUMN IF NOT EXISTS failed_login_attempts INTEGER NOT NULL DEFAULT 0, ADD COLUMN IF NOT EXISTS locked_until TIMESTAMP WITH TIME ZONE;"
```

If containers were recreated with `docker compose down`, re-seed (see logs/2026-06-01.md seeding section).

**Note:** `docker compose stop` preserves data. `docker compose down` wipes postgres_data volume.

---

## Key rules

- UK English (colour/behaviour/organisation/licence)
- No em/en dashes - hyphens only in comments
- No Oxford commas
- First-person WHY comments in code
- No AI co-author credits
- Update `logs/YYYY-MM-DD.md` after every file change with detailed explanations (see logs/2026-06-02.md for the required format)
- Never commit directly to main
- Hooks already executable: `git config core.hooksPath .githooks` already set
- `echo "NEXT_PUBLIC_API_URL=http://localhost:8000" > frontend/.env.local` on fresh clone
- Auto-merge: `gh pr merge <n> --auto --merge --delete-branch`
- Use `make test` and `make lint` before every commit

---

## Important notes for next session

- **Multi-tenant device ownership** is the only remaining backlog item. It requires:
  1. Add nullable `owner_id UUID` FK on `devices` referencing `users.id`
  2. Alembic migration (or raw SQL in `migrations/003_multi_tenant.sql`)
  3. `GET /devices` filter: admin sees all, technician sees own + unowned, viewer sees all
  4. Frontend: no change needed unless we want a device ownership UI
- **All housekeeping is done** - CHANGELOG, decisions, api-reference, VERIFICATION, security.md, Makefile, SUPPORT.md, NEXT.md all updated
- **ruff is installed via brew** on the dev machine (`brew install ruff`) - use directly without Docker for linting
- **git hooks** are now committed as executable (PR 78) - no more silent failures on fresh clones
