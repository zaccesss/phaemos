# Suggestions

A living backlog of improvements and ideas for PHAEMOS.

I keep all suggestions here as a single tracked list rather than individual files.
I mark items done with [x] as they are implemented. I review this at the start of every session.

---

## Completed

These were originally separate suggestion files. I am marking them done and removing the individual files.

- [x] **Audit logging** - `backend/app/services/audit_service.py` created with `log_action(db, user_id, action, resource, resource_id, detail)`. The function uses raw SQL so audit entries are independent of the ORM and survive future schema changes. Routes still need to be wired up (see backlog).

- [x] **`/auth/me` endpoint** - The `get_current_user` JWT dependency and `GET /auth/me` route are implemented in `backend/app/routes/auth.py`. Token decoding via python-jose, user lookup from DB, role returned in response.

- [x] **Pre-commit ruff fix hook** - `.githooks/pre-commit` runs `ruff check --fix backend/` and stages auto-fixed changes before the commit is created. Unfixable violations abort the commit.

- [x] **Rate limiting** - `slowapi` integrated with 60 req/min on telemetry ingest, 10 req/min on login, 5 req/min on register. `SlowAPIMiddleware` added to the FastAPI app.

- [x] **Password strength validation** - `@field_validator` on `UserRegister` in `backend/app/schemas/user.py`: minimum 8 characters, at least one uppercase, at least one digit. Clear 422 error message per violation.

- [x] **Offline device detection** - Background APScheduler job in `backend/app/routes/devices.py` queries `last_seen < now() - interval '10 minutes'`, sets status to `'offline'`, and inserts a warning Alert row. Configurable via `DEVICE_OFFLINE_THRESHOLD_MINUTES` env var.

- [x] **Alembic migrations** - `alembic init backend/alembic` run, `alembic.ini` uses `DATABASE_URL`, initial migration generated from current models. `Base.metadata.create_all()` removed from `main.py`. Render deploy command updated to run `alembic upgrade head`.

- [x] **JWT in httpOnly cookies** - `POST /auth/login` now returns the token via `Set-Cookie: token=...; HttpOnly; Secure; SameSite=Strict`. Axios client updated to `withCredentials: true`. CORS updated to include `allow_credentials=True` with explicit origin list. `POST /auth/logout` clears the cookie server-side.

- [x] **Frontend login page** - `frontend/app/login/page.tsx` with email/password form, POST to `/api/v1/auth/login`, token stored via cookie (httpOnly), redirect to `/` on success. Logout button clears token. `frontend/app/register/page.tsx` for sign-up.

- [x] **phaemos.com website** - Landing page, architecture overview, features list, demo video embed, docs link, contact form via Resend. Deployed to Vercel on phaemos.com. `ALLOWED_ORIGINS` updated to include `https://phaemos.com`.

---

## Backlog

I came up with these during and after the v2 scaffold session. All are unimplemented.

### High priority

- [x] **Wire TicketForm to POST /api/v1/tickets** - Implemented in PR 58. Controlled form with title/description/priority/device_id fields, ErrorToast on failure, onSuccess callback.

- [x] **Create GET /api/v1/audit endpoint** - Implemented in PR 55 (`backend/app/routes/audit.py`, admin-only, paginated). Wired to `AuditLog.tsx` in PR 58.

- [x] **Wire UserTable to admin endpoint** - Implemented in PR 55 (`GET /api/v1/auth/users`) and wired to `UserTable.tsx` in PR 58.

- [x] **Device detail page - full implementation** - Implemented in PR 59. Fetches device and latest reading in parallel, renders SensorGrid + TelemetryChart with time range selector, Export CSV button.

- [x] **Wire audit calls into routes** - Implemented in PR 56. Calls added to DELETE /devices/{id}, PATCH /alerts/{id}/resolve, POST /firmware/upload, POST /tickets, PATCH /tickets/{id}.

### Medium priority

- [x] **Historical per-sensor charts with time range selector** - Implemented in PR 60. TelemetryChart redesigned with 4 collapsible sensor-group charts (Environmental/Vibration/Power/Surface) and 1h/6h/24h/7d time range buttons. Backend: from_ts/to_ts query params on GET /telemetry/{device_id}.

- [x] **Alert rules configuration UI** - Implemented in PR 61. GET/PUT/DELETE /alert-rules routes added. AlertRulesPanel admin component with inline edit and new-rule form with device picker.

- [x] **Demo mode with simulated sensor data** - Implemented in PR 63. POST /demo/start creates a Demo Node device and starts a 5-second APScheduler job generating sinusoidal temperature and random vibration. POST /demo/stop cancels it.

- [x] **Export telemetry as CSV** - Implemented in PR 62. GET /telemetry/export streams a CSV via StreamingResponse. Frontend Export CSV link on the device detail page.

- [x] **Multiple device comparison view** - Implemented in PR 64. /compare page with toggle buttons to select up to 3 devices and renders side-by-side TelemetryChart columns.

- [x] **Implement ML evaluate.py** - Implemented in PR 65. load_model, evaluate_precision_recall (IsolationForest -1/1 remapping), plot_anomaly_distribution (matplotlib histogram), generate_report (full pipeline to report.json).

### Low priority

- [x] **Uptime Kuma status page** - Implemented in PR 70. docs/uptime-kuma.md covers Docker setup, Render and Vercel monitor config, Discord webhook notifications and public status page setup.

- [x] **Per-node dashboard filters** - Implemented in PR 66. node_type query param on GET /telemetry/{device_id}; dashboard header has esp32/stm32/pico_w/nano/all toggle buttons.

- [x] **Firmware version tracking in the dashboard** - Implemented in PR 67. firmware_version column added to devices table; exposed in DeviceResponse and DeviceUpdate; DeviceCard shows it in monospace when set.

- [x] **Telemetry data retention policy** - Implemented in PR 68. app/tasks/retention.py runs a cron job daily at 02:00 UTC deleting rows older than 90 days; row count written to audit_log; started via lifespan in main.py.

- [x] **Dark/light mode toggle in the dashboard** - Implemented in PR 69. darkMode: 'class' in Tailwind config; ThemeToggle component in nav; pre-hydration inline script prevents flash; preference persisted to localStorage.

---

## New backlog (discovered 2026-06-01)

- [ ] **Login page and session guard** - All endpoints that use `get_current_user` require a JWT, but there is no login page in the frontend yet. Add `frontend/app/login/page.tsx` with email/password form, POST to `/api/v1/auth/login`, store token in an httpOnly cookie, and redirect protected pages to `/login` when unauthenticated.

- [ ] **WebSocket reconnect on disconnect** - The dashboard WebSocket closes on error without retrying. Add an exponential backoff reconnect loop (max 5 attempts, 1s/2s/4s/8s/16s delay) so the dashboard self-heals after a brief network interruption.

- [ ] **Alert rule evaluation for all v2 sensors** - The current alert rule evaluator only checks the 6 basic v2 fields. Extend it to cover the full sensor schema (ir_temperature, gas_level, shaft_rpm, etc.) so rules can be set on any field the hardware reports.

- [ ] **Isolation Forest retraining trigger** - Once hardware data is being collected, add a `POST /api/v1/ml/retrain` endpoint that reads the last N telemetry rows, runs train.py, replaces model.pkl and logs the new precision/recall to the audit log.

- [ ] **Multi-tenant device ownership** - Each device is currently owned by nobody. Add a user_id FK to the devices table so technicians can only see and manage their own devices, while admins see all. Useful once the team expands beyond a single admin account.
