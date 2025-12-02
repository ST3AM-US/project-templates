# {PROJECT_NAME} Architecture

> **Template for Vertical Slice Python Applications with CLI + Web Interfaces**
>
> This is a generic architecture template. Sections marked with `[OPTIONAL]` can be removed if not applicable to your project.
>
> **Replace placeholders**: `{PROJECT_NAME}`, `{PACKAGE_NAME}`, `{YOUR_DESCRIPTION}`

---

## Overview

**{PROJECT_NAME}** is {YOUR_DESCRIPTION}.

### Key Features

- Feature 1
- Feature 2
- Feature 3

### Problem Being Solved

Describe the problem domain and why this project exists.

---

## Technology Stack

| Component | Technology | Version | Purpose |
|-----------|------------|---------|---------|
| Language | Python | 3.12+ | Core language |
| CLI Framework | Typer | latest | Command-line interface |
| Web Framework | FastAPI | latest | REST API |
| ASGI Server | Uvicorn | latest | Production server |
| Config | Pydantic Settings + TOML | latest | Configuration management |
| **[OPTIONAL]** Database | (e.g., DuckDB, PostgreSQL) | - | Data persistence |
| **[OPTIONAL]** Cache | (e.g., Redis) | - | Caching layer |
| Package Manager | uv | latest | Dependency management |

### Key Dependencies

- `typer` - CLI framework with rich formatting
- `fastapi` - Modern web framework
- `pydantic` - Data validation and settings
- `pydantic-settings` - Configuration management
- **[OPTIONAL]** Additional dependencies

---

## Architecture Pattern

### Vertical Slice Architecture

This project follows the **Vertical Slice Architecture** pattern, where features are organized by business capability rather than technical layer.

#### Structure

Each feature "slice" contains:
- **Models**: Domain models and data structures
- **Service**: Business logic
- **Router/Commands**: Interface layer (API or CLI)
- **[OPTIONAL] Repository**: Data access layer
- **[OPTIONAL] Schemas**: API request/response models

#### Benefits

1. **High Cohesion**: All code for a feature is co-located
2. **Low Coupling**: Features are independent and can evolve separately
3. **Easy Testing**: Each slice can be tested in isolation
4. **Clear Boundaries**: Minimal shared dependencies
5. **Maintainability**: Changes to one feature don't ripple across the codebase

#### Trade-offs

- **Duplication**: Some code may be duplicated across slices (acceptable trade-off)
- **Learning Curve**: Developers need to understand the pattern
- **Shared Code**: Requires discipline to minimize shared dependencies

#### When to Use

- Projects with distinct business capabilities
- Teams working on different features concurrently
- Applications that need to scale independently by feature
- Long-lived projects that will evolve over time

---

## Directory Structure

```
{project-name}/
├── src/
│   └── {package_name}/
│       ├── __init__.py                  # Package initialization
│       ├── main.py                      # FastAPI app entry point
│       ├── cli.py                       # Typer CLI entry point
│       ├── config.py                    # Pydantic Settings with TOML support
│       ├── dependencies.py              # FastAPI dependency injection
│       │
│       ├── api/                         # Web Interface (FastAPI)
│       │   ├── __init__.py
│       │   └── routers/                 # Feature-specific API routers
│       │       ├── __init__.py
│       │       ├── feature1.py          # Feature 1 API endpoints
│       │       └── feature2.py          # Feature 2 API endpoints
│       │
│       ├── commands/                    # CLI Commands (Typer)
│       │   ├── __init__.py
│       │   ├── feature1.py              # Feature 1 CLI commands
│       │   └── feature2.py              # Feature 2 CLI commands
│       │
│       ├── feature1/                    # Vertical Slice: Feature 1
│       │   ├── __init__.py
│       │   ├── service.py               # Business logic
│       │   ├── models.py                # Domain models
│       │   ├── schemas.py               # [OPTIONAL] Pydantic schemas (API)
│       │   └── repository.py            # [OPTIONAL] Data access layer
│       │
│       ├── feature2/                    # Vertical Slice: Feature 2
│       │   └── ...                      # Same structure as feature1
│       │
│       ├── shared/                      # Cross-cutting concerns
│       │   ├── __init__.py
│       │   ├── core/                    # Core infrastructure
│       │   │   ├── database.py          # [OPTIONAL] DB connection
│       │   │   ├── cache.py             # [OPTIONAL] Cache setup
│       │   │   └── exceptions.py        # Custom exceptions
│       │   └── utils/                   # Shared utilities
│       │       ├── logging.py           # Logging setup
│       │       └── validators.py        # Common validators
│       │
│       └── clients/                     # [OPTIONAL] External API clients
│           ├── __init__.py
│           └── external_service.py      # External service client
│
├── tests/
│   ├── conftest.py                      # Pytest fixtures
│   ├── unit/                            # Unit tests (per feature)
│   │   ├── test_feature1_service.py
│       └── test_feature2_service.py
│   ├── integration/                     # Integration tests
│   │   ├── test_feature1_integration.py
│   │   └── test_api_endpoints.py
│   └── fixtures/                        # Test data and fixtures
│       └── sample_data.json
│
├── scripts/                             # Standalone utilities (legacy/one-off)
│   └── utility_script.py
│
├── docs/                                # Documentation
│   ├── ARCHITECTURE.md                  # This file
│   ├── API.md                           # [OPTIONAL] API documentation
│   └── DEVELOPMENT.md                   # [OPTIONAL] Development guide
│
├── plans/                               # Implementation plans (gitignored)
├── config.toml.example                  # Configuration template
├── .env.example                         # Environment variables template
├── pyproject.toml                       # Project configuration
├── CLAUDE.md                            # Claude Code instructions
└── README.md                            # Project overview
```

### Directory Explanations

| Directory | Purpose | Guidelines |
|-----------|---------|------------|
| `api/routers/` | FastAPI route handlers | Thin layer, delegate to services |
| `commands/` | Typer CLI commands | Thin layer, delegate to services |
| `{feature}/` | Feature slice | All feature code co-located |
| `shared/` | Cross-cutting concerns | Keep minimal, prefer duplication |
| `clients/` | External API wrappers | Isolate external dependencies |

---

## Interfaces

### CLI Interface (Typer)

#### Entry Point (`cli.py`)

```python
import typer
from {package_name}.commands import feature1, feature2

app = typer.Typer(
    name="{toolname}",
    help="{Tool description}",
    add_completion=False
)

# Register feature subcommands
app.add_typer(feature1.app, name="feature1", help="Feature 1 commands")
app.add_typer(feature2.app, name="feature2", help="Feature 2 commands")

@app.command()
def serve(
    host: str = typer.Option("127.0.0.1", help="Host to bind"),
    port: int = typer.Option(8000, help="Port to bind"),
    reload: bool = typer.Option(False, help="Enable auto-reload")
):
    """Start the API server."""
    import uvicorn
    uvicorn.run("{package_name}.main:app", host=host, port=port, reload=reload)

if __name__ == "__main__":
    app()
```

#### Feature Command (`commands/feature1.py`)

```python
import typer
from {package_name}.feature1.service import Feature1Service
from {package_name}.config import get_settings

app = typer.Typer()

@app.command()
def action(
    input: str = typer.Argument(..., help="Input parameter"),
    verbose: bool = typer.Option(False, "--verbose", "-v", help="Verbose output")
):
    """Perform feature1 action."""
    settings = get_settings()
    service = Feature1Service()

    result = service.perform_action(input)

    if verbose:
        typer.echo(f"Result: {result}")
    else:
        typer.echo(result)
```

#### Context/State Management

For sharing state across commands:

```python
# Use Typer context
@app.callback()
def main(ctx: typer.Context):
    """Global setup."""
    settings = get_settings()
    ctx.obj = {"settings": settings}

@app.command()
def command(ctx: typer.Context):
    """Access shared context."""
    settings = ctx.obj["settings"]
```

#### **[OPTIONAL]** Progress Indicators

```python
from rich.progress import Progress, SpinnerColumn, TextColumn

with Progress(
    SpinnerColumn(),
    TextColumn("[progress.description]{task.description}"),
) as progress:
    task = progress.add_task("Processing...", total=None)
    # Do work
    progress.update(task, completed=True)
```

#### **[OPTIONAL]** Rich Output Formatting

```python
from rich.console import Console
from rich.table import Table

console = Console()

table = Table(title="Results")
table.add_column("Name", style="cyan")
table.add_column("Value", style="magenta")
table.add_row("Item 1", "Value 1")

console.print(table)
```

---

### Web API (FastAPI)

#### Application Setup (`main.py`)

```python
from fastapi import FastAPI
from {package_name}.api.routers import feature1, feature2
from {package_name}.config import get_settings

def create_app() -> FastAPI:
    """Create and configure FastAPI application."""
    settings = get_settings()

    app = FastAPI(
        title="{Project Name}",
        description="{Project description}",
        version="0.1.0"
    )

    # Include routers
    app.include_router(
        feature1.router,
        prefix="/api/v1/feature1",
        tags=["Feature 1"]
    )
    app.include_router(
        feature2.router,
        prefix="/api/v1/feature2",
        tags=["Feature 2"]
    )

    return app

app = create_app()
```

#### Feature Router (`api/routers/feature1.py`)

```python
from fastapi import APIRouter, Depends, HTTPException
from {package_name}.feature1.service import Feature1Service
from {package_name}.feature1.schemas import Feature1Request, Feature1Response
from {package_name}.dependencies import get_feature1_service

router = APIRouter()

@router.post("/action", response_model=Feature1Response)
async def perform_action(
    request: Feature1Request,
    service: Feature1Service = Depends(get_feature1_service)
):
    """Perform feature1 action."""
    try:
        result = service.perform_action(request.input)
        return Feature1Response(result=result)
    except Exception as e:
        raise HTTPException(status_code=400, detail=str(e))
```

#### Dependency Injection (`dependencies.py`)

```python
from {package_name}.feature1.service import Feature1Service
from {package_name}.config import get_settings

def get_feature1_service() -> Feature1Service:
    """Get Feature1Service instance."""
    settings = get_settings()
    return Feature1Service(settings)
```

#### **[OPTIONAL]** WebSocket Support

```python
from fastapi import WebSocket

@router.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    await websocket.accept()
    try:
        while True:
            data = await websocket.receive_text()
            await websocket.send_text(f"Echo: {data}")
    except:
        await websocket.close()
```

#### **[OPTIONAL]** Background Tasks

```python
from fastapi import BackgroundTasks

def background_job(param: str):
    # Long-running task
    pass

@router.post("/async-action")
async def async_action(
    background_tasks: BackgroundTasks,
    input: str
):
    background_tasks.add_task(background_job, input)
    return {"status": "processing"}
```

---

## Configuration

### Pattern: TOML + Pydantic Settings

Configuration uses **Pydantic Settings** with custom TOML loader for structured configuration.

#### Settings Class (`config.py`)

```python
from pathlib import Path
from pydantic import BaseModel, Field
from pydantic_settings import BaseSettings, SettingsConfigDict
import tomllib

class Feature1Settings(BaseModel):
    """Feature 1 configuration."""
    param1: str = Field(default="default", description="Parameter 1")
    param2: int = Field(default=10, description="Parameter 2")

class Settings(BaseSettings):
    """Application settings."""

    model_config = SettingsConfigDict(
        env_file=".env",
        env_file_encoding="utf-8",
        case_sensitive=False,
        extra="ignore"
    )

    # App settings
    app_host: str = Field(default="127.0.0.1", description="Host")
    app_port: int = Field(default=8000, description="Port")
    app_debug: bool = Field(default=False, description="Debug mode")

    # Feature settings (nested)
    feature1: Feature1Settings = Field(default_factory=Feature1Settings)

    @classmethod
    def from_toml(cls, toml_file: str = "config.toml") -> "Settings":
        """Load settings from TOML file."""
        if not Path(toml_file).exists():
            return cls()

        with open(toml_file, "rb") as f:
            toml_data = tomllib.load(f)

        return cls(**toml_data)

# Singleton
_settings: Settings | None = None

def get_settings() -> Settings:
    """Get application settings."""
    global _settings
    if _settings is None:
        _settings = Settings.from_toml()
    return _settings
```

#### Configuration File (`config.toml.example`)

```toml
[app]
host = "127.0.0.1"
port = 8000
debug = false

[feature1]
param1 = "value"
param2 = 20
```

### Priority Order

1. **Init parameters** - Passed to Settings constructor
2. **Environment variables** - OS environment
3. **`.env` file** - Environment file
4. **`config.toml`** - TOML configuration
5. **Default values** - Defined in Settings class

### Nested Settings Models

Group related settings into nested models:

```python
class DatabaseSettings(BaseModel):
    url: str
    pool_size: int = 10

class Settings(BaseSettings):
    database: DatabaseSettings
```

#### **[OPTIONAL]** Multi-Environment Support

```python
class Settings(BaseSettings):
    environment: str = "development"

    @classmethod
    def from_toml(cls, env: str = "development"):
        with open("config.toml", "rb") as f:
            config = tomllib.load(f)
        return cls(**config[env])
```

#### **[OPTIONAL]** Secrets Management

```python
from pydantic import SecretStr

class Settings(BaseSettings):
    api_key: SecretStr  # Never logged in plaintext

    def get_api_key(self) -> str:
        return self.api_key.get_secret_value()
```

---

## Service Layer

### Service Class Structure

Services contain business logic and are framework-agnostic.

```python
# feature1/service.py
from typing import Optional
from {package_name}.feature1.models import Feature1Model
from {package_name}.config import Feature1Settings

class Feature1Service:
    """Business logic for Feature 1."""

    def __init__(self, settings: Optional[Feature1Settings] = None):
        self.settings = settings or Feature1Settings()

    def perform_action(self, input: str) -> str:
        """Perform business logic."""
        # Business logic here
        return f"Processed: {input}"

    def validate_input(self, input: str) -> bool:
        """Validate input."""
        return len(input) > 0
```

### Dependency Injection

Services are instantiated in `dependencies.py` and injected into routes:

```python
# dependencies.py
def get_feature1_service() -> Feature1Service:
    settings = get_settings()
    return Feature1Service(settings.feature1)
```

### **[OPTIONAL]** Mixins

Add cross-cutting concerns via mixins:

```python
class AuditMixin:
    """Audit logging mixin."""
    def log_action(self, action: str, user: str):
        print(f"[AUDIT] {user} performed {action}")

class ValidationMixin:
    """Validation helpers."""
    def validate_not_empty(self, value: str):
        if not value:
            raise ValueError("Value cannot be empty")

class Feature1Service(AuditMixin, ValidationMixin):
    """Service with mixins."""
    pass
```

### **[OPTIONAL]** Repository Pattern

Separate data access from business logic:

```python
# feature1/repository.py
class Feature1Repository:
    """Data access for Feature 1."""

    def get_by_id(self, id: str) -> Feature1Model:
        # Data access logic
        pass

    def save(self, model: Feature1Model):
        # Data persistence logic
        pass

# feature1/service.py
class Feature1Service:
    def __init__(self, repository: Feature1Repository):
        self.repository = repository

    def get_item(self, id: str):
        return self.repository.get_by_id(id)
```

---

## Data Flow

### CLI Request Lifecycle

```
User Command
    ↓
cli.py (Typer app)
    ↓
commands/feature.py (Command handler)
    ↓
feature/service.py (Business logic)
    ↓
[OPTIONAL] feature/repository.py (Data access)
    ↓
Response to User
```

### API Request Lifecycle

```
HTTP Request
    ↓
main.py (FastAPI app)
    ↓
api/routers/feature.py (Route handler)
    ↓
dependencies.py (DI)
    ↓
feature/service.py (Business logic)
    ↓
[OPTIONAL] feature/repository.py (Data access)
    ↓
JSON Response
```

### Service Interactions

Services can call other services:

```python
class Feature2Service:
    def __init__(self, feature1_service: Feature1Service):
        self.feature1_service = feature1_service

    def combined_action(self):
        result1 = self.feature1_service.perform_action("input")
        # Use result1
        return result
```

**Guideline**: Keep service dependencies minimal to maintain low coupling.

### **[OPTIONAL]** Event-Driven Patterns

For decoupled communication:

```python
class EventBus:
    def publish(self, event: str, data: dict):
        # Publish event
        pass

    def subscribe(self, event: str, handler):
        # Subscribe to event
        pass
```

### **[OPTIONAL]** Caching Strategies

```python
from functools import lru_cache

class Feature1Service:
    @lru_cache(maxsize=128)
    def expensive_operation(self, input: str) -> str:
        # Cached result
        return result
```

---

## **[OPTIONAL]** Storage Patterns

### Storage Strategy Pattern

Abstraction for different storage backends:

```python
class StorageStrategy:
    def get(self, key: str):
        raise NotImplementedError

    def set(self, key: str, value):
        raise NotImplementedError

class MemoryStorage(StorageStrategy):
    def get(self, key: str):
        return self.data.get(key)

    def set(self, key: str, value):
        self.data[key] = value

class DatabaseStorage(StorageStrategy):
    def get(self, key: str):
        # Query database
        pass
```

### Cache-Fallback Pattern

```python
class CacheFallbackStorage:
    def __init__(self, cache: StorageStrategy, persistent: StorageStrategy):
        self.cache = cache
        self.persistent = persistent

    def get(self, key: str):
        # Try cache first
        value = self.cache.get(key)
        if value is None:
            # Fallback to persistent
            value = self.persistent.get(key)
            if value:
                self.cache.set(key, value)
        return value
```

### Database Migrations

Use Alembic for schema migrations:

```bash
alembic init migrations
alembic revision --autogenerate -m "Initial schema"
alembic upgrade head
```

---

## Testing Strategy

This project follows **Test-Driven Development (TDD)** principles. For comprehensive testing guidelines, patterns, and examples, see **[docs/TESTING_STRATEGY.md](./TESTING_STRATEGY.md)**.

### Quick Reference

- **Methodology**: Test-Driven Development (TDD) - write tests first, then implementation
- **Test Organization**: `tests/unit/`, `tests/integration/`, `tests/fixtures/`
- **Test Framework**: pytest with fixtures in `conftest.py`
- **Coverage**: Unit tests per service, integration tests for APIs/CLI, [OPTIONAL] E2E tests
- **Patterns**: Arrange-Act-Assert, test isolation, fixture reuse
- **CI/CD Integration**: Automated test runs on commits/PRs

See [docs/TESTING_STRATEGY.md](./TESTING_STRATEGY.md) for detailed guidance on:
- TDD workflow and best practices
- Test structure and organization
- Fixture patterns
- Mocking strategies
- Coverage requirements

---

## **[OPTIONAL]** Deployment

### Container Patterns

**Dockerfile**:
```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY pyproject.toml .
RUN pip install uv && uv sync

COPY src/ src/
COPY config.toml.example config.toml

EXPOSE 8000

CMD ["uv", "run", "{toolname}", "serve", "--host", "0.0.0.0"]
```

### Environment Configuration

Use environment variables in production:

```bash
export APP_HOST=0.0.0.0
export APP_PORT=8000
export FEATURE1__PARAM1=production_value
```

### Health Checks

```python
@app.get("/health")
async def health_check():
    """Health check endpoint."""
    return {"status": "healthy"}
```

---

## References

### Implementations

- **Example Project 1**: Link to actual implementation
- **Example Project 2**: Link to actual implementation

### External Documentation

- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [Typer Documentation](https://typer.tiangolo.com/)
- [Pydantic Documentation](https://docs.pydantic.dev/)
- [Vertical Slice Architecture](https://www.jimmybogard.com/vertical-slice-architecture/)

### Related Patterns

- Clean Architecture
- Domain-Driven Design
- CQRS (Command Query Responsibility Segregation)

---

**Last Updated**: {DATE}
**Template Version**: 1.0.0
