# Testing Strategy

**Project:** {PROJECT_NAME}
**Version:** 1.0
**Last Updated:** {DATE}

---

## Overview

This document outlines the Test-Driven Development (TDD) methodology for {PROJECT_NAME}. TDD is critical for systems where bugs can lead to data inconsistencies, incorrect business logic execution, or integration failures.

---

## TDD Principles

### The Red-Green-Refactor Cycle

**1. RED Phase: Write a Failing Test**
- Write a test that describes the desired behavior
- The test must fail initially (no implementation exists yet)
- Focus on the interface and expected behavior
- Keep the test simple and focused on one behavior

**2. GREEN Phase: Make the Test Pass**
- Write the minimum code necessary to make the test pass
- Don't worry about perfect design in this phase
- Focus on functionality, not optimization
- Implement only what's needed to pass the current test

**3. REFACTOR Phase: Improve the Code**
- Refactor both production and test code
- Improve design, remove duplication, enhance readability
- Ensure all tests still pass after refactoring
- Consider performance optimizations

### TDD Benefits

**Design Quality:**
- Forces thinking about interfaces before implementation
- Results in more modular, testable code
- Prevents over-engineering and unnecessary complexity

**Data Integrity:**
- Every critical data path is tested
- Prevents regression bugs
- Tests serve as living specifications

**Documentation:**
- Tests document expected behavior
- Examples of how to use the system
- Living documentation that stays up-to-date

**Confidence:**
- Safe refactoring with comprehensive test coverage
- Quick feedback on breaking changes
- Reliable deployment with automated testing

---

## TDD Implementation Strategy

### For Each User Story

Follow this sequence for implementing each user story:

**1. Start with Acceptance Test (E2E)**
```python
def test_user_story_complete_workflow():
    """
    E2E test for US-XXX-001: Feature Name
    This test guides the entire feature implementation
    """
    # This test will initially fail - that's expected

    # Arrange - Create test scenario
    test_data = setup_test_scenario()

    # Act - Execute the workflow
    result = execute_feature_workflow(test_data)

    # Assert - Verify acceptance criteria
    assert_acceptance_criteria_met(result)
```

**2. Break Down into Unit Tests**
```python
def test_service_method_with_valid_input_should_succeed():
    """Unit test driving service implementation"""
    # Arrange
    service = FeatureService()
    valid_input = create_valid_input()

    # Act - will fail until service is implemented
    result = service.process(valid_input)

    # Assert
    assert result.is_valid
    assert result.data is not None
```

**3. Implement Service Layer**
```python
class FeatureService:
    """Business logic for feature."""

    def process(self, input: InputModel) -> ResultModel:
        """Process input and return result."""
        # Minimal implementation to pass test
        return ResultModel(is_valid=True, data=None)
```

**4. Add Integration Tests**
```python
def test_feature_endpoint_integration(client):
    """Integration test driving API implementation"""
    response = client.post("/api/feature", json={"input": "test"})
    assert response.status_code == 200
    assert response.json()["result"] == "success"
```

**5. Implement API Layer**
```python
@router.post("/api/feature")
def process_feature(request: FeatureRequest):
    """Feature API endpoint."""
    # Implementation to pass integration test
    service = FeatureService()
    result = service.process(request.input)
    return {"result": "success", "data": result.data}
```

**6. Verify and Refactor**
- Ensure E2E test passes
- Refactor for production quality
- Add error handling and edge cases

---

## Test Organization Structure

### Vertical Slice Test Organization

Tests are organized within feature slices to maintain cohesion:

```
tests/
├── conftest.py                      # Shared fixtures and configuration
├── unit/                            # Unit tests (service layer)
│   ├── test_feature1_service.py
│   └── test_feature2_service.py
├── integration/                     # Integration tests (API layer)
│   ├── test_feature1_router.py
│   ├── test_feature2_router.py
│   └── test_api_endpoints.py
├── e2e/                            # End-to-end tests (user workflows)
│   ├── test_feature1_workflow.py
│   └── test_feature2_workflow.py
├── fixtures/                        # Test data and fixtures
│   ├── sample_data.json
│   └── factory.py                   # Factory-Boy factories
└── **[OPTIONAL]** saved_responses/  # Real API responses for testing
    ├── response_loader.py           # Utility to load saved responses
    └── api_name/
        ├── endpoint1_success.json
        └── endpoint1_error.json
```

---

## Test Categories and Levels

### Unit Tests (Service Layer)

**Purpose:** Test business logic in isolation
**Speed:** < 1ms per test
**Coverage Goal:** 100% of business logic

**Characteristics:**
- Mock all external dependencies
- Test individual methods/functions
- Focus on edge cases and error conditions
- Fast execution for quick feedback

**Example:**
```python
# tests/unit/test_feature_service.py
import pytest
from unittest.mock import Mock, patch
from {package_name}.feature.service import FeatureService
from {package_name}.feature.models import FeatureModel

class TestFeatureService:
    """Unit tests for FeatureService."""

    def test_process_with_valid_input_should_return_result(self):
        """Test successful processing of valid input."""
        # Arrange
        service = FeatureService()
        input_data = FeatureModel(name="test", value=42)

        # Act
        result = service.process(input_data)

        # Assert
        assert result.success is True
        assert result.processed_value == 42

    def test_process_with_invalid_input_should_raise_error(self):
        """Test error handling for invalid input."""
        # Arrange
        service = FeatureService()
        invalid_data = FeatureModel(name="", value=-1)

        # Act & Assert
        with pytest.raises(ValidationError) as exc:
            service.process(invalid_data)

        assert "name cannot be empty" in str(exc.value)

    @patch('feature.service.external_api_call')
    def test_process_with_api_failure_should_handle_gracefully(self, mock_api):
        """Test handling of external API failures."""
        # Arrange
        service = FeatureService()
        mock_api.side_effect = ConnectionError("API unavailable")

        # Act & Assert
        with pytest.raises(ServiceError) as exc:
            service.process(FeatureModel(name="test", value=42))

        assert "External service unavailable" in str(exc.value)
```

### Integration Tests (API Layer)

**Purpose:** Test component interactions and API endpoints
**Speed:** < 100ms per test
**Coverage Goal:** 95% of API endpoints

**Characteristics:**
- Use real database (SQLite in-memory for speed)
- Test HTTP requests/responses
- Validate serialization/deserialization
- Test authentication and authorization

**Example:**
```python
# tests/integration/test_feature_router.py
import pytest
from fastapi.testclient import TestClient
from {package_name}.main import app
from {package_name}.shared.core.database import get_db

@pytest.fixture
def client(test_db_session):
    """Test client with database override."""
    def override_get_db():
        yield test_db_session

    app.dependency_overrides[get_db] = override_get_db
    with TestClient(app) as client:
        yield client
    app.dependency_overrides.clear()

class TestFeatureRouter:
    """Integration tests for Feature API endpoints."""

    def test_list_features_should_return_all_items(self, client, sample_features):
        """Test GET /api/features returns all features."""
        # Arrange - sample_features fixture populates database

        # Act
        response = client.get("/api/features")

        # Assert
        assert response.status_code == 200
        data = response.json()
        assert len(data["items"]) == 3
        assert data["items"][0]["name"] == "Feature 1"

    def test_create_feature_with_valid_data_should_succeed(self, client):
        """Test POST /api/features creates new feature."""
        # Arrange
        payload = {"name": "New Feature", "value": 42}

        # Act
        response = client.post("/api/features", json=payload)

        # Assert
        assert response.status_code == 201
        data = response.json()
        assert data["name"] == "New Feature"
        assert data["id"] is not None

    def test_create_feature_with_invalid_data_should_return_400(self, client):
        """Test POST /api/features with invalid data returns error."""
        # Arrange
        payload = {"name": "", "value": -1}

        # Act
        response = client.post("/api/features", json=payload)

        # Assert
        assert response.status_code == 400
        assert "validation" in response.json()["detail"].lower()
```

### End-to-End Tests (User Workflows)

**Purpose:** Test complete user scenarios
**Speed:** < 1s per test
**Coverage Goal:** 100% of critical user paths

**Characteristics:**
- Test realistic user workflows
- Multiple API calls in sequence
- Validate user story acceptance criteria
- Use production-like data

**Example:**
```python
# tests/e2e/test_feature_workflow.py
import pytest
from fastapi.testclient import TestClient

class TestFeatureWorkflow:
    """E2E tests for complete feature workflows."""

    def test_complete_feature_workflow_should_succeed(self, client, test_user):
        """Test complete workflow from creation to deletion."""
        # Arrange
        feature_data = {"name": "Test Feature", "value": 100}

        # Act & Assert - Step 1: Create feature
        create_response = client.post("/api/features", json=feature_data)
        assert create_response.status_code == 201
        feature_id = create_response.json()["id"]

        # Act & Assert - Step 2: Retrieve feature
        get_response = client.get(f"/api/features/{feature_id}")
        assert get_response.status_code == 200
        assert get_response.json()["name"] == "Test Feature"

        # Act & Assert - Step 3: Update feature
        update_data = {"name": "Updated Feature", "value": 200}
        update_response = client.put(
            f"/api/features/{feature_id}",
            json=update_data
        )
        assert update_response.status_code == 200
        assert update_response.json()["value"] == 200

        # Act & Assert - Step 4: Delete feature
        delete_response = client.delete(f"/api/features/{feature_id}")
        assert delete_response.status_code == 204

        # Act & Assert - Step 5: Verify deletion
        verify_response = client.get(f"/api/features/{feature_id}")
        assert verify_response.status_code == 404
```

---

## Test Data Management

### Pytest Fixtures

Use fixtures for consistent, reusable test data:

```python
# tests/conftest.py
import pytest
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from {package_name}.shared.core.database import Base
from {package_name}.feature.models import FeatureModel

@pytest.fixture(scope="function")
def test_db_session():
    """Clean database session for each test."""
    # Create in-memory database
    engine = create_engine("sqlite:///:memory:")
    Base.metadata.create_all(engine)

    # Create session
    SessionLocal = sessionmaker(bind=engine)
    session = SessionLocal()

    yield session

    # Cleanup
    session.close()
    Base.metadata.drop_all(engine)

@pytest.fixture
def sample_features(test_db_session):
    """Create sample features for testing."""
    features = [
        FeatureModel(name="Feature 1", value=10),
        FeatureModel(name="Feature 2", value=20),
        FeatureModel(name="Feature 3", value=30),
    ]
    test_db_session.add_all(features)
    test_db_session.commit()
    return features

@pytest.fixture
def test_user(test_db_session):
    """Create test user."""
    user = UserModel(
        email="test@example.com",
        name="Test User",
        is_active=True
    )
    test_db_session.add(user)
    test_db_session.commit()
    return user
```

### **[OPTIONAL]** Factory-Boy Integration

For complex data models, use Factory-Boy:

```python
# tests/fixtures/factory.py
import factory
from factory.alchemy import SQLAlchemyModelFactory
from {package_name}.feature.models import FeatureModel, UserModel

class FeatureFactory(SQLAlchemyModelFactory):
    """Factory for creating test features."""

    class Meta:
        model = FeatureModel
        sqlalchemy_session = None  # Set in conftest.py
        sqlalchemy_session_persistence = "commit"

    name = factory.Sequence(lambda n: f"Feature {n}")
    value = factory.Faker('random_int', min=1, max=100)
    created_at = factory.Faker('date_time_this_month')

class UserFactory(SQLAlchemyModelFactory):
    """Factory for creating test users."""

    class Meta:
        model = UserModel
        sqlalchemy_session = None

    email = factory.Faker('email')
    name = factory.Faker('name')
    is_active = True

# tests/conftest.py
from tests.fixtures.factory import FeatureFactory, UserFactory

@pytest.fixture(scope="function")
def test_db_session():
    """Database session with factory support."""
    # ... (engine and session creation)

    # Configure factories
    FeatureFactory._meta.sqlalchemy_session = session
    UserFactory._meta.sqlalchemy_session = session

    yield session

    # Cleanup
    session.close()
```

### Test Isolation

Each test runs in a clean environment:

```python
@pytest.fixture(scope="function", autouse=True)
def isolate_tests(test_db_session):
    """Ensure test isolation by rolling back after each test."""
    yield
    test_db_session.rollback()
```

---

## **[OPTIONAL]** Saved Responses Pattern

For projects that integrate with external APIs, use saved responses for deterministic testing:

### Directory Structure

```
tests/saved_responses/
├── response_loader.py              # Utility to load responses
├── external_api/
│   ├── get_resources_success.json
│   ├── get_resources_error.json
│   ├── create_item_success.json
│   └── create_item_validation_error.json
└── another_api/
    └── fetch_data_success.json
```

### Response Loader Utility

```python
# tests/saved_responses/response_loader.py
import json
from pathlib import Path
from typing import Dict, Any

class ResponseLoader:
    """Utility for loading saved API responses."""

    def __init__(self, base_dir: str = "tests/saved_responses"):
        self.base_dir = Path(base_dir)

    def load(self, api_name: str, response_file: str) -> Dict[str, Any]:
        """Load a saved response from file.

        Args:
            api_name: Name of the API directory
            response_file: Name of the response file (with .json extension)

        Returns:
            Parsed JSON response

        Raises:
            FileNotFoundError: If response file doesn't exist
        """
        file_path = self.base_dir / api_name / response_file

        if not file_path.exists():
            raise FileNotFoundError(
                f"Response file not found: {file_path}\n"
                f"Available files: {list((self.base_dir / api_name).glob('*.json'))}"
            )

        with open(file_path, 'r') as f:
            return json.load(f)

    def load_text(self, api_name: str, response_file: str) -> str:
        """Load a saved response as text."""
        file_path = self.base_dir / api_name / response_file

        with open(file_path, 'r') as f:
            return f.read()
```

### Using Saved Responses in Tests

```python
# tests/conftest.py
import pytest
from tests.saved_responses.response_loader import ResponseLoader

@pytest.fixture
def response_loader():
    """Fixture providing response loader."""
    return ResponseLoader()

# tests/integration/test_external_api_client.py
from unittest.mock import Mock, patch

class TestExternalAPIClient:
    """Tests using saved responses."""

    @patch('external_api.client.requests.get')
    def test_get_resources_with_valid_response(self, mock_get, response_loader):
        """Test client with real API response."""
        # Arrange
        saved_response = response_loader.load(
            "external_api",
            "get_resources_success.json"
        )
        mock_get.return_value = Mock(
            status_code=200,
            json=lambda: saved_response
        )
        client = ExternalAPIClient()

        # Act
        resources = client.get_resources()

        # Assert
        assert len(resources) == 5
        assert resources[0]["name"] == "Resource 1"

    @patch('external_api.client.requests.post')
    def test_create_item_with_validation_error(self, mock_post, response_loader):
        """Test client handles validation errors correctly."""
        # Arrange
        error_response = response_loader.load(
            "external_api",
            "create_item_validation_error.json"
        )
        mock_post.return_value = Mock(
            status_code=400,
            json=lambda: error_response
        )
        client = ExternalAPIClient()

        # Act & Assert
        with pytest.raises(ValidationError) as exc:
            client.create_item({"invalid": "data"})

        assert "validation" in str(exc.value).lower()
```

### Capturing Real API Responses

```python
# scripts/capture_api_responses.py
"""
Script to capture real API responses for testing.
Run this script to update saved responses when API changes.
"""
import json
from pathlib import Path
from external_api.client import ExternalAPIClient

def capture_responses():
    """Capture real API responses and save to files."""
    client = ExternalAPIClient()
    output_dir = Path("tests/saved_responses/external_api")
    output_dir.mkdir(parents=True, exist_ok=True)

    # Capture successful response
    try:
        resources = client.get_resources()
        with open(output_dir / "get_resources_success.json", 'w') as f:
            json.dump(resources, f, indent=2)
        print("✓ Captured get_resources_success.json")
    except Exception as e:
        print(f"✗ Failed to capture success response: {e}")

    # Capture error response (if possible)
    try:
        client.create_item({"invalid": "data"})
    except Exception as e:
        # Save the error response
        error_data = e.response.json() if hasattr(e, 'response') else str(e)
        with open(output_dir / "create_item_validation_error.json", 'w') as f:
            json.dump(error_data, f, indent=2)
        print("✓ Captured create_item_validation_error.json")

if __name__ == "__main__":
    capture_responses()
```

---

## Coverage Requirements

### Coverage Targets

**Unit Tests (Service Layer):**
- 100% line coverage of business logic
- 100% branch coverage for conditional logic
- All error paths tested

**Integration Tests (API Layer):**
- 95% coverage of API endpoints
- All HTTP status codes tested
- Authentication and authorization paths validated

**End-to-End Tests:**
- 100% coverage of critical user workflows
- All user story acceptance criteria validated
- Cross-feature integration tested

**Overall Target:** 90% minimum code coverage

### Coverage Enforcement

```bash
# Run tests with coverage
pytest --cov=src/{package_name} --cov-report=html --cov-report=term --cov-fail-under=90

# Coverage by test type
pytest tests/unit/ --cov=src/{package_name} --cov-report=term-missing
pytest tests/integration/ --cov=src/{package_name} --cov-report=term-missing
pytest tests/e2e/ --cov=src/{package_name} --cov-report=term-missing

# Generate detailed HTML report
pytest --cov=src/{package_name} --cov-report=html
open htmlcov/index.html
```

### Pytest Configuration

```toml
# pyproject.toml
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]
addopts = [
    "-v",
    "--strict-markers",
    "--cov=src/{package_name}",
    "--cov-report=term-missing",
    "--cov-report=html",
    "--cov-fail-under=90",
]
markers = [
    "unit: Unit tests (service layer)",
    "integration: Integration tests (API layer)",
    "e2e: End-to-end tests (user workflows)",
    "slow: Tests that take > 1 second",
    "uses_real_data: Tests that use saved real API responses",
]

[tool.coverage.run]
source = ["src/{package_name}"]
omit = [
    "*/tests/*",
    "*/__pycache__/*",
    "*/.venv/*",
]

[tool.coverage.report]
exclude_lines = [
    "pragma: no cover",
    "def __repr__",
    "raise AssertionError",
    "raise NotImplementedError",
    "if __name__ == .__main__.:",
    "if TYPE_CHECKING:",
    "@abstractmethod",
]
```

---

## Test Execution

### Running Tests

```bash
# Run all tests
pytest

# Run specific test category
pytest -m unit
pytest -m integration
pytest -m e2e

# Run specific test file
pytest tests/unit/test_feature_service.py

# Run specific test function
pytest tests/unit/test_feature_service.py::TestFeatureService::test_process_with_valid_input

# Run tests matching pattern
pytest -k "test_feature"

# Run tests with verbose output
pytest -v

# Run tests with coverage
pytest --cov=src/{package_name}

# Run tests in parallel (requires pytest-xdist)
pytest -n auto

# Run tests that use real data
pytest -m uses_real_data
```

### Test Markers

```python
# Mark test categories
@pytest.mark.unit
def test_service_method():
    """Unit test."""
    pass

@pytest.mark.integration
def test_api_endpoint():
    """Integration test."""
    pass

@pytest.mark.e2e
def test_complete_workflow():
    """End-to-end test."""
    pass

# Mark slow tests
@pytest.mark.slow
def test_long_running_operation():
    """Test that takes > 1 second."""
    pass

# Mark tests using real data
@pytest.mark.uses_real_data
def test_with_saved_api_response():
    """Test using saved real API response."""
    pass

# Skip tests conditionally
@pytest.mark.skipif(
    condition=not os.getenv("RUN_INTEGRATION_TESTS"),
    reason="Integration tests disabled"
)
def test_external_api():
    """Test requiring external service."""
    pass
```

---

## Quality Gates

Before any feature is considered complete:

**Test Quality:**
- [ ] All tests pass (no skipped or failing tests)
- [ ] Coverage thresholds met for all test categories (90% overall)
- [ ] Tests run within performance requirements (unit < 1ms, integration < 100ms, e2e < 1s)
- [ ] No production code exists without corresponding tests

**Code Quality:**
- [ ] Tests are readable and maintainable
- [ ] Mocks are used appropriately in unit tests
- [ ] Integration tests use real database connections
- [ ] E2E tests validate complete user workflows

**Requirements Traceability:**
- [ ] All acceptance criteria have corresponding tests
- [ ] Test names clearly indicate what they validate
- [ ] REQUIREMENTS_TEST_MAPPING.md updated (see below)

---

## TDD Best Practices

### Test Naming Conventions

```python
def test_[method_name]_with_[scenario]_should_[expected_behavior]():
    """
    Pattern: test_what_when_then

    Examples:
    - test_authenticate_with_invalid_password_should_raise_error
    - test_create_user_with_duplicate_email_should_return_conflict
    - test_calculate_total_with_empty_cart_should_return_zero
    """
    pass
```

### Arrange-Act-Assert Pattern

```python
def test_create_user_with_valid_data_should_succeed():
    """Clear test structure using AAA pattern."""
    # Arrange - Set up test data and dependencies
    user_data = {"email": "test@example.com", "name": "Test User"}
    user_service = UserService()

    # Act - Execute the code under test
    created_user = user_service.create_user(user_data)

    # Assert - Verify the results
    assert created_user.email == user_data["email"]
    assert created_user.name == user_data["name"]
    assert created_user.is_active is True
```

### Mock Strategy

**Unit Tests:** Mock all external dependencies
```python
from unittest.mock import Mock, patch

@pytest.fixture
def mock_dependencies():
    """Mock external dependencies for unit tests."""
    return {
        'repository': Mock(spec=UserRepository),
        'email_service': Mock(spec=EmailService),
        'audit_service': Mock(spec=AuditService)
    }

def test_create_user_with_mocked_dependencies(mock_dependencies):
    """Unit test with mocked dependencies."""
    # Arrange
    mock_repo = mock_dependencies['repository']
    mock_repo.save.return_value = User(id=1, name="Test")
    service = UserService(repository=mock_repo)

    # Act
    result = service.create_user({"name": "Test"})

    # Assert
    assert result.id == 1
    mock_repo.save.assert_called_once()
```

**Integration Tests:** Use real database, mock external services
```python
@patch('external_api.client.ExternalAPIClient')
def test_feature_endpoint_with_real_db(mock_api, client, test_db_session):
    """Integration test with real DB, mocked external API."""
    # Arrange
    mock_api.return_value.fetch_data.return_value = {"status": "success"}

    # Act
    response = client.post("/api/feature", json={"input": "test"})

    # Assert
    assert response.status_code == 200
    # Verify data persisted in real database
    assert test_db_session.query(Feature).count() == 1
```

**E2E Tests:** Use real implementations where possible
```python
def test_complete_workflow_end_to_end(client):
    """E2E test with real implementations."""
    # Use real database, real services, real endpoints
    # Only mock truly external systems (payment gateways, third-party APIs)
    pass
```

### Test Independence

Tests should not depend on each other:

```python
# BAD - Tests depend on execution order
def test_create_user():
    user = create_user({"name": "Test"})
    global user_id
    user_id = user.id

def test_update_user():
    # Depends on test_create_user
    update_user(user_id, {"name": "Updated"})

# GOOD - Each test is independent
def test_create_user(test_db_session):
    user = create_user({"name": "Test"})
    assert user.id is not None

def test_update_user(test_db_session, sample_user):
    updated = update_user(sample_user.id, {"name": "Updated"})
    assert updated.name == "Updated"
```

---

## Requirements Test Mapping

Maintain traceability between requirements and tests in `tests/REQUIREMENTS_TEST_MAPPING.md`:

```markdown
# Requirements to Test Coverage Mapping

**Project:** {PROJECT_NAME}
**Last Updated:** {DATE}

## Requirements Coverage Summary

| Requirement Category | Total Reqs | Tested | Coverage | Status |
|---------------------|-------------|--------|----------|--------|
| Configuration Management | 3 | 3 | 100% | ✅ GOOD |
| Data Models & Validation | 5 | 5 | 100% | ✅ GOOD |
| API Endpoints | 10 | 9 | 90% | ⚠️ PARTIAL |
| Business Logic | 15 | 15 | 100% | ✅ GOOD |

## Detailed Mapping

### REQ-001: User Authentication

**Test Coverage:** 100%
**Test Functions:**
- `test_authenticate_with_valid_credentials_should_succeed`
- `test_authenticate_with_invalid_password_should_raise_error`
- `test_authenticate_with_nonexistent_user_should_raise_error`
- `test_authentication_logs_all_attempts`

**Requirements Met:**
- ✅ Users can authenticate with email and password
- ✅ Invalid credentials return appropriate error
- ✅ Authentication attempts are logged
- ✅ Session tokens are generated on success

### REQ-002: Data Validation

**Test Coverage:** 100%
**Test Functions:**
- `test_validate_email_format_should_accept_valid_emails`
- `test_validate_email_format_should_reject_invalid_emails`
- `test_required_fields_validation_should_raise_error_when_missing`

**Requirements Met:**
- ✅ Email format validation
- ✅ Required fields validation
- ✅ Custom validation rules applied
```

---

## Continuous Integration

### GitHub Actions Example

```yaml
# .github/workflows/test.yml
name: Tests

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.12"]

    steps:
    - uses: actions/checkout@v4

    - name: Set up Python ${{ matrix.python-version }}
      uses: actions/setup-python@v5
      with:
        python-version: ${{ matrix.python-version }}

    - name: Install uv
      run: pip install uv

    - name: Install dependencies
      run: uv sync

    - name: Run tests with coverage
      run: |
        uv run pytest --cov=src/{package_name} --cov-report=xml --cov-report=term

    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v4
      with:
        file: ./coverage.xml
        fail_ci_if_error: true

    - name: Check coverage threshold
      run: |
        uv run pytest --cov=src/{package_name} --cov-fail-under=90
```

---

## Sprint Testing Process

For sprint-specific test execution tracking, generate separate documents:
- `tests/SPRINT_N_TEST_EXECUTION_SUMMARY.md` - Test execution results per sprint
- `tests/SPRINT_N_REQUIREMENTS_TEST_MAPPING.md` - Requirements coverage per sprint

These documents track:
- User story acceptance criteria validation
- Test pass rates and coverage metrics
- Quality assessment scores
- Gaps and recommendations for next sprint

---

## Conclusion

This testing strategy ensures high-quality, maintainable code through disciplined TDD practices. By writing tests first and maintaining comprehensive coverage, we create reliable software with living documentation and confidence in every change.

**Key Principles:**
1. Write tests before implementation (Red-Green-Refactor)
2. Maintain 90% overall code coverage minimum
3. Test at all levels (unit, integration, e2e)
4. Keep tests independent and isolated
5. Use fixtures for reusable test data
6. Document requirements-to-test mappings
7. Run tests in CI/CD on every commit

---

**Template Version:** 1.0.0
**Last Updated:** {DATE}
