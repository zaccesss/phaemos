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

- [ ] **Wire TicketForm to POST /api/v1/tickets** - `frontend/components/tickets/TicketForm.tsx` is a stub. I need to add form state, validation, and a call to `api.post('/tickets', payload)`. On success refresh the TicketTable. On failure show ErrorToast.
  - Files: `frontend/components/tickets/TicketForm.tsx`, `frontend/hooks/useTickets.ts` (needs creating)
  - Depends on: login page (user must be authenticated), auth/me (created_by field)

- [ ] **Create GET /api/v1/audit endpoint** - `audit_service.log_action()` exists but there is no route to read the audit log. I need a paginated `GET /audit-logs` endpoint in a new `backend/app/routes/audit.py`, admin-only via role check on the JWT.
  - Wire to `frontend/components/admin/AuditLog.tsx` once the endpoint exists.

- [ ] **Wire UserTable to admin endpoint** - `frontend/components/admin/UserTable.tsx` is a stub. I need a `GET /api/v1/auth/users` admin-only route in `backend/app/routes/auth.py` that returns a paginated list of users, then wire the frontend table to it.

- [ ] **Device detail page - full implementation** - `frontend/app/devices/[id]/page.tsx` is a stub. I want it to fetch the device by ID from `GET /api/v1/devices/{id}`, fetch the latest telemetry reading, and render `<SensorGrid reading={latest} />` with a `<TelemetryChart deviceId={id} />` below it.
  - The hook `useTelemetry(deviceId)` already exists and can be used client-side.

- [ ] **Wire audit calls into routes** - `audit_service.log_action()` is never called anywhere. I need to add calls in: `DELETE /devices/{id}`, `PATCH /alerts/{id}/resolve`, `POST /firmware/upload`, `POST /tickets`, ticket status changes. Each call needs the `current_user.id` from the JWT dependency.

### Medium priority

- [ ] **Historical per-sensor charts with time range selector** - The current TelemetryChart shows all sensor data overlaid on one chart. I want individual expandable charts per sensor category (temperature, vibration, power, etc.) with a time range picker (last 1h, 6h, 24h, 7d). Backend needs a `?sensor=temperature&from=ISO&to=ISO` query param on `GET /api/v1/telemetry`.

- [ ] **Alert rules configuration UI** - The alert rules table exists in the DB and the `alert_rules` ORM model exists. I need a UI in the admin panel to create, edit and delete alert rules (sensor field, operator, threshold, severity, message) without touching the database directly. Routes for CRUD on `/api/v1/alert-rules` need to be added.

- [ ] **Demo mode with simulated sensor data** - For demos and testing without hardware, I want a `POST /api/v1/demo/start` endpoint that registers a virtual device and begins posting realistic simulated telemetry (sinusoidal temperature, random vibration noise, occasional anomaly injection) on a configurable interval. Useful for showing the dashboard to someone before hardware arrives.

- [ ] **Export telemetry as CSV** - A `GET /api/v1/telemetry/export?device_id=X&from=ISO&to=ISO` endpoint that streams a CSV file. Frontend button in the device detail page that triggers the download. Useful for offline ML experimentation before the evaluate pipeline is built.

- [ ] **Multiple device comparison view** - A dashboard page that shows two or three devices side by side with their TelemetryChart and SensorGrid. Useful once all four nodes are live - I can compare ESP32 and Pico 2W BME280 readings to spot sensor drift.

- [ ] **Implement ML evaluate.py** - `backend/ml/evaluate.py` is a skeleton. Once Week 10 hardware data is collected I need to implement `evaluate_precision_recall`, `plot_anomaly_distribution` and `generate_report`. The plan: run `train.py`, call `evaluate`, check precision/recall, tune the `ANOMALY_SCORE_THRESHOLD` env var.

### Low priority

- [ ] **Uptime Kuma status page** - Self-hosted Uptime Kuma monitoring the Render backend URL and the Vercel frontend URL. Public status page linked from phaemos.com footer and README. Alerts via Discord webhook when either goes down.

- [ ] **Per-node dashboard filters** - A dropdown in the dashboard header to filter telemetry by `node_type` (esp32, stm32, pico_w, nano). Currently all readings from all nodes mix in the chart. I want to isolate one node at a time for debugging.

- [ ] **Firmware version tracking in the dashboard** - The `firmware_version` column exists on the devices table. I want the OTA upload route to write the uploaded filename as the version, and the DeviceCard to display it alongside the device status.

- [ ] **Telemetry data retention policy** - Once the system is live, the telemetry table will grow fast (4 nodes x 12 readings/minute x 1440 min/day = 69,120 rows/day). I want a scheduled PostgreSQL job (pg_cron) or a FastAPI background task that deletes rows older than 90 days and logs the row count deleted to the audit log.

- [ ] **Dark/light mode toggle in the dashboard** - The UI uses dark Tailwind classes fixed in the layout. I want a toggle stored in localStorage that switches the `dark` class on the html element so the dashboard works in both modes.
