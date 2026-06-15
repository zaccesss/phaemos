# PHAEMOS Engineering Log

This file is an index. The full session detail lives in `logs/`.

See [logs/README.md](logs/README.md) for the logging rules and format.

---

## Session index

| Date | Summary |
|---|---|
| [2026-05-22](logs/2026-05-22.md) | Initial build, production hardening (tests, CI, WebSocket, OTA, notifications, Grafana), governance setup (CLAUDE.md, hooks, logs, suggestions, sql) |
| [2026-05-30](logs/2026-05-30.md) | v2.0 hardware spec implementation: extended telemetry schema (18 new sensor columns), ESP32 v2 structured firmware (10 sensors, 4 outputs, 3 comms modules), STM32 Black Pill + Arduino Nano + Pico 2W firmware, backend additions (audit service, migrations SQL, ML evaluate skeleton), frontend additions (hooks, UI components, SensorGrid, TicketTable), hardware wiring guides, sensor reference, deployment guide, verification tracker, README and CHANGELOG updates. PRs #47-#52 merged. |
| [2026-06-01](logs/2026-06-01.md) | Completed all 15 remaining backlog items. Backend: JWT auth dependencies wired, audit route, audit_service calls in all mutating routes, alert rules CRUD, CSV export, demo mode (APScheduler), node_type filter, firmware_version column, 90-day retention task, ML evaluate.py fully implemented. Frontend: TicketForm/UserTable/AuditLog/AlertRulesPanel wired, device detail page, TelemetryChart redesigned (per-sensor groups + time range picker), compare page, global nav, dark/light mode toggle. PRs #55-#70 opened; #55-#68 merged, #69-#70 CI pending. |
| [2026-06-15](logs/2026-06-15.md) | Added GitHub Sponsors to FUNDING.yml as first entry; reordered to github, buy_me_a_coffee, patreon. PR #142. |
