# Python Packaging Workshop Guide

## Overview
This guide covers the essentials of Python packaging, including creating libraries and applications, managing dependencies, and publishing packages. The workshop includes hands-on examples for creating both a library and a CLI application.

Estimated time: 90 minutes

## Table of Contents
1. [Python Package Types](#1-python-package-types)
2. [Creating a Python Library](#2-creating-a-python-library)
3. [Creating a CLI Application](#3-creating-a-cli-application)
4. [Package Management and Distribution](#4-package-management-and-distribution)

## 1. Python Package Types

### Library vs Application
- **Library**
  - Imported by other Python code
  - Provides reusable functionality
  - Example: requests, pandas
- **Application**
  - Standalone program
  - Has entry points (CLI commands)
  - Example: pytest, flask-cli

## 2. Creating a Python Library

### Project Structure
Create the following structure:
```
my_library/
├── src/
│   └── mylibrary/
│       ├── __init__.py
│       └── core.py
├── tests/
│   ├── __init__.py
│   └── test_core.py
├── README.md
├── setup.py
├── setup.cfg
├── pyproject.toml
└── MANIFEST.in
```

### Basic Implementation

1. Create core functionality (`src/mylibrary/core.py`):
```python
def calculate_sum(numbers):
    """Calculate sum of numbers."""
    return sum(numbers)

def calculate_average(numbers):
    """Calculate average of numbers."""
    if not numbers:
        raise ValueError("Cannot calculate average of empty sequence")
    return sum(numbers) / len(numbers)
```

2. Initialize package (`src/mylibrary/__init__.py`):
```python
"""A simple mathematical operations library."""

from .core import calculate_sum, calculate_average

__version__ = "0.1.0"
```

3. Create setup.py:
```python
from setuptools import setup, find_packages

setup(
    name="mylibrary",
    version="0.1.0",
    package_dir={"": "src"},
    packages=find_packages(where="src"),
    install_requires=[
        "numpy>=1.20.0",
    ],
    extras_require={
        "dev": [
            "pytest>=6.0",
            "pytest-cov>=2.0",
        ],
    },
    python_requires=">=3.7",
    author="Your Name",
    author_email="your.email@example.com",
    description="A simple mathematical operations library",
    long_description=open("README.md").read(),
    long_description_content_type="text/markdown",
    url="https://github.com/yourusername/mylibrary",
    classifiers=[
        "Programming Language :: Python :: 3",
        "License :: OSI Approved :: MIT License",
        "Operating System :: OS Independent",
    ],
)
```

4. Create setup.cfg:
```ini
[metadata]
name = mylibrary
version = 0.1.0
description = A simple mathematical operations library
long_description = file: README.md
long_description_content_type = text/markdown

[options]
package_dir =
    = src
packages = find:
python_requires = >=3.7
install_requires =
    numpy>=1.20.0

[options.packages.find]
where = src

[options.extras_require]
dev =
    pytest>=6.0
    pytest-cov>=2.0
```

5. Create pyproject.toml:
```toml
[build-system]
requires = ["setuptools>=45", "wheel"]
build-backend = "setuptools.build_meta"
```

### Including Non-Python Files
1. Create MANIFEST.in:
```
include README.md
include LICENSE
recursive-include src/mylibrary/data *.json *.csv
```

2. Update setup.py:
```python
setup(
    # ... other options ...
    include_package_data=True,
)
```

## 3. Creating a CLI Application

### Project Structure for CLI
```
my_cli_app/
├── src/
│   └── mycli/
│       ├── __init__.py
│       ├── cli.py
│       └── core.py
├── tests/
├── setup.py
└── README.md
```

### Implementation

1. Create CLI entry point (`src/mycli/cli.py`):
```python
import argparse
from .core import calculate_sum, calculate_average

def main():
    parser = argparse.ArgumentParser(description="Mathematical operations CLI")
    parser.add_argument("numbers", type=float, nargs="+", help="Numbers to process")
    parser.add_argument("--operation", choices=["sum", "average"], default="sum",
                      help="Operation to perform")

    args = parser.parse_args()

    if args.operation == "sum":
        result = calculate_sum(args.numbers)
        print(f"Sum: {result}")
    else:
        result = calculate_average(args.numbers)
        print(f"Average: {result}")

if __name__ == "__main__":
    main()
```

2. Update setup.py for CLI:
```python
setup(
    name="mycli",
    # ... other options ...
    entry_points={
        "console_scripts": [
            "mycli=mycli.cli:main",
        ],
    },
)
```

## 4. Package Management and Distribution

### Local Installation

Development mode:
```bash
# From project directory
pip install -e .

# With development dependencies
pip install -e ".[dev]"
```

Regular installation:
```bash
pip install .
```

### Building the Package
```bash
# Install build tools
pip install build

# Build the package
python -m build
```

This creates:
- `dist/mylibrary-0.1.0.tar.gz` (source distribution)
- `dist/mylibrary-0.1.0-py3-none-any.whl` (wheel distribution)

### Publishing with Twine

1. Install Twine:
```bash
pip install twine
```

2. Create PyPI accounts:
- Test PyPI: https://test.pypi.org/account/register/
- Production PyPI: https://pypi.org/account/register/

3. Create .pypirc:
```ini
[distutils]
index-servers =
    pypi
    testpypi

[pypi]
username = __token__
password = your_pypi_token

[testpypi]
repository = https://test.pypi.org/legacy/
username = __token__
password = your_testpypi_token
```

4. Upload to Test PyPI:
```bash
twine upload --repository testpypi dist/*
```

5. Upload to Production PyPI:
```bash
twine upload dist/*
```

### Testing the Installation

From Test PyPI:
```bash
pip install --index-url https://test.pypi.org/simple/ mylibrary
```

From Production PyPI:
```bash
pip install mylibrary
```

## Best Practices

1. Package Organization
   - Use src layout
   - Separate tests from source
   - Include documentation

2. Version Management
   - Use semantic versioning
   - Single source of truth for version

3. Dependencies
   - Specify minimum versions
   - Use ranges instead of exact versions
   - Group optional dependencies

4. Documentation
   - Clear README
   - API documentation
   - Usage examples

## Common Issues and Solutions

1. Package Not Found
```bash
# Check PYTHONPATH
echo $PYTHONPATH

# Verify installation
pip list | grep mylibrary
```

2. Entry Points Not Working
```bash
# Reinstall package
pip uninstall mylibrary
pip install -e .
```

3. Missing Dependencies
```bash
# Install with all extras
pip install ".[dev,test,docs]"
```

## Resources
- [Python Packaging User Guide](https://packaging.python.org/)
- [setuptools documentation](https://setuptools.pypa.io/)
- [PyPI](https://pypi.org/)
- [Twine documentation](https://twine.readthedocs.io/)
