# Offline device detection

**Why it matters:** A device that crashes or loses power silently disappears from the dashboard - no alert fires, no ticket is created. Users have no way to know a device is down unless they notice the chart has stopped updating.

**Rough approach:**
- Add a FastAPI `startup` event or an APScheduler background job that runs every 5 minutes
- Query all devices where `last_seen < now() - interval '10 minutes'` and `status != 'offline'`
- For each matching device: set `status = 'offline'` and insert an Alert row with severity `warning` and a message like "Device {name} has not reported for over 10 minutes"
- Fire the notify service for critical machines
- Add a configurable `DEVICE_OFFLINE_THRESHOLD_MINUTES` env var

**Priority:** medium
