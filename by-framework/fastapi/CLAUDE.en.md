# CLAUDE.md - FastAPI Project Conventions

## Build & Run

- Dependency management: `uv` or `poetry` — lock file must be committed to the repository
- Start dev server: `uvicorn app.main:app --reload --port 8000`
- Run tests: `pytest -xvs`; Lint: `ruff check . && ruff format --check .`
- Python version: 3.11+, fully leverage type annotations

## Project Structure

- `app/main.py` - Entry point, registers routers and middleware
- `app/routers/` - Routes split by business domain (e.g., `users.py`, `orders.py`)
- `app/models/` - SQLAlchemy ORM models; `app/schemas/` - Pydantic v2 schemas
- `app/services/` - Business logic layer; `app/dependencies.py` - Shared dependency injection
- `alembic/` - Database migration scripts

## Async Model

- Route functions default to `async def`; use `def` only when calling synchronous blocking libraries
- CPU-intensive tasks should be offloaded to a thread pool via `run_in_executor` — never block the event loop
- Database: `sqlalchemy[asyncio]` + `asyncpg` — calling synchronous blocking I/O from async code is forbidden
- HTTP client: use `httpx.AsyncClient`, create a shared instance in the lifespan handler:
  ```python
  async def lifespan(app: FastAPI):
      app.state.http = httpx.AsyncClient()
      yield
      await app.state.http.aclose()
  ```
- Background tasks: use `BackgroundTasks` or Celery/ARQ — never perform long-running operations inside request handlers

## Pydantic v2 Conventions

- Request bodies and responses must use Pydantic models — returning bare `dict` is forbidden
- Define separate request and response models: `UserCreate` (input), `UserResponse` (output), `UserInDB` (internal)
- Use `ConfigDict(from_attributes=True)` instead of the legacy `orm_mode`; enable `ConfigDict(strict=True)` for input models
- Sensitive fields (e.g., password) only appear in input models — response models must explicitly list fields via `model_fields`
- Field validation via `Field(ge=0, max_length=100)` and `@field_validator`
- Reuse common fields through mixins or base classes — no copy-paste duplication
- Enum values should use `StrEnum` for automatic string serialization

## Database & Migrations

- SQLAlchemy 2.0 style: use `Mapped[]` type annotations and `mapped_column()`
- Database sessions via dependency injection: `async def get_db() -> AsyncGenerator[AsyncSession]`
- All schema changes must go through Alembic: `alembic revision --autogenerate -m "description"`
- Every migration file must have both `upgrade()` and `downgrade()` — downgrade must not be empty
- Migration files must be committed to git; run `alembic check` in CI to catch missing migrations

## Dependency Injection

- Common dependencies (auth, pagination, DB session) defined in `dependencies.py`
- Use `Annotated[User, Depends(get_current_user)]` to simplify type declarations
- Use the yield pattern for resource lifecycle management; override with `app.dependency_overrides` in tests
- Keep dependency nesting <= 3 levels deep; refactor into service classes if deeper

## Router Organization

- Each router uses `APIRouter(prefix="/resource-name", tags=["tag"])`, registered centrally in `main.py`
- `main.py` should only call `app.include_router()` — no business logic here
- Route functions handle parameter validation and call services only — business logic belongs in the `services/` layer
- RESTful naming: `list_users`, `get_user`, `create_user`, `delete_user`

## Testing Conventions

- Use `httpx.AsyncClient` + `pytest-asyncio` for API testing
- Test database: SQLite in-memory mode or Testcontainers; fixtures manage data with per-test rollback

## Common Pitfalls

- Never call synchronous blocking functions (e.g., `requests.get`, `time.sleep`) inside `async def` routes
- `BackgroundTasks` is suitable for lightweight tasks — use Celery or arq for heavy workloads
- Avoid expensive computation inside `Depends` — it runs on every request
- Always set `response_model` on routes to prevent accidental exposure of internal fields
