class: inverse, center, middle

# SonarQube Code Quality Management

---

class: inverse, center, middle

# Mi a SonarQube?

---

## SonarQube áttekintés

* Nyílt forráskódú kód minőség platform
* Folyamatos kód inspekció
* Security vulnerability detektálás
* Technical debt management
* Multi-language támogatás
* CI/CD integráció
* Quality Gates
* Code Coverage elemzés

---

## SonarQube architektúra

* Főbb komponensek:
  * SonarQube Server
  * Database (PostgreSQL)
  * SonarQube Scanner
  * Webhooks
  * Web UI

<img src="/api/placeholder/800/400" alt="SonarQube Architecture">

---

## SonarQube telepítés

### Docker Compose telepítés:
```yaml
version: "3"
services:
  sonarqube:
    image: sonarqube:latest
    ports:
      - "9000:9000"
    environment:
      - SONAR_JDBC_URL=jdbc:postgresql://db:5432/sonar
    depends_on:
      - db
  db:
    image: postgres:13
    environment:
      - POSTGRES_USER=sonar
      - POSTGRES_PASSWORD=sonar
```

---

## SonarQube UI áttekintés

* Projects dashboard
* Issues view
* Quality Gates
* Rules configuration
* Administration
* User management
* Security hotspots
* Code coverage view

---

## Python projekt elemzés

### Scanner telepítés:
```bash
pip install sonar-scanner
```

### Projekt konfiguráció:
```properties
# sonar-project.properties
sonar.projectKey=my-python-project
sonar.sources=src
sonar.python.coverage.reportPaths=coverage.xml
sonar.python.version=3.8
```

---

## Támogatott nyelvek

* Python
* Java
* JavaScript/TypeScript
* C#
* Go
* PHP
* Ruby
* Kotlin
* Swift
* ...és még sok más

---

## Issue kezelés

### Issue típusok:
* Bug
* Vulnerability
* Code Smell
* Security Hotspot

### Severity levels:
* Blocker
* Critical
* Major
* Minor
* Info

---

## Rules konfiguráció

### Rule példák Python-hoz:
```yaml
# Python specifikus szabályok
python:S1134:  # "FIXME" tag detected
  enabled: true
  severity: MINOR

python:S1135:  # "TODO" tag detected
  enabled: true
  severity: INFO

python:S1716:  # Duplicate branch in conditional
  enabled: true
  severity: MAJOR
```

---

## Quality Gates

### Alap Quality Gate feltételek:
* Coverage >= 80%
* Duplicate lines < 3%
* Maintainability Rating = A
* Reliability Rating = A
* Security Rating = A

### Konfiguráció példa:
```json
{
  "conditions": [
    {
      "metric": "new_coverage",
      "op": "LT",
      "warning": "85",
      "error": "80"
    }
  ]
}
```

---

## GitLab integráció

### .gitlab-ci.yml konfiguráció:
```yaml
sonarqube-check:
  image: 
    name: sonarsource/sonar-scanner-cli
    entrypoint: [""]
  variables:
    SONAR_USER_HOME: "${CI_PROJECT_DIR}/.sonar"
  script:
    - sonar-scanner
  allow_failure: true
  only:
    - merge_requests
    - main
```

---

## Jenkins integráció

### Jenkinsfile példa:
```groovy
pipeline {
    agent any
    stages {
        stage('SonarQube Analysis') {
            steps {
                script {
                    def scannerHome = tool 'SonarScanner'
                    withSonarQubeEnv('SonarQube') {
                        sh """
                            ${scannerHome}/bin/sonar-scanner \
                            -D sonar.projectKey=my-project \
                            -D sonar.sources=. \
                            -D sonar.host.url=http://sonarqube:9000
                        """
                    }
                }
            }
        }
    }
}
```

---

## Best Practices

* Rendszeres code review
* Quality Gate enforcement
* Branch policy beállítások
* Coverage mérés automatizálás
* CI/CD pipeline integráció
* Custom szabály készítés
* Team coding standards
* Technical debt kezelés

---

## Use Cases

* Pull Request ellenőrzés
* Napi build elemzés
* Security audit
* Technical debt monitoring
* Team performance tracking
* Compliance ellenőrzés
* Architecture conformance

---

## Összefoglalás

* Code quality management
* Automatikus kód elemzés
* Multi-language támogatás
* CI/CD integráció
* Quality Gates és Rules
* Best practices
* Use cases és példák

---

class: inverse, center, middle

# Kérdések?

