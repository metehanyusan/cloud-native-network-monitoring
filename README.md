# Cloud-Native Network Monitoring System

This project demonstrates a cloud-native monitoring infrastructure using Prometheus, Grafana, and Node Exporter.

## Technologies Used

- Prometheus
- Grafana
- Node Exporter
- Docker
- Docker Compose
- WSL Ubuntu

## Project Architecture

Node Exporter → Prometheus → Grafana → Alert System

## Features

- Real-time system monitoring
- CPU, RAM, Disk, and Network monitoring
- Grafana dashboard visualization
- Prometheus telemetry collection
- Alert mechanism for high CPU usage
- Docker-based deployment

## Alert Scenario

A CPU load test was performed using:

```bash
yes > /dev/null &
