# Jenkins CI/CD Training Guide
This comprehensive guide covers Jenkins installation, basic concepts, and practical implementations for CI/CD pipelines.

### 3. Jenkins Job Types Overview (30 minutes)
- Freestyle Projects
- Pipeline Projects
- Multi-branch Pipeline
- Organization Folders
- Matrix Projects

### 4. Freestyle Project Workshop
#### Python Build and Package Example (60 minutes)

##### Project Setup
1. Create new Freestyle project:
   - Click "New Item"
   - Enter name: "python-hello-world"
   - Select "Freestyle project"
   - Click OK

2. Configure Source Code Management:
```bash
# Sample Python project structure
my-python-app/
├── src/
│   └── hello.py
├── tests/
│   └── test_hello.py
├── requirements.txt
└── setup.py
```

3. Configure Build Steps:
```bash
# Add build step "Execute shell"
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
python -m pytest tests/
python setup.py bdist_wheel
```

4. Add Post-build Actions:
   - Archive artifacts: `dist/*.whl`
   - Publish test results: `test-results/*.xml`

### 5. Jenkins Plugin Management (30 minutes)
#### Essential Plugins Installation
1. Navigate to "Manage Jenkins" > "Plugins"
2. Install recommended plugins:
   - Blue Ocean
   - Pipeline
   - Git
   - Docker
   - Python

#### Jenkins Service Management
```bash
# Start Jenkins
docker start jenkins

# Stop Jenkins
docker stop jenkins

# View logs
docker logs -f jenkins
```

## Session 5: Advanced Jenkins Configuration

### 1. Blue Ocean Setup and Usage (45 minutes)
1. Install Blue Ocean plugin:
   - Navigate to Plugin Manager
   - Search for "Blue Ocean"
   - Install without restart

2. Access Blue Ocean interface:
   - Click "Open Blue Ocean" in sidebar
   - Create new Pipeline

### 2. Python Multi-stage Pipeline Example

#### Pipeline Configuration (60 minutes)
```groovy
// Jenkinsfile
pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/your-repo/python-app.git'
            }
        }
        
        stage('Build') {
            steps {
                sh '''
                    python -m venv venv
                    . venv/bin/activate
                    pip install -r requirements.txt
                '''
            }
        }
        
        stage('Test') {
            steps {
                sh '''
                    . venv/bin/activate
                    python -m pytest tests/ --junitxml=test-results/junit.xml
                '''
            }
            post {
                always {
                    junit 'test-results/junit.xml'
                }
            }
        }
        
        stage('Package') {
            steps {
                sh '''
                    . venv/bin/activate
                    python setup.py bdist_wheel
                '''
            }
        }
    }
}
```

### 3. Nexus Integration (45 minutes)

#### Nexus Setup
1. Start Nexus container:
```bash
docker run -d \
  --name nexus \
  --network jenkins-network \
  -p 8081:8081 \
  sonatype/nexus3
```

2. Configure Nexus repository:
   - Create PyPI hosted repository
   - Create Docker hosted repository
   - Configure authentication

#### Publishing Python Package to Nexus
```groovy
// Add to Jenkinsfile
stage('Publish to Nexus') {
    steps {
        sh '''
            . venv/bin/activate
            pip install twine
            twine upload --repository-url http://nexus:8081/repository/pypi-internal/ \
                        -u admin -p admin123 \
                        dist/*
        '''
    }
}
```

#### Publishing Docker Image to Nexus
```groovy
// Add to Jenkinsfile
stage('Build and Push Docker Image') {
    steps {
        sh '''
            docker build -t nexus:8081/my-python-app:${BUILD_NUMBER} .
            docker push nexus:8081/my-python-app:${BUILD_NUMBER}
        '''
    }
}
```

### Practice Exercises

1. Basic Pipeline Creation
```
Task: Create a simple pipeline that:
- Checks out code from Git
- Runs Python tests
- Creates a wheel package
- Expected time: 30 minutes
```

2. Blue Ocean Pipeline
```
Task: Convert the basic pipeline to Blue Ocean format:
- Create parallel test stages
- Add test reporting
- Add build notifications
- Expected time: 45 minutes
```

3. Nexus Integration
```
Task: Extend the pipeline to:
- Publish Python package to Nexus
- Build and push Docker image
- Add version tagging
- Expected time: 45 minutes
```

### Troubleshooting Guide

Common Issues and Solutions:
1. Jenkins container won't start
```bash
# Check docker logs
docker logs jenkins

# Verify volume permissions
ls -l /var/lib/docker/volumes/jenkins-data/_data
```

2. Pipeline fails during Python build
```bash
# Check Python environment
docker exec jenkins python --version

# Verify pip installation
docker exec jenkins pip --version
```

3. Nexus connection issues
```bash
# Test network connectivity
docker exec jenkins ping nexus

# Verify Nexus credentials
curl -u admin:admin123 http://nexus:8081/service/rest/v1/status
```

### Additional Resources
- Official Jenkins Documentation: https://www.jenkins.io/doc/
- Blue Ocean Documentation: https://www.jenkins.io/projects/blueocean/
- Nexus Repository OSS: https://help.sonatype.com/repomanager3
