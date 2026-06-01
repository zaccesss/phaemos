# NEXT - Continuation Prompt

Read this file at the start of every new session to pick up exactly where we left off.

**Last updated:** 2026-06-01

---

## Start-of-session prompt (copy this in verbatim)

> Continue building Phaemos. Check your memory for full context. Pull and read suggestions/README.md for the backlog, docs/VERIFICATION.md for what is verified, and logs/LOG.md for session history. Start today's date log immediately before touching any file.
>
> Priority order from the backlog:
> 1. Login page and session guard (frontend/app/login/page.tsx - biggest usability gap, admin panel is unusable without it)
> 2. Full light mode across all components (ThemeToggle works but cards/charts are still hardcoded dark)
> 3. WebSocket reconnect on disconnect (exponential backoff, max 5 attempts)
> 4. Alert rule evaluation for all v2 sensors (evaluator currently only checks 6 fields)
> 5. Remaining items in suggestions/README.md in priority order
>
> Also check hardware/inventory/needed.md - update status of any Amazon deliveries that have arrived and move them to owned.md.
>
> Rules: update the session log after every file change. PRs auto-merge (already configured). No AI attribution in commits. First-person WHY comments in all code. Plain ASCII hyphens only, no em-dashes. UK English throughout.

---

## Current status

All 15 original backlog items are done (PRs 55-72). The app runs end to end locally with Docker.

**What works:**
- Dashboard with live device cards, per-sensor charts, time range picker, node type filter
- Compare page - side by side charts for up to 3 devices
- Device detail page - sensor grid + charts + Export CSV
- Admin panel - user table, audit log, alert rules CRUD, firmware upload
- Backend: JWT auth, audit logging, demo mode, data retention, CSV export, ML evaluate pipeline
- Dark mode toggle (light mode body works, individual components still dark-only)

**New backlog (not done yet) - see suggestions/README.md:**
1. Login page and session guard - HIGH (admin features need auth from UI)
2. Full light mode across all components - HIGH (toggle exists but components are dark-only)
3. WebSocket reconnect - MEDIUM
4. Alert evaluator for all v2 sensors - MEDIUM
5. ML retrain endpoint - MEDIUM
6. Live sensor grid auto-update on device detail - MEDIUM
7. Multi-tenant device ownership - LOW
8. alerts.resolved String->Boolean migration - LOW
9. Ticket creation from alert banner - LOW
10. Pagination on tickets/devices pages - LOW
11. Hardware inventory updates as deliveries arrive
12. Hardware testing (waiting for components)
13. Isolation Forest training (waiting for hardware data)
14. Node enclosure design (Phase 2, after MVP validated)

---

## Hardware status

Amazon order placed May 2026 - £133.09, most arriving this week.
See `hardware/inventory/needed.md` for delivery dates.
See `hardware/inventory/owned.md` for what is already in hand.

**Important notes:**
- BMP280 ordered instead of BME280 - no humidity on secondary nodes. Check if acceptable.
- AS5600 diametrically magnetised magnet still needs to be ordered.
- Logic level shifter 3.3V-5V still needs to be ordered.
- Check BOJACK 37-values kit and ELEGOO Starter Kit contents before collecting from Aston lab (Richard).

---

## Start backend locally

```bash
export PATH="/Applications/Docker.app/Contents/Resources/bin:$PATH"
docker compose up db redis backend -d
cd frontend && npm run dev
curl http://localhost:8000/  # should return {"status":"ok"}
```

If the DB is empty (containers were recreated with `docker compose down`), re-seed:
```bash
# Get device IDs and API keys, POST telemetry with device_id in body
# See logs/2026-06-01.md seeding section for the full script
```

**Note:** `docker compose stop` preserves data. `docker compose down` wipes the postgres_data volume.

---

## Key rules

- UK English (colour/behaviour/organisation/licence)
- No em/en dashes - hyphens only in comments
- No Oxford commas
- First-person WHY comments ("# I use X because Y")
- No AI co-author credits
- Update `logs/YYYY-MM-DD.md` after every file change
- Never commit directly to main
- `git config core.hooksPath .githooks` on fresh clone
- `echo "NEXT_PUBLIC_API_URL=http://localhost:8000" > frontend/.env.local` on fresh clone
- Auto-merge: `gh pr merge <n> --auto --merge --delete-branch`
