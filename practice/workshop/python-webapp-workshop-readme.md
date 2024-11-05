# Python Web Application Development and CI/CD Workshop

## Overview
This guide walks through creating a production-ready Python REST API with automated deployment pipelines. Learn how to build a Flask-based web application, containerize it, and implement professional development workflows with pre-commit hooks and GitHub Actions.

## Prerequisites
- Python 3.8+
- Docker Desktop
- Git
- GitHub account
- Basic understanding of REST APIs
- Basic command line knowledge

## Workshop Contents

### 1. Project Setup
```bash
# Create project structure
my-flask-api/
├── .github/
│   └── workflows/
│       └── ci-cd.yml
├── app/
│   ├── __init__.py
│   ├── routes.py
│   └── models.py
├── tests/
│   ├── __init__.py
│   └── test_routes.py
├── .pre-commit-config.yaml
├── Dockerfile
├── docker-compose.yml
├── requirements.txt
├── setup.py
└── README.md
```

### 2. Flask REST API Development

#### Key Components
- REST API endpoints implementation
- Request/Response handling
- Error handling
- Input validation
- Database integration (optional)
- API documentation with Swagger/OpenAPI

#### Example Implementation
```python
from flask import Flask, jsonify, request
from flask_cors import CORS

app = Flask(__name__)
CORS(app)

@app.route('/api/health', methods=['GET'])
def health_check():
    return jsonify({'status': 'healthy'})

@app.route('/api/items', methods=['GET', 'POST'])
def handle_items():
    if request.method == 'GET':
        return jsonify({'items': []})
    else:
        data = request.get_json()
        # Process POST request
        return jsonify({'message': 'Item created'}), 201
```

### 3. Development Tools Setup

#### setup.py Configuration
```python
from setuptools import setup, find_packages

setup(
    name="my-flask-api",
    version="0.1.0",
    packages=find_packages(),
    install_requires=[
        "flask>=2.0.0",
        "flask-cors>=3.0.0",
        "gunicorn>=20.1.0",
    ],
    extras_require={
        "dev": [
            "pytest>=7.0.0",
            "pytest-cov>=3.0.0",
            "black>=22.0.0",
            "flake8>=4.0.0",
            "isort>=5.0.0",
        ]
    }
)
```

#### Pre-commit Hooks Configuration
```yaml
# .pre-commit-config.yaml
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

-   repo: https://github.com/pycqa/isort
    rev: 5.12.0
    hooks:
    -   id: isort

-   repo: https://github.com/pycqa/flake8
    rev: 6.0.0
    hooks:
    -   id: flake8
```

### 4. Docker Configuration

#### Dockerfile
```dockerfile
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["gunicorn", "--bind", "0.0.0.0:5000", "app:app"]
```

#### docker-compose.yml
```yaml
version: '3.8'

services:
  api:
    build: .
    ports:
      - "5000:5000"
    environment:
      - FLASK_ENV=production
    restart: unless-stopped
```

### 5. CI/CD Pipeline

#### GitHub Actions Workflow
```yaml
# .github/workflows/ci-cd.yml
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
        python-version: '3.9'
    
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -e .[dev]
    
    - name: Run tests
      run: pytest --cov=app tests/
    
    - name: Lint
      run: |
        black --check .
        isort --check-only .
        flake8 .

  build:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Build and push Docker image
      run: |
        docker build -t my-flask-api .
        # Add Docker push commands here for your registry
```

### 6. Reverse Proxy and SSL Configuration

#### Nginx Configuration Example
```nginx
# /etc/nginx/sites-available/flask-api
server {
    listen 80;
    server_name api.yourdomain.com;
    
    # Redirect HTTP to HTTPS
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name api.yourdomain.com;

    ssl_certificate /etc/letsencrypt/live/api.yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/api.yourdomain.com/privkey.pem;

    # SSL configuration
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384;

    location / {
        proxy_pass http://localhost:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### 7. SSL Certificate Setup with Let's Encrypt
```bash
# Install Certbot
sudo apt-get update
sudo apt-get install certbot python3-certbot-nginx

# Obtain SSL certificate
sudo certbot --nginx -d api.yourdomain.com
```

## Getting Started

1. Clone the repository:
```bash
git clone <repository-url>
cd my-flask-api
```

2. Install dependencies:
```bash
pip install -e .[dev]
```

3. Set up pre-commit hooks:
```bash
pre-commit install
```

4. Run the application locally:
```bash
flask run
```

5. Build and run with Docker:
```bash
docker-compose up --build
```

## Testing
```bash
pytest --cov=app tests/
```

## Deployment
1. Push changes to GitHub
2. GitHub Actions will automatically:
   - Run tests
   - Check code quality
   - Build Docker image
   - Deploy (if configured)

## Security Considerations
- Keep dependencies updated
- Use environment variables for sensitive data
- Implement rate limiting
- Enable CORS appropriately
- Use secure HTTP headers
- Regular security audits

## Contributing
1. Create a new branch
2. Make changes
3. Run tests and ensure pre-commit hooks pass
4. Submit pull request

## License
[MIT License](LICENSE)
