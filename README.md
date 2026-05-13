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










































