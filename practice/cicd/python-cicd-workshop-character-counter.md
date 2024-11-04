# Local CI/CD Workshop Guide: Character Counter Project

## Overview
This workshop guides you through implementing a local CI/CD pipeline for a Python character counter application using Task and Docker.

Estimated time: 60 minutes

## Prerequisites
- Windows 10/11
- Administrative access
- Docker Desktop installed
- Chocolatey installed
- Basic Python knowledge

## 1. Initial Setup (10 minutes)

### Install Task Runner
Open PowerShell as Administrator and run:
```powershell
choco install go-task
```

Verify installation:
```powershell
task --version
```

### Project Structure
Create the following structure:
```
python-cicd/
├── Dockerfile
├── Taskfile.yml
├── requirements.txt
├── src/
│   ├── __init__.py
│   └── character_count.py
├── tests/
│   ├── __init__.py
│   └── test_character_count.py
└── sample_files/
    └── sample.txt
```

## 2. Project Implementation (20 minutes)

### Main Application
Create `src/character_count.py`:
```python
import sys

def count_characters(file_path):
    try:
        with open(file_path, 'r') as file:
            text = file.read()
            char_count = len(text)
            return char_count
    except FileNotFoundError:
        return -1

if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Usage: python character_count.py <file_path>")
        sys.exit(1)

    file_path = sys.argv[1]
    result = count_characters(file_path)
    if result == -1:
        print("File not found.")
    else:
        print(f"Character count: {result}")
```

### Test File
Create `tests/test_character_count.py`:
```python
import os
import pytest
from src.character_count import count_characters

def create_test_file(content):
    """Helper function to create test files."""
    with open('test_file.txt', 'w') as f:
        f.write(content)

def test_empty_file():
    """Test counting characters in an empty file."""
    create_test_file("")
    assert count_characters('test_file.txt') == 0
    os.remove('test_file.txt')

def test_simple_content():
    """Test counting characters in a file with simple content."""
    create_test_file("Hello, World!")
    assert count_characters('test_file.txt') == 13
    os.remove('test_file.txt')

def test_multiline_content():
    """Test counting characters in a file with multiple lines."""
    content = """Line 1
    Line 2
    Line 3"""
    create_test_file(content)
    assert count_characters('test_file.txt') == len(content)
    os.remove('test_file.txt')

def test_special_characters():
    """Test counting special characters."""
    content = "Hello\nWorld\t!@#$%^&*()"
    create_test_file(content)
    assert count_characters('test_file.txt') == len(content)
    os.remove('test_file.txt')

def test_nonexistent_file():
    """Test handling of nonexistent file."""
    assert count_characters('nonexistent.txt') == -1

@pytest.fixture(autouse=True)
def cleanup():
    """Cleanup any test files after tests."""
    yield
    if os.path.exists('test_file.txt'):
        os.remove('test_file.txt')
```

### Dependencies
Create `requirements.txt`:
```txt
pytest==7.4.3
pytest-cov==4.1.0
flake8==6.1.0
black==23.11.0
```

## 3. Docker Configuration (15 minutes)

### Dockerfile
Create `Dockerfile`:
```dockerfile
FROM python:3.9-slim

WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy project files
COPY . .

# Default command
ENTRYPOINT ["python", "src/character_count.py"]
CMD ["data/sample.txt"]
```

### Task Configuration
Create `Taskfile.yml`:
```yaml
version: '3'

vars:
  IMAGE_NAME: character-counter
  CONTAINER_NAME: character-counter-app

tasks:
  docker-build:
    desc: Build Docker image
    cmds:
      - docker build -t {{.IMAGE_NAME}} .

  docker-run:
    desc: Run Docker container with sample file
    deps: [docker-build]
    cmds:
      - docker run --rm --name {{.CONTAINER_NAME}} -v ${PWD}/sample_files:/app/data {{.IMAGE_NAME}} data/sample.txt

  docker-test:
    desc: Run tests in Docker
    deps: [docker-build]
    cmds:
      - docker run --rm {{.IMAGE_NAME}} python -m pytest tests/ -v --cov=src

  docker-lint:
    desc: Run linting in Docker
    deps: [docker-build]
    cmds:
      - docker run --rm {{.IMAGE_NAME}} python -m flake8 src/
      - docker run --rm {{.IMAGE_NAME}} python -m black src/ --check

  docker-clean:
    desc: Clean Docker resources
    cmds:
      - cmd: docker stop {{.CONTAINER_NAME}}
        ignore_error: true
      - cmd: docker rm {{.CONTAINER_NAME}}
        ignore_error: true
      - cmd: docker rmi {{.IMAGE_NAME}}
        ignore_error: true

  docker-all:
    desc: Run complete Docker pipeline
    cmds:
      - task: docker-clean
      - task: docker-build
      - task: docker-lint
      - task: docker-test
      - task: docker-run

  create-sample:
    desc: Create a sample text file
    cmds:
      - mkdir -p sample_files
      - echo "This is a sample text file.\nIt contains multiple lines.\nWe can count its characters." > sample_files/sample.txt
```

## 4. Running the Pipeline (15 minutes)

### Create Sample File
```powershell
task create-sample
```

### View Available Commands
```powershell
task --list
```

### Run Individual Steps
```powershell
# Build the Docker image
task docker-build

# Run linting
task docker-lint

# Run tests with coverage
task docker-test

# Run the application
task docker-run

# Clean up resources
task docker-clean
```

### Run Complete Pipeline
```powershell
task docker-all
```

## Pipeline Steps Explanation

1. **docker-clean**
   - Removes any existing containers and images
   - Ensures clean state for testing

2. **docker-build**
   - Creates Docker image with Python environment
   - Installs all required dependencies
   - Copies application code

3. **docker-lint**
   - Checks code style with flake8
   - Verifies formatting with black
   - Ensures code quality

4. **docker-test**
   - Runs pytest test suite
   - Generates coverage report
   - Verifies all functionality

5. **docker-run**
   - Executes application
   - Processes sample file
   - Shows character count

## Test Cases Explanation

1. **Empty File Test**
   - Verifies handling of empty files
   - Expected count: 0

2. **Simple Content Test**
   - Tests basic character counting
   - Includes standard ASCII characters

3. **Multiline Content Test**
   - Verifies handling of line breaks
   - Tests indentation and spacing

4. **Special Characters Test**
   - Tests handling of escape sequences
   - Includes special symbols

5. **Nonexistent File Test**
   - Verifies error handling
   - Expected return: -1

## Troubleshooting Guide

### Common Issues and Solutions

1. **File Not Found Errors**
   ```powershell
   # Verify sample file exists
   dir sample_files
   
   # Recreate sample file
   task create-sample
   ```

2. **Test Failures**
   ```powershell
   # Run tests with detailed output
   task docker-test
   
   # Check test file permissions
   icacls test_file.txt
   ```

3. **Docker Volume Issues**
   ```powershell
   # Verify volume mounting
   docker run --rm -v ${PWD}/sample_files:/app/data {{.IMAGE_NAME}} ls /app/data
   ```

## Best Practices

1. **Testing**
   - Write tests for all edge cases
   - Maintain high coverage
   - Clean up test files

2. **Docker**
   - Use specific base image versions
   - Minimize image size
   - Handle file permissions

3. **CI/CD**
   - Run full pipeline before commits
   - Version control all configurations
   - Monitor test coverage

## Next Steps

1. Enhance Application:
   - Add word counting
   - Support multiple files
   - Generate statistics

2. Improve Pipeline:
   - Add security scanning
   - Implement automated releases
   - Add performance testing

3. Extend Testing:
   - Add integration tests
   - Test different encodings
   - Add stress testing
