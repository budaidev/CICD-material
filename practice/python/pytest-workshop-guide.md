# Python Testing Workshop Guide with pytest

## Overview
This workshop covers Python testing fundamentals and advanced concepts using pytest. You'll learn how to write tests, use fixtures, parametrize tests, and work with mocks.

Estimated time: 110 minutes

---

## Workshop Structure

### Part 1: pytest Basics (20 minutes)
### Part 2: Basic Testing Practice (30 minutes)
### Part 3: Advanced pytest Features (60 minutes)

---

## Prerequisites
- Python 3.8+
- PyCharm Community Edition
- Basic Python knowledge

---

## Part 1: pytest Basics (20 minutes)

### 1.1 Setting Up pytest

Create a new Python project in PyCharm and install pytest:

```cmd
# In PyCharm terminal (Alt+F12)
pip install pytest pytest-mock
```

Verify installation:
```cmd
pytest --version
```

### 1.2 Test Discovery Rules

pytest follows these conventions:
- Test files should be named `test_*.py` or `*_test.py`
- Test functions should be named `test_*`
- Test classes should be named `Test*`
- Test folders don't require `__init__.py` files

### 1.3 Your First Test

Create `test_basic.py`:
```python
def test_simple_math():
    assert 1 + 1 == 2

def test_string():
    assert "hello".upper() == "HELLO"
```

Run tests in PyCharm:
- Right-click the test file > Run 'pytest in test_basic.py'
- Or use the green play button next to each test

---

## Part 2: Basic Testing Practice (30 minutes)

### 2.1 Project Structure

Create the following structure in PyCharm:
```
project_root/
  ├── src/
  │   └── calculator.py
  └── tests/
      └── test_calculator.py
```

### 2.2 Implementation

In `src/calculator.py`:
```python
class Calculator:
    def add(self, a, b):
        return a + b
    
    def divide(self, a, b):
        if b == 0:
            raise ValueError("Division by zero!")
        return a / b
```

In `tests/test_calculator.py`:
```python
import pytest
from src.calculator import Calculator

def test_add():
    calc = Calculator()
    assert calc.add(2, 3) == 5
    assert calc.add(-1, 1) == 0
    assert calc.add(0, 0) == 0

def test_divide():
    calc = Calculator()
    assert calc.divide(6, 2) == 3.0
    assert calc.divide(5, 2) == 2.5
    
def test_divide_by_zero():
    calc = Calculator()
    with pytest.raises(ValueError, match="Division by zero!"):
        calc.divide(1, 0)
```

### 2.3 Running Tests

1. Run single test:
   ```cmd
   pytest tests/test_calculator.py -v
   ```

2. Run specific test function:
   ```cmd
   pytest tests/test_calculator.py::test_add -v
   ```

3. Run tests with print output:
   ```cmd
   pytest -v -s
   ```

---

## Part 3: Advanced pytest Features (60 minutes)

### 3.1 Parametrized Tests (15 minutes)

Modify `test_calculator.py` to use parametrization:

```python
import pytest
from src.calculator import Calculator

@pytest.mark.parametrize("a, b, expected", [
    (2, 3, 5),
    (-1, 1, 0),
    (0, 0, 0),
    (10, -5, 5),
    (100, 200, 300)
])
def test_add_parametrized(a, b, expected):
    calc = Calculator()
    assert calc.add(a, b) == expected

@pytest.mark.parametrize("a, b, expected", [
    (6, 2, 3.0),
    (5, 2, 2.5),
    (10, 5, 2.0),
    (-6, 2, -3.0),
    (0, 1, 0.0)
])
def test_divide_parametrized(a, b, expected):
    calc = Calculator()
    assert calc.divide(a, b) == expected
```

### 3.2 Fixtures (20 minutes)

Enhance tests with fixtures:

```python
import pytest
from src.calculator import Calculator

@pytest.fixture
def calculator():
    """Provides a Calculator instance."""
    return Calculator()

@pytest.fixture
def calculated_value():
    """Provides a pre-calculated value."""
    return 42

def test_add_with_fixture(calculator):
    assert calculator.add(2, 3) == 5

def test_divide_with_fixture(calculator):
    assert calculator.divide(6, 2) == 3.0

# Fixture with setup and teardown
@pytest.fixture
def complex_calculator():
    """Provides a Calculator instance with setup and teardown."""
    # Setup
    calc = Calculator()
    print("\nSetting up calculator")
    
    # Provide the fixture value
    yield calc
    
    # Teardown
    print("\nCleaning up calculator")

def test_with_complex_fixture(complex_calculator):
    assert complex_calculator.add(2, 3) == 5

# Sharing fixtures across tests
@pytest.fixture
def shared_result(calculator):
    return calculator.add(10, 10)

def test_using_shared_result(shared_result):
    assert shared_result == 20
```

### 3.3 Mocking (15 minutes)

Create a new class that uses external services:

```python
# src/enhanced_calculator.py
class EnhancedCalculator:
    def __init__(self, data_service):
        self.data_service = data_service
    
    def add_with_history(self, a, b):
        result = a + b
        self.data_service.save_calculation(f"{a} + {b} = {result}")
        return result
    
    def get_last_calculation(self):
        return self.data_service.get_last_calculation()
```

Write tests with mocking:

```python
# tests/test_enhanced_calculator.py
import pytest
from src.enhanced_calculator import EnhancedCalculator

def test_add_with_history(mocker):
    # Create mock service
    mock_service = mocker.Mock()
    
    # Configure mock
    mock_service.get_last_calculation.return_value = "2 + 3 = 5"
    
    # Create calculator with mock
    calc = EnhancedCalculator(mock_service)
    
    # Test the add operation
    result = calc.add_with_history(2, 3)
    
    # Verify result
    assert result == 5
    
    # Verify mock was called correctly
    mock_service.save_calculation.assert_called_once_with("2 + 3 = 5")

def test_get_last_calculation(mocker):
    # Create and configure mock
    mock_service = mocker.Mock()
    mock_service.get_last_calculation.return_value = "2 + 3 = 5"
    
    # Create calculator with mock
    calc = EnhancedCalculator(mock_service)
    
    # Test getting last calculation
    result = calc.get_last_calculation()
    
    # Verify result
    assert result == "2 + 3 = 5"
    
    # Verify mock was called
    mock_service.get_last_calculation.assert_called_once()
```

### 3.4 Running Tests with Coverage

Install coverage package:
```cmd
pip install pytest-cov
```

Run tests with coverage:
```cmd
pytest --cov=src tests/
```

Generate HTML coverage report:
```cmd
pytest --cov=src tests/ --cov-report=html
```

---

## Best Practices

1. Test Organization:
   - Keep tests in a separate `tests` directory
   - Mirror your source code structure in tests
   - Use clear, descriptive test names

2. Test Structure:
   - Arrange: Set up test data and conditions
   - Act: Execute the code being tested
   - Assert: Verify the results

3. Fixtures:
   - Use fixtures for common setup
   - Keep fixtures focused and simple
   - Use scopes appropriately (function, class, module, session)

4. Parameterization:
   - Use for testing multiple inputs
   - Keep test cases focused and readable
   - Include edge cases and boundary conditions

5. Mocking:
   - Mock external dependencies
   - Verify mock interactions
   - Don't mock the system under test

---

## PyCharm Tips for Testing

1. Running Tests:
   - Green play button next to test
   - Right-click on test file/folder
   - Run configurations

2. Debugging Tests:
   - Set breakpoints
   - Debug button (bug icon)
   - Step through test execution

3. Coverage:
   - Run with Coverage (Run menu)
   - View coverage in editor
   - Export coverage reports

---

## Resources

- [pytest Documentation](https://docs.pytest.org/)
- [pytest-mock Documentation](https://pytest-mock.readthedocs.io/)
- [Coverage.py Documentation](https://coverage.readthedocs.io/)
- [PyCharm Testing Tutorial](https://www.jetbrains.com/help/pycharm/testing-your-first-python-application.html)
