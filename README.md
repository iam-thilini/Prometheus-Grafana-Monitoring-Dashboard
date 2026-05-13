# Prometheus Grafana Monitoring Dashboard

A local real-time monitoring and alerting project built using **Prometheus**, **Grafana**, **Docker Compose**, and **PromQL**.

This project demonstrates how to configure Prometheus scrape jobs, collect metrics from monitored targets, visualize metrics using Grafana dashboards, and configure Grafana-managed alert rules for service availability, CPU usage, and API latency.

---

## Project Overview

The goal of this project is to simulate a real-world observability workflow using a local Prometheus and Grafana setup.

Prometheus is configured to scrape:

- Prometheus internal metrics
- External PromLabs demo application metrics

Grafana is used to build a monitoring dashboard for:

- Service health
- CPU usage
- API request rate
- Request rate by instance
- Top API endpoints
- Average API latency
- Prometheus ingestion rate

The project also includes Grafana-managed alerts for:

- Target downtime
- High CPU usage
- High API latency

---

## Architecture

```txt
PromLabs Demo Applications
        ↓
Local Prometheus
        ↓
Grafana Dashboard + Alerts
```

## Tech Stack
- Prometheus
- Grafana
- Docker Compose
- PromQL
- Webhook Contact Point

## Features
- Local Prometheus setup using Docker Compose
- Grafana dashboard connected to Prometheus
- Custom Prometheus scrape configuration
- Service target health monitoring
- CPU usage monitoring
- API request rate tracking
- Request rate by instance
- Top API endpoint analysis
- Average API latency monitoring
- Prometheus ingestion rate monitoring
- Grafana-managed alert rules
- Webhook-based alert notification testing

## Project Structure
```txt
monitoring-project/
├── docker-compose.yml
├── prometheus.yml
```

## Prometheus Configuration
Prometheus is configured with a 5s scrape interval and two scrape jobs:
1. prometheus — monitors Prometheus itself
2. promlabs-demo — monitors external PromLabs demo application targets

```
global:
  scrape_interval: 5s

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets:
          - localhost:9090

  - job_name: "promlabs-demo"
    static_configs:
      - targets:
          - demo.promlabs.com:10000
          - demo.promlabs.com:10001
          - demo.promlabs.com:10002
```

## Docker Compose Setup
```
services:
  prometheus:
    image: prom/prometheus
    container_name: local-prometheus
    ports:
      - "9090:9090"
    volumes:
      - ./prometheus.yml:/etc/prometheus/prometheus.yml
    command:
      - "--config.file=/etc/prometheus/prometheus.yml"

  grafana:
    image: grafana/grafana
    container_name: local-grafana
    ports:
      - "3000:3000"
    depends_on:
      - prometheus
```

## How to Run the Project
**1. Clone the repository**
```
git clone https://github.com/your-username/prometheus-grafana-monitoring-dashboard.git
cd prometheus-grafana-monitoring-dashboard
```
**2. Start Prometheus and Grafana**
```
docker compose up -d
```
**3. Check running containers**
```
docker ps
```
You should see:
```
local-prometheus
local-grafana
```
**4. Open Prometheus**
```
http://localhost:9090
```
**5. Open Prometheus targets page**
```
http://localhost:9090/targets
```
All targets should show as `UP`

**6. Open Grafana**
```
http://localhost:3000
```
Default login:
```
Username: admin
Password: admin
```
**7. Add Prometheus data source in Grafana**
Use this Prometheus URL inside Grafana:
```
http://prometheus:9090
```
This works because Grafana and Prometheus are running inside the same Docker Compose network.

## Dashboard Panels
| Panel                     | Visualization | Description                                                       |
| ------------------------- | ------------- | ----------------------------------------------------------------- |
| Target Health             | Table         | Displays the current availability status of each monitored target |
| CPU Usage                 | Time series   | Shows CPU usage over time for each demo application instance      |
| Request Rate              | Time series   | Displays total API request rate across all demo services          |
| Request Rate by Instance  | Time series   | Shows API traffic separately for each application instance        |
| Top API Endpoints         | Bar chart     | Displays the top 3 busiest API endpoints by request rate          |
| Average API Latency       | Time series   | Shows average request duration for each API endpoint              |
| Prometheus Ingestion Rate | Time series   | Shows how many metric samples Prometheus is ingesting per second  |

## Dashboard Queries
**1. Target Health**
```
up
```
Description:
```txt
Displays the current availability status of each monitored target. A status of UP means Prometheus is successfully scraping metrics from that service.
```
**2. CPU Usage**
```
sum by(instance) (
  rate(demo_cpu_usage_seconds_total{mode!="idle"}[5m])
)
/
on(instance) group_left()
demo_num_cpus
```
Description:
```txt
Shows CPU usage over time for each demo application instance, helping identify load patterns and performance changes across services.
```
**3. Request Rate**
```
sum(rate(demo_api_request_duration_seconds_count[5m]))
```
Description:
```txt
Displays the total API request rate across all demo application services, helping monitor traffic volume and system activity over time.
```
**4. Request Rate by Instance**
```
sum by(instance) (
  rate(demo_api_request_duration_seconds_count[5m])
)
```
Description:
```txt
Shows API request traffic separately for each application instance, helping identify load distribution and whether one service instance is receiving more traffic than others.
```
**5. Top API Endpoints**
```
label_join(
  topk(3, sum by(path, method) (
    rate(demo_api_request_duration_seconds_count[5m])
  )),
  "endpoint",
  " ",
  "method",
  "path"
)
```
Description:
```txt
Displays the top 3 busiest API endpoints by request rate, helping identify which routes receive the most traffic.
```
**6. Average API Latency**
```
sum by(path, method) (
  rate(demo_api_request_duration_seconds_sum[5m])
)
/
sum by(path, method) (
  rate(demo_api_request_duration_seconds_count[5m])
)
```
Description:
```txt
Shows the average request duration for each API endpoint, helping identify slow routes and potential performance bottlenecks.
```
**7. Prometheus Ingestion Rate**
```
sum(rate(prometheus_tsdb_head_samples_appended_total[1m]))
```
Description:
```txt
Shows how many metric samples Prometheus is ingesting per second, helping monitor Prometheus storage and data collection activity.
```
## Grafana Alert Rules
This project includes three Grafana-managed alert rules.
| Alert                  | Purpose                                                         |
| ---------------------- | --------------------------------------------------------------- |
| Target Down Alert      | Fires when one or more monitored targets are unavailable        |
| High CPU Usage Alert   | Fires when CPU usage exceeds the configured threshold           |
| High API Latency Alert | Fires when average API latency exceeds the configured threshold |

## Alert 1: Target Down Alert
**Query**








































