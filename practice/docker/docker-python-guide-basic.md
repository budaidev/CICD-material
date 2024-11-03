# Docker and Python - Practical Guide

## 1. Docker Overview

### What is Docker?
- Containerization platform
- Isolated execution of applications and dependencies
- Platform-independent environment

### Key Concepts
- **Image**: Snapshot of application and environment
- **Container**: Running instance of an image
- **Dockerfile**: Instructions for creating an image
- **Layer**: Image layers that build upon each other

## 2. Guided Practice: Dockerizing Python Code

### Simple Python Application
`app.py`:
```python
def greet(name):
    return f"Hello, {name}!"

if __name__ == "__main__":
    name = input("Enter your name: ")
    print(greet(name))
```

### Creating a Dockerfile
```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY app.py .

CMD ["python", "app.py"]
```

### Build and Run
```bash
# Build image
docker build -t my-python-app .

# Run container
docker run -it my-python-app
```

## 3. Docker Layers

### How Layers Work
- Each Dockerfile instruction creates a new layer
- Layers are reusable
- Caching speeds up builds

### Optimized Dockerfile
```dockerfile
FROM python:3.11-slim

WORKDIR /app

# Dependencies in separate layer
COPY requirements.txt .
RUN pip install -r requirements.txt

# Application code in separate layer
COPY . .

CMD ["python", "app.py"]
```

## 4. Docker Compose Overview

### What is Docker Compose?
- Management of multi-container applications
- YAML format configuration file
- Handling of services, networks, and volumes

### Simple Docker Compose File
```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "5000:5000"
```

## 5. Docker Compose Running

### Basic Commands
```bash
# Start services
docker-compose up

# Run in background
docker-compose up -d

# Stop services
docker-compose down

# View logs
docker-compose logs
```

## 6. Docker Compose Build

### Build Configuration
```yaml
version: '3.8'

services:
  app:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "5000:5000"
```

### Build Commands
```bash
# Run build
docker-compose build

# Build and start
docker-compose up --build
```

## 7. Using Environment Variables

### .env File
```env
APP_PORT=5000
DB_NAME=myapp
```

### Docker Compose with Environment Variables
```yaml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "${APP_PORT}:5000"
    environment:
      - DB_NAME=${DB_NAME}
```

## 8. Practice: Dockerizing a CLI Python Application

### CLI Application
`character_count.py`:
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

### Dockerfile for CLI Application
```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY cli.py .

ENTRYPOINT ["python", "character_count.py"]
```

### Running with Arguments
```bash
# Build
docker build -t my-cli-app .

# Run with argument
docker run my-cli-app John
```

### Docker Compose for CLI Application
```yaml
version: '3.8'

services:
  cli:
    build: .
    entrypoint: ["python", "cli.py"]
    command: ["John"]
```

## Common Commands Summary

```bash
# Docker
docker build -t name .          # Build image
docker run name                 # Run container
docker ps                       # List running containers
docker stop container-id        # Stop container

# Docker Compose
docker-compose up              # Start services
docker-compose down            # Stop services
docker-compose build           # Build images
docker-compose logs            # View logs
```

### Tips
- Always use specific tags for base images
- Keep images small by using slim variants
- Use `.dockerignore` to exclude unnecessary files
- Remember to expose required ports
- Use environment variables for configuration
