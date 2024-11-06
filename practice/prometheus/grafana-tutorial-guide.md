# Setting Up and Using Grafana and Prometheus

## Initial Setup

### Prerequisites
- Docker installed and running
- Git installed
- Internet connection for downloading resources

### Setup Steps
1. Clone the tutorial repository:
```bash
git clone https://github.com/grafana/tutorial-environment.git
```

2. Navigate to project directory:
```bash
cd tutorial-environment
```

3. Verify Docker is running:
```bash
docker ps
```

4. Start the sample application:
```bash
docker-compose up -d
```

5. Verify all services are running:
```bash
docker-compose ps
```

## Grafana Interface

### Access
- URL: http://localhost:3000
- Default credentials (for local installations):
  - Username: admin
  - Password: admin

## Exploring Metrics

### Using Grafana Explore
1. Navigate to Explore in the sidebar
2. Select Prometheus as the data source
3. Enter PromQL queries:
   ```
   # Basic query
   tns_request_duration_seconds_count

   # Rate query
   rate(tns_request_duration_seconds_count[5m])

   # Grouped by route
   sum(rate(tns_request_duration_seconds_count[5m])) by(route)
   ```

### Query Tips
- Use the 5s auto-refresh option for real-time updates
- Use the time picker to zoom into specific time ranges
- Try grouping by different labels (e.g., `status_code`)

## Creating a Dashboard

### Steps
1. Navigate to Dashboards
2. Click New > New Dashboard
3. Add visualization
4. Select Prometheus data source
5. Enter query:
   ```
   sum(rate(tns_request_duration_seconds_count[5m])) by(route)
   ```
6. Set panel title
7. Click Apply
8. Save dashboard with a name

### Dashboard Features
- Panels can display different visualizations
- Each panel consists of a query and visualization
- Dashboards can be saved and shared
