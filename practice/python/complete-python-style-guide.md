# Complete Python Style and Clean Code Guide

## Overview
This comprehensive guide combines Python's official style guide (PEP 8) with clean code principles to help you write readable, maintainable, and efficient Python code.

## Table of Contents
1. [PEP 8 Style Guide](#1-pep-8-style-guide)
2. [Clean Code Principles](#2-clean-code-principles)
3. [Code Documentation](#3-code-documentation)
4. [Code Organization](#4-code-organization)
5. [Error Handling](#5-error-handling)
6. [Data Structures and Algorithms](#6-data-structures-and-algorithms)
7. [Type Hints](#7-type-hints)

## 1. PEP 8 Style Guide

### Indentation and Line Breaks
```python
# Indentation: 4 spaces (no tabs)
def my_function():
    do_something()
    for i in range(10):
        print(i)
        if i % 2 == 0:
            handle_even()

# Maximum line length: 79 characters
long_string = (
    "This is a very long string that needs to be "
    "wrapped across multiple lines."
)

# Line breaks before binary operators
total = (
    variable_one
    + variable_two
    + variable_three
)
```

### Imports Organization
```python
# 1. Standard library imports
import os
import sys

# 2. Third-party imports
import numpy as np
import pandas as pd

# 3. Local application imports
from mypackage import my_module
```

### Whitespace Rules
```python
# Good ✅
spam(ham[1], {eggs: 2})
foo = (0,)
if x == 4: print(x, y)

# Bad ❌
spam( ham[ 1 ], { eggs: 2 } )
foo = (0, )
if x == 4 : print(x , y)
```

## 2. Clean Code Principles

### Naming Conventions

#### Variables and Functions
```python
# Bad ❌
def fn(x):
    r = 0
    for i in x:
        r += i
    return r

# Good ✅
def calculate_sum(numbers):
    total = 0
    for number in numbers:
        total += number
    return total
```

#### Classes and Methods
```python
# Bad ❌
class Calc:
    def proc(self, x):
        pass

# Good ✅
class Calculator:
    def process_data(self, data):
        """Process the input data and return results."""
        pass
```

### Consistent Naming Patterns
```python
# Constants
MAX_CONNECTIONS = 100
DEFAULT_TIMEOUT = 30

# Classes
class DataProcessor:
    pass

# Functions and variables
def calculate_average(numbers):
    final_result = sum(numbers) / len(numbers)
    return final_result

# Private methods/variables (use single underscore)
class MyClass:
    def __init__(self):
        self._private_var = 10
    
    def _private_method(self):
        pass
```

## 3. Code Documentation

### Docstrings
```python
def calculate_discount(price: float, discount_rate: float) -> float:
    """
    Calculates the discounted price of an item.

    Args:
        price (float): The original price of the item
        discount_rate (float): The discount rate (0.0 to 1.0)

    Returns:
        float: The final price after discount

    Raises:
        ValueError: If discount_rate is not between 0 and 1
    """
    if not 0 <= discount_rate <= 1:
        raise ValueError("Discount rate must be between 0 and 1")
    return price * (1 - discount_rate)
```

### Comments
```python
# Bad ❌ - Obvious comment
# Add 1 to x
x += 1

# Good ✅ - Explains why, not what
# Compensate for zero-based index
x += 1
```

## 4. Code Organization

### DRY (Don't Repeat Yourself)
```python
# Bad ❌
def validate_user_input(text):
    if not text.strip():
        raise ValueError("Input cannot be empty")
    if len(text) > 100:
        raise ValueError("Input too long")

def validate_comment(text):
    if not text.strip():
        raise ValueError("Input cannot be empty")
    if len(text) > 100:
        raise ValueError("Input too long")

# Good ✅
def validate_text(text, context="input"):
    """Validate text input against common rules."""
    if not text.strip():
        raise ValueError(f"{context} cannot be empty")
    if len(text) > 100:
        raise ValueError(f"{context} too long")

def validate_user_input(text):
    validate_text(text, "User input")

def validate_comment(text):
    validate_text(text, "Comment")
```

### Single Responsibility Principle
```python
# Bad ❌
class UserManager:
    def process_user(self, user_data):
        # Validates data
        if not self._validate_data(user_data):
            raise ValueError("Invalid data")
        
        # Saves to database
        self._save_to_db(user_data)
        
        # Sends email
        self._send_email(user_data)

# Good ✅
class UserValidator:
    def validate(self, user_data):
        return self._check_required_fields(user_data)

class UserRepository:
    def save(self, user_data):
        # Database operations
        pass

class UserNotifier:
    def send_welcome_email(self, user_data):
        # Email operations
        pass

class UserManager:
    def __init__(self, validator, repository, notifier):
        self.validator = validator
        self.repository = repository
        self.notifier = notifier

    def process_user(self, user_data):
        self.validator.validate(user_data)
        self.repository.save(user_data)
        self.notifier.send_welcome_email(user_data)
```

## 5. Error Handling

### Proper Exception Handling
```python
# Bad ❌
try:
    do_something()
except:
    print("Error occurred")

# Good ✅
try:
    do_something()
except ValueError as e:
    logger.error(f"Invalid value provided: {e}")
except FileNotFoundError as e:
    logger.error(f"Required file not found: {e}")
except Exception as e:
    logger.error(f"Unexpected error: {e}")
    raise  # Re-raise unexpected exceptions
```

### Custom Exceptions
```python
class DataProcessingError(Exception):
    """Raised when data processing fails."""
    pass

class ValidationError(Exception):
    """Raised when data validation fails."""
    pass

def process_data(data):
    try:
        validate_data(data)
        return transform_data(data)
    except ValidationError as e:
        raise DataProcessingError(f"Processing failed: {e}")
```

## 6. Data Structures and Algorithms

### Choose Appropriate Data Structures
```python
# Bad ❌ - Using list for lookups
def find_user(users, user_id):
    for user in users:
        if user.id == user_id:
            return user
    return None

# Good ✅ - Using dictionary for O(1) lookups
def find_user(users_dict, user_id):
    return users_dict.get(user_id)
```

### Efficient Algorithms
```python
# Bad ❌ - O(n²) complexity
def find_duplicate(numbers):
    for i in range(len(numbers)):
        for j in range(i + 1, len(numbers)):
            if numbers[i] == numbers[j]:
                return True
    return False

# Good ✅ - O(n) complexity
def find_duplicate(numbers):
    seen = set()
    for num in numbers:
        if num in seen:
            return True
        seen.add(num)
    return False
```

## 7. Type Hints

### Basic Type Hints
```python
from typing import List, Dict, Optional, Union

def process_data(items: List[str]) -> Dict[str, int]:
    """Process string items and return their lengths."""
    return {item: len(item) for item in items}

def get_user(user_id: int) -> Optional[Dict[str, Any]]:
    """Retrieve user data, returns None if user not found."""
    pass

# Variable type hints
name: str = "John"
age: int = 30
scores: List[int] = [95, 89, 92]
user_info: Dict[str, Union[str, int]] = {
    "name": "Alice",
    "age": 25
}
```

### Complex Type Hints
```python
from typing import Callable, TypeVar, Generic

T = TypeVar('T')

class DataProcessor(Generic[T]):
    def process(self, data: T, operation: Callable[[T], T]) -> T:
        return operation(data)

# Using with specific types
processor: DataProcessor[str] = DataProcessor()
```

## Best Practices Summary

1. **Follow PEP 8**
   - Consistent indentation (4 spaces)
   - Maximum line length of 79 characters
   - Proper import organization
   - Whitespace conventions

2. **Clean Code Principles**
   - Meaningful names
   - Single responsibility
   - DRY principle
   - Clear documentation

3. **Error Handling**
   - Specific exceptions
   - Proper error messages
   - Graceful error recovery

4. **Code Organization**
   - Logical structure
   - Modular design
   - Separation of concerns

5. **Performance**
   - Appropriate data structures
   - Efficient algorithms
   - Resource management

6. **Type Safety**
   - Consistent type hints
   - Clear interfaces
   - Static type checking

## Tools for Code Quality

1. **Code Formatters**
   ```bash
   # autopep8
   pip install autopep8
   autopep8 --in-place file.py

   # black
   pip install black
   black file.py
   ```

2. **Linters**
   ```bash
   # flake8
   pip install flake8
   flake8 file.py

   # pylint
   pip install pylint
   pylint file.py
   ```

3. **Type Checkers**
   ```bash
   # mypy
   pip install mypy
   mypy file.py
   ```

## Resources
- [PEP 8 -- Style Guide for Python Code](https://www.python.org/dev/peps/pep-0008/)
- [Python Type Hints Documentation](https://docs.python.org/3/library/typing.html)
- [Clean Code in Python](https://github.com/zedr/clean-code-python)
