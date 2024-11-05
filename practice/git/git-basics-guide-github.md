# Git Version Control with GitHub Workshop

## Overview
This workshop covers Git fundamentals, GitHub workflows, and branching strategies, with a focus on Python development.

Estimated time: 120 minutes

## Table of Contents
1. [Git Overview](#1-git-overview)
2. [Version Control Systems](#2-version-control-systems)
3. [Git CLI Commands](#3-git-cli-commands)
4. [GitHub Hands-on](#4-github-hands-on)
5. [GitHub Pull Requests](#5-github-pull-requests)
6. [Pre-commit Hooks](#6-pre-commit-hooks)
7. [Branching Strategies](#7-branching-strategies)
8. [Best Practices](#8-best-practices)

## 1. Git Overview

### What is Git?
- Distributed version control system
- Tracks changes in source code
- Enables collaboration
- Maintains history of changes

### Key Concepts
- Repository (repo)
- Commit
- Branch
- Remote
- Clone
- Push/Pull

## 2. Version Control Systems

### Types of VCS
1. **Centralized (CVCS)**
   - SVN
   - Perforce
   - CVS

2. **Distributed (DVCS)**
   - Git
   - Mercurial
   - Bazaar

### Git vs Others
- Distributed nature
- Branching capabilities
- Speed
- Community support

## 3. Git CLI Commands

### Essential Commands
```bash
# Initialize repository
git init

# Check status
git status

# Add files
git add filename
git add .

# Commit changes
git commit -m "Message"

# View history
git log
git log --oneline

# Branch operations
git branch branch-name
git checkout branch-name
git checkout -b new-branch

# Remote operations
git remote add origin URL
git push origin branch-name
git pull origin branch-name
```

## 4. GitHub Hands-on

### Creating a Repository

1. **Create GitHub Account**
   - Go to [GitHub](https://github.com)
   - Sign up for an account
   - Verify email

2. **Create New Repository**
   - Click "+" in top right
   - Select "New repository"
   - Name your repository
   - Add README (optional)
   - Add .gitignore (choose Python)
   - Choose license

3. **Clone Repository**
```bash
git clone https://github.com/username/repository.git
cd repository
```

### Basic Project Setup
```bash
# Create Python project structure
mkdir my_project
cd my_project

# Initialize Git
git init

# Create basic structure
mkdir src tests
touch src/__init__.py
touch src/main.py
touch tests/__init__.py
touch tests/test_main.py
touch requirements.txt
touch .gitignore

# Link to GitHub
git remote add origin https://github.com/username/my_project.git
git branch -M main
git push -u origin main
```

### SSH Setup for GitHub
```bash
# Generate SSH key
ssh-keygen -t ed25519 -C "your_email@example.com"

# Start ssh-agent
eval "$(ssh-agent -s)"

# Add SSH key to ssh-agent
ssh-add ~/.ssh/id_ed25519

# Copy public key
cat ~/.ssh/id_ed25519.pub
# Add this key to GitHub (Settings > SSH and GPG keys)
```

## 5. GitHub Pull Requests

### Creating a Pull Request

1. Create feature branch
```bash
git checkout -b feature/new-feature
```

2. Make changes
```bash
# Edit files
git add .
git commit -m "Implement new feature"
```

3. Push changes
```bash
git push origin feature/new-feature
```

4. Create Pull Request on GitHub
   - Go to repository on GitHub
   - Click "Pull requests"
   - Click "New pull request"
   - Select base and compare branches
   - Add title and description
   - Add reviewers
   - Create pull request

### Pull Request Best Practices
1. **Clear Title and Description**
```markdown
Title: Add user authentication feature

Description:
- Implement login/logout functionality
- Add password hashing
- Create user session management
- Add unit tests for authentication

Related Issue: #42
```

2. **Review Process**
   - Request reviews from team members
   - Address feedback
   - Update PR with requested changes
   - Resolve conversations
   - Merge when approved

## 6. Pre-commit Hooks

### Setup Pre-commit for Python

1. Install pre-commit:
```bash
pip install pre-commit
```

2. Create `.pre-commit-config.yaml`:
```yaml
repos:
-   repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
    -   id: trailing-whitespace
    -   id: end-of-file-fixer
    -   id: check-yaml
    -   id: check-added-large-files

-   repo: https://github.com/psf/black
    rev: 23.3.0
    hooks:
    -   id: black

-   repo: https://github.com/PyCQA/flake8
    rev: 6.0.0
    hooks:
    -   id: flake8

-   repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.3.0
    hooks:
    -   id: mypy
        additional_dependencies: [types-all]
```

3. Install hooks:
```bash
pre-commit install
```

test all
```bash
pip install pre-commit && pre-commit install && pre-commit run --all-files
```

4. Test commit:
```bash
git add .
git commit -m "Test pre-commit hooks"
```

## 7. Branching Strategies

### GitHub Flow
Simple, lightweight flow ideal for small teams:
```
main
├── feature/feature1
├── feature/feature2
└── bugfix/fix1
```

#### Process
1. Create branch from main
2. Add commits
3. Open Pull Request
4. Review and discuss
5. Deploy and test
6. Merge to main

### Trunk-Based Development
```
main
├── feature1
├── feature2
└── feature3
```

#### Commands
```bash
# Feature branch
git checkout -b feature/quick-fix
git commit -m "Fix bug"
git push origin feature/quick-fix
# Create pull request
# After approval, merge to main
```

## 8. Best Practices

### GitHub-Specific Best Practices

1. **Repository Settings**
   - Enable branch protection
   - Require pull request reviews
   - Enable required status checks
   - Enforce signed commits

2. **Issue Templates**
Create `.github/ISSUE_TEMPLATE.md`:
```markdown
## Expected Behavior

## Current Behavior

## Steps to Reproduce

## Environment

## Additional Context
```

3. **Pull Request Templates**
Create `.github/PULL_REQUEST_TEMPLATE.md`:
```markdown
## Description
What does this PR do?

## Related Issues
Fixes #

## Testing
How has this been tested?

## Screenshots (if appropriate)

## Types of changes
- [ ] Bug fix
- [ ] New feature
- [ ] Breaking change
```

### GitHub Actions for Python
Create `.github/workflows/python-app.yml`:
```yaml
name: Python application

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: 3.9
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install flake8 pytest
        if [ -f requirements.txt ]; then pip install -r requirements.txt; fi
    - name: Lint with flake8
      run: |
        flake8 .
    - name: Test with pytest
      run: |
        pytest
```

## Common Issues and Solutions

1. **Authentication Failed**
```bash
# Setup credential manager
git config --global credential.helper cache
```

2. **Merge Conflicts**
```bash
# Get latest changes
git fetch origin
git checkout main
git pull origin main

# Merge main into feature branch
git checkout feature-branch
git merge main

# Resolve conflicts and commit
git add .
git commit -m "Resolve merge conflicts"
```

## Resources
- [GitHub Documentation](https://docs.github.com)
- [GitHub Guides](https://guides.github.com)
- [GitHub Skills](https://skills.github.com)
- [Pre-commit Documentation](https://pre-commit.com)
