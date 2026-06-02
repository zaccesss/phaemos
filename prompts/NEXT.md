# NEXT - Continuation Prompt

Read this file at the start of every new session to pick up exactly where we left off.

**Last updated:** 2026-06-02

---

## Start-of-session prompt (copy this in verbatim)

> Continue building Phaemos. Check your memory for full context. Pull and read suggestions/README.md for the backlog, docs/VERIFICATION.md for what is verified, and logs/LOG.md for session history. Start today's date log immediately before touching any file.
>
> Priority order from the backlog: (1) Auth on remaining device endpoints POST/PATCH/GET /devices/{id}, (2) Refresh token / silent JWT renewal, (3) Device ownership assignment UI, (4) Tab visibility API pause in useTelemetry.
>
> Rules: update the session log after every file change. PRs auto-merge (configured). No AI attribution in commits. First-person WHY comments in code. Plain ASCII hyphens only. UK English throughout. Run pytest + ruff before every backend commit. Run npm run lint + npm run build before every frontend commit.

---

## Current status (as of 2026-06-02)

**Done this session (PRs 77-86):**

- PR 77 MERGED: WebSocket reconnect hook with exponential backoff, TelemetryChart live push wiring
- PR 78 MERGED: git hook execute bit committed
- PR 79 MERGED: Alert rule evaluation extended to all v2 sensors
- PR 80 MERGED: ML retrain endpoint POST /api/v1/ml/retrain (admin, 202, 1-hour cooldown, background task)
- PR 81 MERGED: Live sensor grid auto-update on device detail (5s poll)
- PR 82 MERGED: alerts.resolved ORM column String -> Boolean fix
- PR 83 MERGED: AlertBanner Create Ticket button with prefilled modal
- PR 84 MERGED: Pagination on tickets and devices (skip/limit backend, Prev/Next frontend)
- PR 85 MERGED: Session housekeeping (CHANGELOG v2.2.0, Makefile, SUPPORT.md, docs/security.md, 3 ADRs, VERIFICATION.md, NEXT.md)
- PR 86 OPEN (CI/auto-merge): Multi-tenant device ownership - nullable owner_id FK on devices, GET /devices role filter, SQL migration 003, 5 tests

**What works:**

- Dashboard, compare page, device detail, admin panel, alerts, tickets, devices
- JWT auth end-to-end (login form, middleware guard, Bearer token for API, WS JWT)
- WebSocket live telemetry with exponential backoff reconnect
- Light/dark toggle switches the entire UI
- Security: rate limiting, lockout, WS auth, telemetry auth, password strength, CORS
- Backend: audit log, demo mode, data retention, CSV export, ML pipeline, firmware OTA, ML retrain
- Docker Compose runs end-to-end locally
- Pagination on tickets and devices
- Multi-tenant device ownership (owner_id on devices, role-filtered GET /devices)

**Remaining backlog (see suggestions/README.md for full detail):**

1. Auth on POST /devices, PATCH /devices/{id}, GET /devices/{id} - MEDIUM
2. Refresh token / silent JWT renewal - MEDIUM
3. Device ownership assignment UI - LOW
4. Tab visibility API pause in useTelemetry - LOW

**Hardware-blocked (do not start):**

- Custom node enclosure design
- Hardware testing
- Train Isolation Forest

---

## Start backend locally

```bash
export PATH="/Applications/Docker.app/Contents/Resources/bin:$PATH"
make dev
```

Or manually:

```bash
docker compose up db redis backend -d
cd frontend && npm run dev
curl http://localhost:8000/  # should return {"status":"ok"}
```

Run migration on existing DB if backend crashes on login:

```bash
docker exec phaemos-db-1 psql -U postgres -d phaemos -f /path/to/migration.sql
```

If containers were recreated with `docker compose down`, re-seed:

```bash
make seed
```

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

- **PR 86** may still be in CI - check `gh pr view 86` before starting
- **ruff is installed via brew** on the dev machine - use directly without Docker for linting
- **git hooks** are committed as executable (PR 78) - no more silent failures on fresh clones
- **Multi-tenant is done** - owner_id is nullable; NULL = shared/unassigned device visible to all technicians
- **Next priority** is auth on the remaining unauthenticated device endpoints (POST, PATCH, GET by ID)
