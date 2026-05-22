# Password strength validation

**Why it matters:** The register endpoint accepts any string as a password including single characters, which would allow extremely weak accounts.

**Rough approach:**
- Add a validator to `UserRegister` in `backend/app/schemas/user.py` using Pydantic's `@field_validator`
- Enforce: minimum 8 characters, at least one uppercase letter, at least one digit
- Return a clear 422 error with the specific rule that was violated
- Add matching client-side validation in the frontend register form (once built)

**Priority:** medium
