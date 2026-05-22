# Rate limiting

**Why it matters:** The telemetry ingest endpoint is completely open - a single client can flood it with thousands of requests per second, exhausting the database connection pool and taking down the service.

**Rough approach:**
- Add `slowapi` to `backend/requirements.txt`
- Initialise the limiter in `backend/app/main.py`: `limiter = Limiter(key_func=get_remote_address)`
- Apply `@limiter.limit("60/minute")` to `POST /telemetry`
- Apply `@limiter.limit("10/minute")` to `POST /auth/login` (brute-force protection)
- Apply `@limiter.limit("5/minute")` to `POST /auth/register`
- Add the `SlowAPIMiddleware` to the FastAPI app

**Priority:** high - should be done before any public deployment
