# Alembic migrations

**Why it matters:** `main.py` currently calls `Base.metadata.create_all()` which will silently skip any schema changes on an existing database - or in a worst case destroy data if tables are dropped and recreated.

**Rough approach:**
- Run `alembic init backend/alembic` to create the migrations folder
- Configure `alembic.ini` to use the `DATABASE_URL` env var
- Generate the initial migration from the current models: `alembic revision --autogenerate -m "initial schema"`
- Remove `Base.metadata.create_all(bind=engine)` from `main.py`
- Replace it with an Alembic upgrade call on startup, or run `alembic upgrade head` as part of the Docker entrypoint
- Add the `backend/alembic/` folder to version control
- Add `alembic upgrade head` to the Render deploy command

**Priority:** high - must be done before any production deployment with real data
