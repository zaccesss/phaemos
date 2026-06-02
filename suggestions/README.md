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
- [x] **Security hardening** - rate limiting on login, brute-force lockout, WebSocket JWT auth, telemetry GET auth, password strength, firmware 2MB cap, CORS tightened, API key rotation endpoint, audit SQL text() fix (PR 74)
- [x] **Login page and session guard** - frontend/app/login/page.tsx, Next.js edge middleware, LogoutButton in navbar (PR 75)
- [x] **Full light mode across all components** - dark: variants on all components and pages (PR 76, pending merge)
- [x] **Hardware inventory** - Updated hardware/inventory/needed.md and owned.md with all Amazon deliveries received.

---

## Backlog

### Medium priority

- [x] **WebSocket reconnect on disconnect** - Added useWebSocketTelemetry hook with exponential backoff (1/2/4/8/16s, max 5 attempts), no retry on 1008 auth failure. Wired into TelemetryChart for live pushes. (PR 77)

- [x] **Alert rule evaluation for all v2 sensors** - Extended reading dict in ingest route to use payload.model_dump() so all v2 sensor fields reach alert_service.evaluate_rules. (PR 79)

- [x] **ML retrain endpoint** - POST /api/v1/ml/retrain (admin only, 202, 1-hour cooldown, background task). Fits IsolationForest on last 10,000 rows, dumps model.pkl, reloads in ml_service. Logs n_samples and n_anomalies to audit log. (PR 80)

- [x] **Live sensor grid update on device detail page** - useTelemetry polls every 5s; liveReadings[0] passed to SensorGrid. Removed redundant one-time fetch. (PR 81)

### Low priority

- [ ] **Multi-tenant device ownership** - Add nullable owner_id FK on devices to users. GET /devices filters: admin sees all, technician sees own + unowned. Alembic migration required.

- [x] **alerts.resolved column type migration** - ORM model fixed to Column(Boolean); str() cast removed from filter. No SQL migration needed - DB schema was already BOOLEAN. (PR 82)

- [x] **Ticket creation from alert banner** - AlertBanner now a client component with Create Ticket button. Opens modal with TicketForm prefilled from alert context (device_id, title, description). (PR 83)

- [ ] **Pagination on tickets and devices pages** - Backend: add skip/limit to GET /tickets and GET /devices. Frontend: page state + Prev/Next controls.

### Housekeeping (do in the same session as the last feature)

- [ ] Update all READMEs in folders/subfolders touched this session
- [ ] Update docs/decisions.md with new ADRs (cookie auth, WS JWT, ML retrain, multi-tenant)
- [ ] Update docs/api-reference.md with new endpoints (retrain, rotate-key, pagination)
- [ ] Update docs/VERIFICATION.md with new checklist items
- [ ] Create docs/security.md with the full 18-measure security table
- [ ] Update SECURITY.md to reference docs/security.md
- [ ] Create Makefile at repo root (make dev, make test, make lint, make migrate)
- [ ] Create SUPPORT.md at repo root
- [ ] Update CHANGELOG.md with all session PRs

### Hardware-blocked (do NOT attempt until hardware arrives and is tested)

- [ ] **Custom node enclosure design** - After all 4 nodes tested on breadboard. Options: 3D print (Aston lab), laser cut acrylic, CNC aluminium.
- [ ] **Hardware testing** - Test full sensor suite on all 4 boards. See hardware/wiring/ for pinouts.
- [ ] **Train Isolation Forest** - After 1-2 weeks of real telemetry, run backend/ml/train.py and evaluate with evaluate.py.
