# Habit Tracker

A small REST API for tracking daily habits — create habits, check in on them each day, and view streak/completion statistics. Built with FastAPI and SQLAlchemy.

## Main features

- **Create, list, retrieve, and update habits** (name, description) via a REST API
- **Archive habits** (mark inactive) and filter lists to active habits only
- **Daily check-ins** for a habit, with a unique constraint of one check-in per date and an optional note
- **List and delete check-ins** for a habit
- **Statistics per habit**: current streak, longest streak, total check-ins, and 30-day completion rate
- **Persistence** with SQLAlchemy (defaults to a local SQLite database, configurable via `DATABASE_URL`)
- **Pydantic request/response validation** (e.g. blank habit names are rejected)
- **Automated tests** using FastAPI's `TestClient` and an in-memory SQLite database

## Installation

This project uses [uv](https://docs.astral.sh/uv/) for dependency management (a `uv.lock` lockfile and `pyproject.toml` are provided). It requires Python 3.12+.

```bash
uv sync
```

If you prefer pip, install the dependencies from `pyproject.toml` manually:

```bash
pip install fastapi "uvicorn[standard]" sqlalchemy python-multipart
pip install pytest httpx  # for running the tests
```

### Environment variables

Only one variable is used, and it is optional:

- `DATABASE_URL` — SQLAlchemy database URL. Defaults to `sqlite:///./habits.db` if unset (see `app/database.py`).

No `.env.example` file is present in the repository.

## Running locally

Start the API with Uvicorn (the FastAPI app instance lives in `app.main`):

```bash
uv run uvicorn app.main:app --reload
```

The API will be available at `http://127.0.0.1:8000`, and the interactive API docs at `http://127.0.0.1:8000/docs`.

### Running the tests

```bash
uv run pytest
```

## Project structure

- `app/` — application package containing the API and data layer
  - `main.py` — FastAPI app and route definitions (habits, check-ins, stats)
  - `models.py` — SQLAlchemy ORM models (`Habit`, `Checkin`)
  - `schemas.py` — Pydantic request/response schemas with validation
  - `crud.py` — database operations for habits and check-ins
  - `stats.py` — streak and completion-rate calculations
  - `database.py` — engine/session setup and table creation (reads `DATABASE_URL`)
- `tests/` — test suite (`test_habits.py`, `test_checkins.py`, `conftest.py`)
- `pyproject.toml` — project metadata and dependencies
- `uv.lock` — locked dependency versions for reproducible installs

## Notes

- Database tables are created automatically on startup (see the app lifespan in `app/main.py`).
- The default SQLite database file (`habits.db`) is created in the working directory at runtime.