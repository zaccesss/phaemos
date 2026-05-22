# Audit logging

**Why it matters:** The `audit_logs` table was designed into the schema but nothing writes to it - there is no record of who deleted a device, who resolved an alert or who uploaded firmware.

**Rough approach:**
- Create `backend/app/models/audit_log.py` ORM model (or verify it already exists in schema)
- Write a `log_action(user_id, action, target, detail, db)` helper function
- Call it in: `DELETE /devices`, `PATCH /alerts/{id}/resolve`, `POST /firmware/upload`, `POST /devices`, ticket status changes
- This requires the `/auth/me` endpoint to be working first so user_id is available
- Add `GET /audit-logs` endpoint (admin only) to expose the log

**Priority:** medium - depends on auth/me being implemented first
