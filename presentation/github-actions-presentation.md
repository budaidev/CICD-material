class: inverse, center, middle

# GitHub Actions és Local Testing

---

class: inverse, center, middle

# Mi a GitHub Actions?

---

## GitHub Actions áttekintés

* Automatizált CI/CD platform
* Workflow alapú megközelítés
* Event-driven architektúra
* Beépített GitHub integráció
* Marketplace with pre-built actions
* Self-hosted runners lehetőség
* Matrix builds támogatás
* Artifact kezelés

---

## Workflow struktúra

```yaml
name: CI Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Set up Python
        uses: actions/setup-python@v4
```

---

## Events és Triggers

### Esemény típusok:
* push
* pull_request
* release
* schedule
* workflow_dispatch
* repository_dispatch

### Példa konfiguráció:
```yaml
on:
  push:
    branches:
      - main
      - 'releases/**'
    tags:
      - v*
  pull_request:
    types: [opened, synchronize]
```

---

## Jobs és Steps

### Job konfiguráció:
```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: [3.8, 3.9, 3.10]
    
    steps:
      - uses: actions/checkout@v3
      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v4
        with:
          python-version: ${{ matrix.python-version }}
```

---

## Environment és Secrets

### Környezeti változók:
```yaml
env:
  GLOBAL_VAR: "value"

jobs:
  build:
    env:
      JOB_VAR: "value"
    steps:
      - name: Step with env
        env:
          STEP_VAR: "value"
        run: echo $STEP_VAR
```

### Secrets használata:
```yaml
steps:
  - name: Deploy
    env:
      API_TOKEN: ${{ secrets.API_TOKEN }}
    run: ./deploy.sh
```

---

## Artifacts és Cache

### Artifact kezelés:
```yaml
- name: Upload artifact
  uses: actions/upload-artifact@v3
  with:
    name: my-artifact
    path: dist/

- name: Download artifact
  uses: actions/download-artifact@v3
  with:
    name: my-artifact
```

### Cache használat:
```yaml
- name: Cache pip packages
  uses: actions/cache@v3
  with:
    path: ~/.cache/pip
    key: ${{ runner.os }}-pip-${{ hashFiles('**/requirements.txt') }}
```

---

## Marketplace Actions

### Népszerű actions:
* actions/checkout
* actions/setup-python
* actions/cache
* docker/build-push-action
* codecov/codecov-action
* JamesIves/github-pages-deploy-action

```yaml
- name: Build and push Docker image
  uses: docker/build-push-action@v4
  with:
    context: .
    push: true
    tags: user/app:latest
```

---

## Act - Local Testing

### Mi az Act?
* GitHub Actions lokális futtatása
* Docker alapú környezet
* Workflow debuggolás
* Gyors iteráció

### Telepítés:
```bash
# macOS
brew install act

# Linux
curl https://raw.githubusercontent.com/nektos/act/master/install.sh | sudo bash
```

---

## Act használata

### Alapvető parancsok:
```bash
# Összes workflow futtatása
act

# Konkrét event trigger
act push

# Konkrét job futtatása
act -j build

# Lista workflow-król
act -l

# Secrets használata
act --secret-file my.secrets
```

---

## Act konfiguráció

### .actrc file:
```bash
-P ubuntu-latest=nektos/act-environments-ubuntu:18.04
--secret-file my.secrets
-j build
```

### Platform override:
```bash
# Minimális image használata
act -P ubuntu-latest=node:16-slim

# Teljes image
act -P ubuntu-latest=nektos/act-environments-ubuntu:18.04
```

---

## Komplex Workflow példa

```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.10'
          
      - name: Install dependencies
        run: |
          pip install -r requirements.txt
          pip install pytest pytest-cov
          
      - name: Run tests
        run: pytest --cov
        
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        
  deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v3
      
      - name: Deploy
        run: |
          echo "Deploying..."
```

---

## Best Practices

* Workflow modularizálás
* Reusable workflows használata
* Minimális futási idő
* Cache használat
* Megfelelő trigger kondíciók
* Secrets biztonságos kezelése
* Self-hosted runner biztonsága
* Matrix builds optimalizálás
* Job függőségek tervezése

---

## Debugging és Monitoring

### Debug logging:
```yaml
env:
  ACTIONS_RUNNER_DEBUG: true
  ACTIONS_STEP_DEBUG: true
```

### Status badge:
```markdown
![CI](https://github.com/user/repo/actions/workflows/ci.yml/badge.svg)
```

---

class: inverse, center, middle

# Kérdések?

