class: inverse, center, middle

# Python Microservices Architektúra

---

class: inverse, center, middle

# Mi a Microservice Architektúra?

---

## Microservice alapelvek

* Független, önálló szolgáltatások
* Decentralizált adatkezelés
* Külön deployment egységek
* Skálázhatóság
* Fault isolation
* Technology stack freedom
* Smart endpoints, dumb pipes
* Domain Driven Design (DDD)

---

## Monolith vs. Microservices

### Monolith:
* Egységes kódbázis
* Egyszerűbb fejlesztés
* Nehezebb skálázhatóság
* Technológiai kötöttség

### Microservices:
* Elosztott rendszer
* Komplex infrastruktúra
* Könnyebb skálázhatóság
* Technológiai szabadság

---

## DevOps kihívások

### Infrastruktúra:
* Service discovery
* Load balancing
* Health monitoring
* Logging centralization
* Distributed tracing
* Configuration management

### Deployment:
* CI/CD pipelines
* Container orchestration
* Blue/Green deployment
* Canary releases

---

## Konfiguráció kezelés

### Python környezeti változók:
```python
from decouple import config

DATABASE_URL = config('DATABASE_URL')
REDIS_HOST = config('REDIS_HOST', default='localhost')
DEBUG = config('DEBUG', default=False, cast=bool)
```

### Konfig fájl kezelés:
```python
import yaml

def load_config():
    with open('config.yml') as f:
        return yaml.safe_load(f)

config = load_config()
```

---

## Message Queue megoldások

### RabbitMQ példa:
```python
import pika

connection = pika.BlockingConnection(
    pika.ConnectionParameters('localhost'))
channel = connection.channel()

channel.queue_declare(queue='hello')

def callback(ch, method, properties, body):
    print(f" [x] Received {body}")

channel.basic_consume(
    queue='hello', 
    on_message_callback=callback, 
    auto_ack=True)

print(' [*] Waiting for messages...')
channel.start_consuming()
```

---

## Kafka integráció

### Producer:
```python
from kafka import KafkaProducer

producer = KafkaProducer(
    bootstrap_servers=['localhost:9092'])

producer.send('topic_name', b'message')
producer.flush()
```

### Consumer:
```python
from kafka import KafkaConsumer

consumer = KafkaConsumer(
    'topic_name',
    bootstrap_servers=['localhost:9092'],
    auto_offset_reset='earliest',
    group_id='my-group')

for message in consumer:
    print(message.value)
```

---

## Service Discovery

### Consul integráció:
```python
import consul

c = consul.Consul()

# Service regisztráció
c.agent.service.register(
    'web',
    service_id='web-1',
    port=8080,
    tags=['python'])

# Service discovery
services = c.agent.services()
```

---

## Distributed Tracing

### OpenTelemetry példa:
```python
from opentelemetry import trace
from opentelemetry.instrumentation.flask import FlaskInstrumentor
from opentelemetry.instrumentation.requests import RequestsInstrumentor

app = Flask(__name__)
FlaskInstrumentor().instrument_app(app)
RequestsInstrumentor().instrument()

tracer = trace.get_tracer(__name__)

@app.route('/')
def hello():
    with tracer.start_as_current_span("hello") as span:
        span.set_attribute("operation", "greeting")
        return "Hello World!"
```

---

## Példa Microservice

### User Service:
```python
from flask import Flask, jsonify
from flask_sqlalchemy import SQLAlchemy

app = Flask(__name__)
db = SQLAlchemy(app)

class User(db.Model):
    id = db.Column(db.Integer, primary_key=True)
    username = db.Column(db.String(80), unique=True)
    email = db.Column(db.String(120), unique=True)

@app.route('/users/<id>')
def get_user(id):
    user = User.query.get_or_404(id)
    return jsonify({
        'id': user.id,
        'username': user.username,
        'email': user.email
    })
```

---

## Inter-service Communication

### REST API hívás:
```python
import requests

def get_user_orders(user_id):
    response = requests.get(
        f'http://order-service/orders/{user_id}'
    )
    return response.json()
```

### gRPC hívás:
```python
import grpc
import user_pb2
import user_pb2_grpc

channel = grpc.insecure_channel('localhost:50051')
stub = user_pb2_grpc.UserServiceStub(channel)

request = user_pb2.GetUserRequest(id=1)
response = stub.GetUser(request)
```

---

## Circuit Breaker Pattern

```python
from circuitbreaker import circuit

@circuit(failure_threshold=5, recovery_timeout=60)
def call_external_service():
    response = requests.get('http://external-api')
    return response.json()

try:
    result = call_external_service()
except Exception as e:
    # Handle failure
    pass
```

---

## Monitoring és Metrics

### Prometheus metrikák:
```python
from prometheus_client import Counter, Histogram
import prometheus_client

REQUEST_COUNT = Counter(
    'http_requests_total',
    'Total HTTP requests',
    ['method', 'endpoint']
)

REQUEST_LATENCY = Histogram(
    'http_request_duration_seconds',
    'HTTP request latency'
)

@app.route('/metrics')
def metrics():
    return prometheus_client.generate_latest()
```

---

## Best Practices

* Service boundary definition
* API versioning
* Error handling
* Authentication/Authorization
* Data consistency
* Monitoring és logging
* Testing strategies
* Documentation
* Security considerations
* Performance optimization

---

class: inverse, center, middle

# Kérdések?

