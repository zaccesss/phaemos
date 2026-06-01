# Suggestions

A living backlog of improvements for PHAEMOS. Updated at the end of every session.

- [x] = implemented and merged
- [ ] = not yet done

Read this at the start of every session to know what to work on next.
High priority items first.

---
## Completed

- [x] Audit logging - audit_service.py, wired to all mutating routes (PRs 55-56)
- [x] /auth/me endpoint - JWT dependency, get_current_user (PR 55)
- [x] Pre-commit ruff fix hook
- [x] Rate limiting - slowapi
- [x] Password strength validation
- [x] Offline device detection - APScheduler
- [x] Alembic migrations
- [x] JWT in httpOnly cookies
- [x] Frontend login page (phaemos.com - separate site)
- [x] Wire TicketForm to POST /tickets (PR 58)
- [x] GET /audit-logs endpoint and AuditLog component (PRs 55, 58)
- [x] UserTable wired to GET /auth/users (PRs 55, 58)
- [x] Device detail page (PR 59)
- [x] Audit calls in all routes (PR 56)
- [x] Per-sensor charts with time range selector (PR 60)
- [x] Alert rules CRUD + AlertRulesPanel (PR 61)
- [x] Demo mode - POST /demo/start with APScheduler (PR 63)
- [x] CSV export - GET /telemetry/export (PR 62)
- [x] Device comparison page /compare (PR 64)
- [x] ML evaluate.py - all 4 functions (PR 65)
- [x] Uptime Kuma setup guide (PR 70)
- [x] node_type filter on telemetry + dashboard picker (PR 66)
- [x] firmware_version column on Device + DeviceCard display (PR 67)
- [x] 90-day telemetry data retention - APScheduler cron (PR 68)
- [x] Dark/light mode toggle - ThemeToggle + localStorage (PR 69)
- [x] PostCSS config (was missing - broke Tailwind)
- [x] alerts.resolved boolean/string fix
- [x] Dashboard selected-device polling bug fix

---

## Backlog

### High priority

- [ ] **Login page and session guard** - `frontend/app/login/page.tsx` does not exist. Admin panel endpoints return 401 from the browser without a token. Add email/password form, POST to `/api/v1/auth/login`, store token, redirect protected routes to `/login` when unauthenticated.

- [ ] **Full light mode across all components** - ThemeToggle works at the body level. DeviceCard, TelemetryChart, SensorGrid and all other components use hardcoded dark Tailwind classes. Add `dark:` prefixes to every dark-only class so the whole UI switches in light mode.

### Medium priority

- [ ] **WebSocket reconnect on disconnect** - The dashboard WebSocket closes on error without retrying. Add exponential backoff reconnect (max 5 attempts, 1s/2s/4s/8s/16s delays).

- [ ] **Alert rule evaluation for all v2 sensors** - alert_service.py only checks 6 fields. Extend to cover all numeric telemetry columns dynamically.

- [ ] **ML retrain endpoint** - POST /api/v1/ml/retrain - reads last N rows, runs train.py, replaces model.pkl, logs precision/recall to audit log. Admin only.

- [ ] **Live sensor grid update on device detail page** - SensorGrid shows a static snapshot. Wire it to poll every 5s using the most recent useTelemetry reading.

### Low priority

- [ ] **Multi-tenant device ownership** - user_id FK on devices so technicians only see their own.

- [ ] **alerts.resolved column type migration** - Change from String to Boolean via Alembic migration. Remove str(resolved) workaround in routes/alerts.py.

- [ ] **Ticket creation from alert banner** - Add "Create Ticket" button on AlertBanner pre-filled with alert context.

- [ ] **Pagination on tickets and devices pages** - Add skip/limit query params and Next/Prev controls.

- [ ] **Hardware inventory** - Update hardware/inventory/needed.md as Amazon deliveries arrive. Move items to owned.md when received.

- [ ] **Custom node enclosure design** - After all 4 nodes tested on breadboard, design housing. Options: 3D print (Aston lab), laser cut acrylic, CNC aluminium. Do not start until sensor layout is finalised and PCB is designed.

- [ ] **Hardware testing** - Test full sensor suite on all 4 boards. See hardware/wiring/ for pinouts and hardware/inventory/owned.md for what is available.

- [ ] **Train Isolation Forest** - After 1-2 weeks of real telemetry, run backend/ml/train.py and evaluate with evaluate.py. Tune ANOMALY_SCORE_THRESHOLD.
