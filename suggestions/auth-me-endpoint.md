# /auth/me endpoint

**Why it matters:** `GET /auth/me` returns 501 - the backend cannot identify the currently logged-in user from a JWT token, which blocks role-based UI rendering and all Phase 2 personalisation.

**Rough approach:**
- Write a `get_current_user(token: str = Depends(oauth2_scheme), db: Session = Depends(get_db))` dependency in `backend/app/routes/auth.py`
- Use `python-jose` to decode the token, extract `sub` (user UUID) and `role`
- Look up the user in the DB and return it
- Replace the 501 stub in the `/me` route with a call to that dependency
- Use this dependency on any route that needs to know who is logged in (tickets created_by, audit logs, etc.)

**Priority:** high - blocks Phase 2 features
