# MyPy Installation and Usage Workshop Guide

## Overview
This practical guide walks you through installing mypy and using it for static type checking in Python. You'll learn how to set up mypy, run it on your code, and handle common type checking scenarios.

Estimated time: 30 minutes

## Prerequisites
- Python 3.7+
- Basic understanding of Python type hints
- PyCharm Community Edition or VS Code
- Command line familiarity

## Table of Contents
1. [Installation](#1-installation)
2. [Basic Usage](#2-basic-usage)
3. [Common Scenarios](#3-common-scenarios)
4. [IDE Integration](#4-ide-integration)
5. [Troubleshooting](#5-troubleshooting)

## 1. Installation

### Step 1: Install mypy
```bash
# Using pip
pip install mypy

# Verify installation
mypy --version
```

### Step 2: Create a Test Project
Create a new directory and set up a simple Python file to test mypy:

```bash
mkdir mypy_workshop
cd mypy_workshop
```

## 2. Basic Usage

### First Example
Create a file named `example.py`:

```python
# example.py
def greet(name: str) -> str:
    return "Hello, " + name

# This will raise a type error
result = greet(123)  # Error: Argument has type "int"; expected "str"
```

Run mypy:
```bash
mypy example.py
```

Expected output:
```
example.py:5: error: Argument 1 to "greet" has incompatible type "int"; expected "str"
Found 1 error in 1 file (checked 1 source file)
```

### More Complex Example
Create `calculator.py`:

```python
class Calculator:
    def add(self, a: int, b: int) -> int:
        return a + b
    
    def divide(self, a: float, b: float) -> float:
        if b == 0:
            raise ValueError("Division by zero")
        return a / b

# These will work fine
calc = Calculator()
result1 = calc.add(5, 3)
result2 = calc.divide(10.0, 2.0)

# These will raise type errors
result3 = calc.add("5", "3")  # Error: Argument has type "str"; expected "int"
result4 = calc.divide(10, 2)  # Error: Argument has type "int"; expected "float"
```

## 3. Common Scenarios

### Optional Types
Create `optional_example.py`:

```python
from typing import Optional

def find_user(user_id: int) -> Optional[str]:
    users = {1: "John", 2: "Jane"}
    return users.get(user_id)

# These are fine
result1 = find_user(1)
result2 = find_user(3)  # Returns None

# This will raise a type error
result3 = find_user("1")  # Error: Argument has type "str"; expected "int"
```

### List and Dict Types
Create `collections_example.py`:

```python
from typing import List, Dict

def process_numbers(numbers: List[int]) -> Dict[str, int]:
    result = {
        "sum": sum(numbers),
        "length": len(numbers)
    }
    return result

# This is fine
result1 = process_numbers([1, 2, 3])

# These will raise type errors
result2 = process_numbers([1, "2", 3])  # Error: List contains str
result3 = process_numbers("123")  # Error: Argument has type "str"
```

### Union Types
Create `union_example.py`:

```python
from typing import Union

def process_id(user_id: Union[int, str]) -> str:
    return f"Processing ID: {user_id}"

# These are all fine
result1 = process_id(123)
result2 = process_id("ABC123")

# This will raise a type error
result3 = process_id([1, 2, 3])  # Error: Argument has type "List[int]"
```

## 4. IDE Integration

### PyCharm Setup
1. Open Settings/Preferences
2. Navigate to Tools > Python Integrated Tools
3. Under "Type Checkers", select "mypy"

### VS Code Setup
1. Install Python extension
2. Open Settings (Ctrl+,)
3. Search for "python.linting.mypyEnabled"
4. Set to "true"

## 5. Troubleshooting

### Common Issues and Solutions

1. **"No module named 'typing'"**
   ```bash
   pip install typing
   ```

2. **"Cannot find implementation or library stub"**
   Create a `mypy.ini` file:
   ```ini
   [mypy]
   ignore_missing_imports = True
   ```

3. **Type checking third-party libraries**
   Install type stubs:
   ```bash
   pip install types-requests  # Example for requests library
   ```

## Practice Exercises

### Exercise 1: Basic Function Types
Create `exercise1.py`:

```python
# TODO: Add type hints to fix mypy errors
def calculate_total(prices, tax_rate):
    return sum(prices) * (1 + tax_rate)

# Test cases
result1 = calculate_total([10, 20, 30], 0.1)
result2 = calculate_total([10, 20, "30"], 0.1)  # Should raise type error
```

### Exercise 2: Class Types
Create `exercise2.py`:

```python
# TODO: Add type hints to fix mypy errors
class ShoppingCart:
    def __init__(self):
        self.items = []
    
    def add_item(self, item, quantity):
        self.items.append({"item": item, "quantity": quantity})
    
    def get_total(self):
        return sum(item["quantity"] for item in self.items)

# Test cases
cart = ShoppingCart()
cart.add_item("apple", 3)
cart.add_item("banana", "2")  # Should raise type error
```

## Running Type Checks

### Single File
```bash
mypy filename.py
```

### Multiple Files
```bash
mypy file1.py file2.py
```

### Entire Directory
```bash
mypy .
```

### With Specific Settings
```bash
mypy --strict filename.py
mypy --disallow-untyped-defs filename.py
```

## Best Practices

1. Start with basic type hints and gradually add more complex ones
2. Use `--strict` mode for new projects
3. Create a `mypy.ini` config file for project-specific settings
4. Add type checking to your CI/CD pipeline

## Configuration Example

Create `mypy.ini`:
```ini
[mypy]
python_version = 3.8
warn_return_any = True
warn_unused_configs = True
disallow_untyped_defs = True
disallow_incomplete_defs = True
check_untyped_defs = True
disallow_untyped_decorators = True
no_implicit_optional = True
warn_redundant_casts = True
warn_unused_ignores = True
warn_no_return = True
warn_unreachable = True
```

## Resources
- [mypy Documentation](http://mypy.readthedocs.io/)
- [Python Type Checking Guide](https://realpython.com/python-type-checking/)
- [PEP 484 – Type Hints](https://www.python.org/dev/peps/pep-0484/)
