# Suggestions

A living backlog of improvements for PHAEMOS. Updated at the end of every session.

- [x] = implemented and merged
- [ ] = not yet done

Read this at the start of every session to know what to work on next.

---

## Backlog

### Medium priority

- [ ] **Auth on remaining device endpoints** - POST /devices, PATCH /devices/{id} and GET /devices/{id} have no auth guard. Anyone who knows a device UUID can update its name or status. Add `Depends(get_current_user)` to those three routes; PATCH should also check ownership (technician can only update their own device).

- [ ] **Refresh token / silent JWT renewal** - The current JWT has a fixed expiry and the user gets hard-logged-out when it expires. Add a short-lived access token (15 min) and a long-lived refresh token (7 days, httpOnly cookie). The frontend should silently exchange the refresh token for a new access token before expiry.

### Low priority

- [ ] **Device ownership assignment UI** - There is no frontend way to set or change `owner_id` on a device. Add an owner picker to the device detail page (admin only): a `<select>` listing all users with role=technician, PATCH to `/devices/{id}` with `{owner_id}`.

- [ ] **Tab visibility API in useTelemetry** - Polling continues when the browser tab is hidden, wasting requests. Add a `visibilitychange` event listener that pauses `setInterval` when `document.hidden` is true and resumes when the tab becomes visible again.

---

## Hardware-blocked (do NOT attempt until hardware arrives and is tested)

- [ ] **Custom node enclosure design** - After all 4 nodes tested on breadboard. Options: 3D print (Aston lab), laser cut acrylic, CNC aluminium.
- [ ] **Hardware testing** - Test full sensor suite on all 4 boards. See hardware/wiring/ for pinouts.
- [ ] **Train Isolation Forest** - After 1-2 weeks of real telemetry, run backend/ml/train.py and evaluate with evaluate.py.
