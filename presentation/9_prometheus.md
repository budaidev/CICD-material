class: inverse, center, middle

# Prometheus Monitoring Alapok

---

class: inverse, center, middle

# Mi a Prometheus?

---

## Prometheus áttekintés

* Nyílt forráskódú monitoring és alerting rendszer
* Time series adatbázis
* Pull-based architektúra
* Service discovery
* Erős query nyelv (PromQL)
* Vizualizációs lehetőségek
* Integrált alerting

---

## Prometheus architektúra

* Főbb komponensek:
  * Prometheus server (scraping, storage)
  * Exporters
  * Alertmanager
  * Push Gateway
  * Web UI

<img src="/api/placeholder/800/400" alt="Prometheus Architecture">

---

## Prometheus telepítés

* Kubernetes-en Helm chart-tal:
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm install prometheus prometheus-community/prometheus
```

* Docker-rel:
```bash
docker run -p 9090:9090 prom/prometheus
```

* Konfiguráció ellenőrzése:
```yaml
global:
  scrape_interval: 15s
scrape_configs:
  - job_name: 'prometheus'
    static_configs:
      - targets: ['localhost:9090']
```

---

## Prometheus UI

* Főbb funkciók:
  * Expression browser
  * Graph view
  * Targets page
  * Service discovery
  * Configuration
  * Rules

* Elérés: http://localhost:9090

---

## Exporterek

### Node Exporter (Linux)

```bash
docker run -d \
  --name node_exporter \
  -p 9100:9100 \
  prom/node-exporter
```

### Windows Exporter

* Metrikák:
  * CPU, memória használat
  * Disk I/O
  * Network statistics
  * Windows service status

---

## PromQL alapok

### Adattípusok

* Instant vector
* Range vector
* Scalar
* String

### Példa lekérdezések:

```promql
# Instant vector
http_requests_total

# Range vector
http_requests_total[5m]

# Aggregáció
sum(http_requests_total)
```

---

## PromQL Selectorok

### Label matchers:

```promql
# Equality
http_requests_total{environment="production"}

# Regex match
http_requests_total{job=~".*backend.*"}

# Multiple conditions
http_requests_total{status!="500",method="GET"}
```

---

## PromQL Operátorok

### Aritmetikai operátorok:
* +, -, *, /, %, ^

### Összehasonlító operátorok:
* ==, !=, >, <, >=, <=

### Logikai operátorok:
* and, or, unless

```promql
# Példa összetett query
sum(rate(http_requests_total{status="200"}[5m])) 
  / 
sum(rate(http_requests_total[5m]))
```

---

## Python alkalmazás instrumentálás

### Prometheus client library:

```python
from prometheus_client import Counter, Histogram
import prometheus_client

# Metrikák definiálása
REQUEST_COUNT = Counter(
    'http_requests_total',
    'Total HTTP requests',
    ['method', 'endpoint', 'status']
)

REQUEST_LATENCY = Histogram(
    'http_request_duration_seconds',
    'HTTP request latency',
    ['endpoint']
)
```

---

## Python middleware példa

```python
@app.middleware("http")
async def monitor_requests(request, call_next):
    start_time = time.time()
    
    response = await call_next(request)
    
    duration = time.time() - start_time
    REQUEST_COUNT.labels(
        method=request.method,
        endpoint=request.url.path,
        status=response.status_code
    ).inc()
    
    REQUEST_LATENCY.labels(
        endpoint=request.url.path
    ).observe(duration)
    
    return response
```

---

## Alerting beállítás

### Alert szabály definíció:

```yaml
groups:
- name: example
  rules:
  - alert: HighRequestLatency
    expr: http_request_duration_seconds > 0.5
    for: 5m
    labels:
      severity: warning
    annotations:
      summary: High request latency on {{ $labels.instance }}
      description: Request latency is above 500ms
```

---

## Alert Manager konfiguráció

```yaml
route:
  group_by: ['alertname']
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 1h
  receiver: 'email-notifications'

receivers:
- name: 'email-notifications'
  email_configs:
  - to: 'team@example.com'
    from: 'prometheus@example.com'
    smarthost: 'smtp.gmail.com:587'
    auth_username: 'prometheus@example.com'
    auth_password: 'password'
```

---

## Prometheus Best Practices

* Metrika naming conventions
* Label használat optimalizálás
* Retention és storage beállítások
* Query optimalizálás
* High availability setup
* Backup és recovery
* Security considerations

---

## Összefoglalás

* Prometheus architektúra
* Metrika gyűjtés
* PromQL lekérdezések
* Python integráció
* Alerting rendszer
* Best practices és tippek

---

class: inverse, center, middle

# Kérdések?

