# GitHub Actions Guide for Python with Docker

## Overview
This guide demonstrates how to set up GitHub Actions for a Python application using Docker, including building, testing, and code quality checks.

## Table of Contents
1. [Project Setup](#1-project-setup)
2. [Docker Configuration](#2-docker-configuration)
3. [GitHub Actions Workflow](#3-github-actions-workflow)
4. [Running and Monitoring](#4-running-and-monitoring)

## 1. Project Setup

### Project Structure
```
project/
├── .github/
│   └── workflows/
│       └── python-app.yml
├── src/
│   ├── __init__.py
│   └── character_count.py
├── tests/
│   ├── __init__.py
│   └── test_character_count.py
├── Dockerfile
├── requirements.txt
└── README.md
```

### Source Code
`src/character_count.py`:
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
`tests/test_character_count.py`:
```python
import os
import pytest
from src.character_count import count_characters

def create_test_file(content):
    """Helper function to create test files."""
    with open('test_file.txt', 'w') as f:
        f.write(content)

def test_empty_file():
    create_test_file("")
    assert count_characters('test_file.txt') == 0
    os.remove('test_file.txt')

def test_simple_content():
    create_test_file("Hello, World!")
    assert count_characters('test_file.txt') == 13
    os.remove('test_file.txt')

def test_nonexistent_file():
    assert count_characters('nonexistent.txt') == -1
```

## 2. Docker Configuration

### Dockerfile
```dockerfile
FROM python:3.9-slim

WORKDIR /app

# Install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Copy source code
COPY . .

# Default command
ENTRYPOINT ["python", "src/character_count.py"]
```

### Requirements
`requirements.txt`:
```txt
pytest==7.4.3
flake8==6.1.0
black==23.11.0
```

## 3. GitHub Actions Workflow

Create `.github/workflows/python-app.yml`:
```yaml
name: Python Application CI

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build-and-test:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v2
    
    - name: Set up Docker Buildx
      uses: docker/setup-buildx-action@v1
    
    - name: Build Docker image
      uses: docker/build-push-action@v2
      with:
        context: .
        load: true
        tags: character-counter:test
        cache-from: type=gha
        cache-to: type=gha,mode=max
    
    - name: Code Quality Check
      run: |
        docker run character-counter:test flake8 src/ --max-line-length=88
        docker run character-counter:test black src/ --check
    
    - name: Run Tests
      run: |
        docker run character-counter:test pytest tests/ -v
    
    - name: Test Application
      run: |
        echo "Hello, World!" > test.txt
        docker run -v ${PWD}:/app character-counter:test /app/test.txt

  notify:
    needs: build-and-test
    runs-on: ubuntu-latest
    if: always()
    
    steps:
    - name: Send Notification
      run: |
        if [ "${{ needs.build-and-test.result }}" == "success" ]; then
          echo "Build succeeded!"
        else
          echo "Build failed!"
        fi
```

## 4. Running and Monitoring

### Setting Up Repository
1. Push code to GitHub:
```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/username/repo.git
git push -u origin main
```

2. Enable GitHub Actions:
- Go to repository settings
- Navigate to "Actions" section
- Enable GitHub Actions

### Monitoring Workflows
1. Navigate to "Actions" tab in your repository
2. View workflow runs
3. Check detailed logs
4. Monitor test results and code quality reports

### Triggering Workflows
Workflows are triggered automatically on:
- Push to main branch
- Pull requests to main branch

Manual trigger:
```bash
# Make changes
git checkout -b feature/new-feature
git add .
git commit -m "Add new feature"
git push origin feature/new-feature

# Create pull request in GitHub UI
```

## Optional Enhancements

### Add Code Coverage
Update workflow file:
```yaml
    - name: Run Tests with Coverage
      run: |
        docker run character-counter:test pytest tests/ --cov=src -v
```

### Add Branch Protection
1. Go to repository settings
2. Navigate to Branches
3. Add rule for main branch:
   - Require status checks to pass
   - Require pull request reviews
   - Enable branch protection

### Add Build Badge
Add to README.md:
```markdown
![Build Status](https://github.com/username/repo/workflows/Python%20Application%20CI/badge.svg)
```

## Common Issues and Solutions

1. **Docker Build Fails**
   - Check Dockerfile syntax
   - Verify requirements.txt
   - Check GitHub Actions logs

2. **Tests Fail**
   - View detailed test output in Actions
   - Check test environment
   - Verify file permissions

3. **Code Quality Issues**
   - Run checks locally first:
   ```bash
   docker build -t character-counter:test .
   docker run character-counter:test flake8 src/
   docker run character-counter:test black src/ --check
   ```

## Best Practices

1. **Workflow Organization**
   - Keep jobs focused and simple
   - Use meaningful step names
   - Leverage caching for speed

2. **Security**
   - Don't expose secrets in logs
   - Use GitHub Secrets for sensitive data
   - Regular dependency updates

3. **Performance**
   - Use Docker layer caching
   - Optimize test execution
   - Clean up resources

## Next Steps
1. Add deployment stages
2. Implement semantic versioning
3. Add automated releases
4. Set up container registry publishing
