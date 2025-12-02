# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a **templates repository** containing architecture templates for Python applications. The primary template is for vertical slice architecture with dual CLI (Typer) and Web (FastAPI) interfaces.

## Working with Templates

### Template Structure

- **docs/ARCHITECTURE.md**: The main template file for vertical slice Python applications
- **docs/TESTING_STRATEGY.md**: Comprehensive TDD methodology and testing patterns
- **docs/USER_STORIES.md**: User story template with examples and tracking guidance
- Contains placeholders: `{PROJECT_NAME}`, `{PACKAGE_NAME}`, `{YOUR_DESCRIPTION}`, `{toolname}`, `{project-name}`, `{package_name}`, `{DATE}`, `{PROJECT_DESCRIPTION}`
- Includes OPTIONAL sections marked with `[OPTIONAL]` that can be removed if not applicable

### When Users Request Template Customization

1. **Read the templates first**: Always read `docs/ARCHITECTURE.md`, `docs/TESTING_STRATEGY.md`, and `docs/USER_STORIES.md` before making suggestions
2. **Preserve structure**: Maintain the overall organization and flow
3. **Keep placeholders**: Don't replace placeholders unless specifically asked
4. **Respect OPTIONAL markers**: Keep sections marked `[OPTIONAL]` unless user wants them removed
5. **Update version info**: Update "Last Updated" and "Template Version" when making significant changes

### Template Sections

The template includes:
- **Overview**: Project description and features
- **Technology Stack**: Core technologies (Python 3.12+, Typer, FastAPI, Pydantic)
- **Architecture Pattern**: Vertical Slice Architecture explanation
- **Directory Structure**: Complete project layout
- **Interfaces**: CLI (Typer) and Web API (FastAPI) patterns
- **Configuration**: TOML + Pydantic Settings pattern
- **Service Layer**: Business logic patterns
- **Data Flow**: Request lifecycles
- **Storage Patterns**: Database and caching strategies (optional)
- **Testing Strategy**: References docs/TESTING_STRATEGY.md for comprehensive TDD methodology
- **User Stories**: References docs/USER_STORIES.md with GitHub issue integration
- **GitHub Integration**: Issue templates following the 3 C's (Card, Conversation, Confirmation)
- **Deployment**: Container and production patterns (optional)

## Vertical Slice Architecture

This template follows **Vertical Slice Architecture** where:
- Features are organized by business capability (not technical layer)
- Each slice contains: models, service, router/commands, optional repository/schemas
- Benefits: high cohesion, low coupling, easy testing, clear boundaries
- Trade-off: Some duplication is acceptable across slices

### Key Principles

1. **Feature Co-location**: All code for a feature lives together in its slice
2. **Minimal Shared Code**: Prefer duplication over premature abstraction
3. **Service-First**: Business logic lives in framework-agnostic service classes
4. **Thin Interfaces**: CLI commands and API routers are thin layers that delegate to services

## Configuration Pattern

The template uses **TOML + Pydantic Settings** with:
- Primary config in `config.toml` (TOML format)
- Environment overrides via `.env` file
- Type-safe settings with Pydantic models
- Nested settings for feature-specific configuration
- Priority: Init params > Env vars > .env file > config.toml > defaults

## Template Maintenance

### Adding New Sections

When adding content:
- Follow existing markdown formatting
- Use code blocks with language tags
- Include "Purpose" and "Guidelines" where applicable
- Mark truly optional patterns with `[OPTIONAL]` prefix
- Add inline comments in code examples to explain key concepts

### Updating Technology Versions

- Keep "latest" for dependencies that should track current versions
- Specify version constraints only when critical (e.g., Python 3.12+)
- Update "Last Updated" date when making changes
- Increment "Template Version" for breaking changes

### Testing Strategy

The `docs/TESTING_STRATEGY.md` template provides comprehensive TDD methodology:
- **Red-Green-Refactor**: TDD cycle for each feature
- **Test Organization**: Unit, integration, and E2E test structure
- **Coverage Targets**: 90% overall, 100% business logic, 95% API endpoints
- **Pytest Configuration**: Markers, fixtures, and test execution
- **Saved Responses**: Optional pattern for external API testing with ResponseLoader utility
- **Requirements Mapping**: Traceability between requirements and tests

When implementing features in projects using this template:
1. Write tests first (Red phase)
2. Implement minimal code to pass (Green phase)
3. Refactor for quality (Refactor phase)
4. Maintain test organization matching vertical slice structure
5. Update REQUIREMENTS_TEST_MAPPING.md for traceability

## Code Example Standards

All code examples should:
- Be complete and runnable (not pseudocode)
- Include proper imports
- Follow PEP 8 style guidelines
- Use type hints where applicable
- Include docstrings for classes and public methods
- Show realistic parameter names and values

## Emoticon Usage Policy

**Approved Emoticons Only:** Only the following emoticons may be used in templates:
- ✅ Green checkmark - For completed items and success states
- ❌ Red X - For errors and failures
- ⚠️ Warning - For warnings and cautions
- 🔄 Refresh/Reload - For in-progress items

**Prohibited:** All other emoticons are prohibited to maintain professional tone. This includes but is not limited to: 🎯 🐛 🔧 📋 🔍 💡 💬 📖 🏭 🧪 🤖 📅 and any other emoji not explicitly listed above.

When updating templates or creating new content, do not add emoticons beyond the approved list.
