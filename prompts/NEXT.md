# NEXT - Continuation Prompt

Read this file at the start of every new session to pick up exactly where we left off.

**Last updated:** 2026-06-01 - all backlog items complete

---

## Current status

**All 15 backlog items from suggestions/README.md are done.** PRs #55-#68 are merged to main. PRs #69 (dark mode) and #70 (Uptime Kuma docs) are in CI as of end of session - they will auto-merge when green.

The codebase is in a clean, well-organised state. The backend is feature-complete. The frontend is fully wired. Hardware is still awaited for physical testing.

---

## One-time setup (do this on every fresh clone)

```bash
git config core.hooksPath .githooks
```

---

## What was done this session (2026-06-01)

### PRs merged to main

| PR | Description |
|----|-------------|
| 55 | JWT auth deps (get_current_user, require_admin), GET /auth/me, GET /auth/users, GET /audit-logs |
| 56 | audit_service.log_action wired into devices/alerts/firmware/tickets routes |
| 57 | firmware_version added to UserResponse (hotfix follow-up to PR 55) |
| 58 | TicketForm, UserTable, AuditLog, useTickets hook wired to real endpoints |
| 59 | Full device detail page (SensorGrid + TelemetryChart + Export CSV button) |
| 60 | TelemetryChart redesigned: per-sensor groups, 1h/6h/24h/7d range, useTelemetry URL fix |
| 61 | Alert rules CRUD (GET/PUT/DELETE /alert-rules), AlertRulesPanel admin component |
| 62 | GET /telemetry/export CSV streaming endpoint, Export CSV button on device detail page |
| 63 | POST /demo/start + /demo/stop with APScheduler sinusoidal telemetry generation |
| 64 | /compare page (up to 3 devices side-by-side), global nav bar in layout.tsx |
| 65 | ml/evaluate.py fully implemented: load_model, evaluate_precision_recall, plot_anomaly_distribution, generate_report |
| 66 | node_type filter on GET /telemetry/{device_id}, dashboard node type picker buttons |
| 67 | firmware_version column on Device model, DeviceResponse, DeviceCard display |
| 68 | 90-day retention task via APScheduler cron (02:00 UTC), lifespan in main.py |
| 69 | Dark/light mode toggle (ThemeToggle + localStorage + pre-hydration script) - CI pending |
| 70 | docs/uptime-kuma.md setup guide - CI pending |

### Key fixes during session

- F401 unused import in audit.py (ruff)
- `onClose` vs `onDismiss` prop mismatch on ErrorToast (caused PR 58 and 59 to fail)
- `ObjectDeletedError` on test_resolve_alert: SQLAlchemy ORM objects expire after `db.commit()` inside `audit_service.log_action()`. Fix: call `Model.model_validate(obj)` before the audit call to capture data in a Pydantic model.
- useTelemetry was calling `/telemetry?device_id=` (query param) instead of `/telemetry/${deviceId}` (path param) - fixed in PR 60

---

## Next steps (in priority order)

1. **Wait for PR 69 and PR 70 to merge** - they are in CI and set to auto-merge. No action needed.

2. **Login page + session guard** - `frontend/app/login/page.tsx` does not exist. All JWT-protected endpoints exist but the frontend has no way to authenticate. This blocks real multi-user use. See new backlog entry in suggestions/README.md.

3. **Hardware testing** - Components are ordered. Once the ESP32, STM32, Nano and Pico 2W boards arrive, test the firmware-to-backend flow end to end. See `hardware/` and `docs/sensor_reference.md` for wiring.

4. **Train the Isolation Forest** - Once 1-2 weeks of real telemetry data is collected, run `backend/ml/train.py` and then use the newly-implemented `evaluate.py` pipeline to check precision/recall. Tune `ANOMALY_SCORE_THRESHOLD` env var if needed.

5. **WebSocket reconnect** - The dashboard WebSocket closes on error without retrying. See new backlog entry in suggestions/README.md.

---

## Blockers

None. All backlog items are done. Waiting on hardware delivery for physical testing.

---

## Key files changed this session

```
backend/app/routes/auth.py          - get_current_user, require_admin, /auth/me, /auth/users
backend/app/routes/audit.py         - GET /audit-logs (new file)
backend/app/routes/alerts.py        - resolve_alert wired, GET/PUT/DELETE /alert-rules
backend/app/routes/devices.py       - delete_device audit wired
backend/app/routes/firmware.py      - upload_firmware audit wired
backend/app/routes/tickets.py       - create/update audit wired, current_user dep
backend/app/routes/telemetry.py     - /export, from_ts/to_ts/node_type filters
backend/app/routes/demo.py          - POST /demo/start, /demo/stop (new file)
backend/app/schemas/alert.py        - AlertRuleUpdate
backend/app/schemas/device.py       - firmware_version in DeviceResponse/DeviceUpdate
backend/app/schemas/user.py         - created_at in UserResponse
backend/app/models/device.py        - firmware_version column
backend/app/tasks/retention.py      - 90-day retention cron (new file)
backend/app/main.py                 - demo + retention registered, lifespan added
backend/ml/evaluate.py              - fully implemented
backend/requirements.txt            - apscheduler, matplotlib added
frontend/app/layout.tsx             - global nav + ThemeToggle + dark mode script
frontend/app/page.tsx               - node type filter buttons
frontend/app/compare/page.tsx       - new comparison page
frontend/app/devices/[id]/page.tsx  - full implementation + Export CSV
frontend/app/admin/page.tsx         - UserTable + AlertRulesPanel + AuditLog wired
frontend/components/TelemetryChart.tsx  - deviceId prop, per-sensor groups, range picker
frontend/components/admin/AlertRulesPanel.tsx  - new file
frontend/components/admin/UserTable.tsx        - fully implemented
frontend/components/admin/AuditLog.tsx         - fully implemented
frontend/components/tickets/TicketForm.tsx     - fully implemented
frontend/components/ui/ThemeToggle.tsx         - new file
frontend/hooks/useTelemetry.ts      - URL fix, fromTs/toTs/nodeType options
frontend/hooks/useTickets.ts        - new file
frontend/types/index.ts             - AlertRule, firmware_version on Device
docs/VERIFICATION.md                - all new items marked [x]
docs/api-reference.md               - new endpoints documented
docs/architecture.md                - background tasks, frontend routes table
docs/decisions.md                   - decisions 006-009 added
docs/schema.md                      - firmware_version on devices table
docs/uptime-kuma.md                 - new file
suggestions/README.md               - all 15 items marked [x], 5 new backlog items
logs/2026-06-01.md                  - full session record
logs/LOG.md                         - 2026-06-01 row added
prompts/NEXT.md                     - this file
```

---

## Key rules reminder

- UK English (colour/behaviour/organisation/licence)
- No em/en dashes - hyphens only in code comments
- No Oxford commas
- First-person code comments only ("# I use X because Y")
- No AI co-author credits - ever
- Always update logs/YYYY-MM-DD.md and prompts/NEXT.md after every action
- Never commit directly to main
- Run `git config core.hooksPath .githooks` on every fresh clone
- Auto-merge PRs: `gh pr merge <n> --auto --merge --delete-branch`
