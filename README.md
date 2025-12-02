# ST3AM Project Templates

A collection of architecture templates and best practices for building Python applications following vertical slice architecture.

## Available Templates

### Vertical Slice Python Application

A comprehensive template for building Python applications with:
- **Dual Interfaces**: CLI (Typer) and Web API (FastAPI)
- **Vertical Slice Architecture**: Features organized by business capability
- **Configuration**: TOML + Pydantic Settings with multi-environment support
- **Testing**: Complete TDD methodology with pytest
- **Modern Stack**: Python 3.12+, FastAPI, Typer, Pydantic

**Documentation:**
- [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) - Complete architecture guide
- [`docs/TESTING_STRATEGY.md`](docs/TESTING_STRATEGY.md) - TDD methodology and testing patterns
- [`docs/USER_STORIES.md`](docs/USER_STORIES.md) - User story template with examples and tracking

## How to Use

### Starting a New Project

1. **Copy the template structure** to your new project
2. **Replace placeholders** throughout the templates:
   - `{PROJECT_NAME}` - Full project name (e.g., "CCI Turf Migration")
   - `{PACKAGE_NAME}` - Package name (e.g., "cci_turf")
   - `{package_name}` - Package name (lowercase with underscores)
   - `{project-name}` - Project name (lowercase with hyphens)
   - `{toolname}` - CLI tool name
   - `{YOUR_DESCRIPTION}` - Project description
   - `{DATE}` - Current date

3. **Set up GitHub issue templates**:
   - Copy `.github/ISSUE_TEMPLATE/` to your repository
   - Update `config.yml` with your repository URLs
   - Create recommended labels in GitHub

4. **Remove optional sections** marked with `[OPTIONAL]` if not needed:
   - Database and cache patterns
   - WebSocket support
   - Background tasks
   - Deployment configurations
   - Saved responses pattern for API testing
   - Epic organization (can use flat structure)

5. **Customize for your needs**:
   - Adjust technology versions
   - Add project-specific requirements
   - Modify coverage targets
   - Add custom architectural patterns
   - Configure GitHub labels and project boards

### Template Structure

```
Templates/
├── README.md                        # This file
├── CLAUDE.md                        # Instructions for Claude Code
├── .github/
│   └── ISSUE_TEMPLATE/
│       ├── user_story.yml           # User story template
│       ├── bug_report.yml           # Bug report template
│       ├── technical_debt.yml       # Technical debt template
│       └── config.yml               # Issue template configuration
└── docs/
    ├── ARCHITECTURE.md              # Vertical slice architecture template
    ├── TESTING_STRATEGY.md          # TDD methodology template
    └── USER_STORIES.md              # User story template and GitHub integration
```

## Key Concepts

### Vertical Slice Architecture

Features are organized by business capability rather than technical layer:

```
src/{package_name}/
├── feature1/           # Complete feature slice
│   ├── models.py       # Domain models
│   ├── service.py      # Business logic
│   ├── schemas.py      # API request/response models
│   └── repository.py   # [OPTIONAL] Data access
├── feature2/           # Another complete slice
└── shared/             # Minimal shared code
```

**Benefits:**
- High cohesion within features
- Low coupling between features
- Easy to test in isolation
- Clear boundaries
- Scalable teams

### Configuration Pattern

TOML-based configuration with Pydantic validation:

```toml
# config.toml
[app]
host = "127.0.0.1"
port = 8000

[feature1]
param1 = "value"
```

Priority order: Init params > Env vars > .env file > config.toml > Defaults

### Testing Strategy

Test-Driven Development (TDD) with comprehensive coverage:

- **Unit Tests**: Business logic (100% coverage goal)
- **Integration Tests**: API endpoints (95% coverage goal)
- **E2E Tests**: Complete workflows (100% critical paths)
- **Overall Target**: 90% minimum coverage

Organized by feature slice:
```
tests/
├── unit/
├── integration/
├── e2e/
└── fixtures/
```

## When to Use This Template

✅ **Good fit:**
- Business applications with distinct features
- Projects requiring both CLI and API interfaces
- Teams practicing TDD
- Long-lived projects that will evolve
- Multi-tenant or multi-instance systems

❌ **Not ideal for:**
- Simple scripts or utilities
- Microservices (too much structure)
- Projects with complex domain models (consider DDD instead)
- Proof-of-concepts or prototypes

## Examples

Projects built with this template:
- `cci-turf-reassignment` - Multi-instance OFS data management
- `duckofs` - DuckDB-based OFS data caching
- `cci-org-assignment` - Organization resource assignment

## Contributing

To improve these templates:
1. Test changes in a real project first
2. Update documentation to match
3. Preserve placeholder format
4. Keep examples complete and runnable
5. Mark truly optional sections with `[OPTIONAL]`

## Support

For questions or issues with the templates:
- Review existing projects using the template
- Check the `docs/` directory for detailed guidance
- Consult `CLAUDE.md` for Claude Code integration

## Emoticon Usage

This template uses a minimal set of approved emoticons for clarity:
- ✅ Green checkmark - Completed items and success states
- ❌ Red X - Errors and failures
- ⚠️ Warning - Warnings and cautions
- 🔄 Refresh - In-progress items

No other emoticons are used to maintain a professional tone.

---

**Template Version**: 1.0.0
**Last Updated**: 2024-12-02
**Maintained by**: ST3AM Team
