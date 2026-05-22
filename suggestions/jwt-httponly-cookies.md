# JWT in httpOnly cookies

**Why it matters:** Storing the JWT in localStorage means any XSS vulnerability in the frontend can steal the token and impersonate the user. httpOnly cookies are not accessible to JavaScript and are the safer production pattern.

**Rough approach:**
- Change `POST /auth/login` to return the token as a `Set-Cookie: token=...; HttpOnly; Secure; SameSite=Strict` header instead of in the response body
- Update the axios client in `frontend/lib/api.ts` to use `withCredentials: true` instead of the localStorage interceptor
- Update the CORS configuration to allow credentials from the frontend origin
- Handle logout by calling a `POST /auth/logout` endpoint that clears the cookie server-side

**Priority:** low for portfolio/dev, high before any real user deployment
