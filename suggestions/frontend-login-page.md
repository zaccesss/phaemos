# Frontend login page

**Why it matters:** JWT auth exists in the backend and tokens are read from localStorage, but there is no login UI - anyone who is not a developer cannot access the dashboard.

**Rough approach:**
- Create `frontend/app/login/page.tsx` with an email/password form
- On submit, POST to `/api/v1/auth/login` and store the returned token in localStorage
- Redirect to `/` on success
- Add a logout button to the nav that clears the token and redirects to `/login`
- Protect all dashboard routes with a middleware check for the token
- Also add a sign-up page at `frontend/app/register/page.tsx`

**Priority:** high
