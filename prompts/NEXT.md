# NEXT - Continuation Prompt

Read this file at the start of every new session to pick up exactly where we left off.

**Last updated:** 2026-06-02

---

## Start-of-session prompt (copy this in verbatim)

> Continue building Phaemos. Check your memory for full context. Pull and read suggestions/README.md for the backlog, docs/VERIFICATION.md for what is verified, and logs/LOG.md for session history. Start today's date log immediately before touching any file.
>
> Priority order from the backlog:
> 1. WebSocket reconnect on disconnect (exponential backoff, max 5 attempts) - find client with `grep -r "new WebSocket" frontend/`
> 2. Alert rule evaluation for all v2 sensors (check Telemetry model columns vs docs/sensor_reference.md, extend ingest reading dict)
> 3. ML retrain endpoint (POST /api/v1/ml/retrain, admin only, background task, 1-hour cooldown, reuses backend/ml/preprocess.py and train.py)
> 4. Live sensor grid auto-update on device detail (poll /telemetry/{id}/latest every 5s, pass to SensorGrid prop)
> 5. Multi-tenant device ownership (nullable owner_id FK on devices)
> 6. alerts.resolved Boolean migration (USING resolved::boolean in SQL, remove str(resolved) cast)
> 7. Ticket creation from alert banner (TicketForm prefill prop already exists from PR 76 - just wire AlertBanner)
> 8. Pagination on tickets and devices pages
> 9. Housekeeping at the very end: READMEs, docs/decisions.md, docs/api-reference.md, docs/security.md, SECURITY.md, Makefile, SUPPORT.md, CHANGELOG.md
>
> Rules: update the session log after every file change. PRs auto-merge (configured). No AI attribution in commits. First-person WHY comments in code. Plain ASCII hyphens only. UK English throughout. Run pytest + ruff before every backend commit. Run npm run lint + npm run build before every frontend commit.

---

## Current status (as of 2026-06-02)

**Done this session (PRs 74-76):**
- PR 74 MERGED: Security hardening - rate limiting on /auth/login (slowapi, 5/min), brute-force lockout (5 failures = 15min lock), WebSocket JWT auth (close 1008 on fail), telemetry GET/export require Bearer auth, password strength validation, firmware 2MB upload cap, CORS tightened, API key rotation endpoint, audit_service text() fix, last_login tracking, migrations/002_security_hardening.sql
- PR 75 MERGED: Login page (frontend/app/login/page.tsx), Next.js edge middleware (frontend/middleware.ts) guards all routes, LogoutButton in navbar
- PR 76 OPEN (CI running): Full light mode - dark: variants across all components and pages

**What works:**
- Dashboard, compare page, device detail, admin panel, alerts, tickets, devices
- JWT auth end-to-end (login form, middleware guard, Bearer token for API, WS JWT)
- Light/dark toggle switches the entire UI (PR 76, pending merge)
- Security: rate limiting, lockout, WS auth, telemetry auth, password strength, CORS
- Backend: audit log, demo mode, data retention, CSV export, ML pipeline, firmware OTA
- Docker Compose runs end-to-end locally

**Remaining backlog (see suggestions/README.md for full detail):**
1. WebSocket reconnect - MEDIUM
2. Alert evaluator for all v2 sensors - MEDIUM
3. ML retrain endpoint - MEDIUM
4. Live sensor grid auto-update - MEDIUM
5. Multi-tenant device ownership - LOW
6. alerts.resolved Boolean migration - LOW
7. Ticket creation from alert banner - LOW (TicketForm prefill prop already added)
8. Pagination on tickets and devices - LOW
9. End-of-session housekeeping (docs, READMEs, CHANGELOG, Makefile, SUPPORT.md, docs/security.md)

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
- Update `logs/YYYY-MM-DD.md` after every file change
- Never commit directly to main
- Hooks already executable: `git config core.hooksPath .githooks` already set
- `echo "NEXT_PUBLIC_API_URL=http://localhost:8000" > frontend/.env.local` on fresh clone
- Auto-merge: `gh pr merge <n> --auto --merge --delete-branch`

---

## Important notes for next session

- **TicketForm prefill prop** already exists (added in PR 76) - accept `prefill?: { device_id?, description?, title? }`. AlertBanner just needs a "Create Ticket" button that passes alert context.
- **alerts.resolved** - the SQL schema has BOOLEAN but the Python model has Column(String). Do this migration before any alert work to avoid silent corruption.
- **WebSocket client** - find with `grep -r "new WebSocket" frontend/`. Reconnect must NOT retry on 1008 close code (auth failure) - that would loop forever.
- **Backend image rebuild** needed after any requirements.txt change: `docker compose build backend`
- **Git hooks are now executable** - no more "hook ignored" warnings on commit
