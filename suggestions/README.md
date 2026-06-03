# Suggestions

A living backlog of improvements for PHAEMOS. Updated at the end of every session.

- [x] = implemented and merged
- [ ] = not yet done

Read this at the start of every session to know what to work on next.

---

## Backlog

All software backlog items are complete as of PR 134. The remaining work is hardware-blocked.

### Completed (software)

- [x] **Auth on remaining device endpoints** - POST /devices, PATCH /devices/{id} and GET /devices/{id} - done in PR 88
- [x] **Refresh token / silent JWT renewal** - 15-min access token + 7-day httpOnly refresh cookie - done in PR 89 (backend) and PR 99 (frontend interceptor)
- [x] **Device ownership assignment UI** - owner picker on device detail page (admin only) - done in PR 106
- [x] **Tab visibility API in useTelemetry** - pauses polling when tab is hidden - done in PR 107

---

## Hardware-blocked (do NOT attempt until hardware arrives and is tested)

- [ ] **Custom node enclosure design** - After all 4 nodes tested on breadboard. Options: 3D print (Aston lab), laser cut acrylic, CNC aluminium.
- [ ] **Hardware testing** - Test full sensor suite on all 4 boards. See hardware/wiring/ for pinouts.
- [ ] **Train Isolation Forest** - After 1-2 weeks of real telemetry, run backend/ml/train.py and evaluate with evaluate.py.
