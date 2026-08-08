# SaaS Education Platform Infrastructure

Production-ready infrastructure automation for a SaaS-based School Management System.

---

## Overview

This repository contains Infrastructure as Code (IaC), deployment automation, observability configuration, and operational tooling for a cloud-native education platform.

### Application Stack

* Backend: Go
* Frontend: Next.js
* Database: PostgreSQL (self-hosted on EC2)
* Containerization: Docker
* Infrastructure: Terraform
* Cloud Provider: AWS
* CI/CD: GitHub Actions
* Monitoring: Prometheus + Grafana
* Logging: Loki
* Tracing: OpenTelemetry

---

## Architecture

```text
Users
  │
  ▼
Route53
  │
  ▼
Application Load Balancer
  │
  ▼
EC2 (Docker Host)
 ├── Frontend (Next.js)
 ├── Backend API (Go)
 ├── PostgreSQL Database
 └── Background Workers
```

---

## Repository Structure

```text
infra/
├── terraform/
│   ├── modules/
│   │   ├── vpc/
│   │   ├── ec2/
│   │   ├── alb/
│   │   ├── security-groups/
│   │   └── monitoring/
│   │
│   └── environments/
│       ├── dev/
│       ├── staging/
│       └── prod/
│
├── docker/
│   ├── backend/
│   ├── frontend/
│   ├── postgres/
│   └── docker-compose.yml
│
├── github-actions/
│
├── scripts/
│
└── docs/
```

---

# Infrastructure Components

## Networking

* Custom VPC
* Public and Private Subnets
* Internet Gateway
* NAT Gateway
* Route Tables
* Security Groups

### Goals

* Isolated workloads
* Controlled inbound access
* Secure database exposure via private networking

---

## Compute Layer (EC2)

### Features

* EC2-based application hosting
* Docker runtime
* Docker Compose orchestration
* Rolling updates via CI/CD

### Workloads

| Service  | Description         |
| -------- | ------------------- |
| frontend | Next.js Application |
| api      | Go REST API         |
| postgres | PostgreSQL Database |

---

## Database

### PostgreSQL (Self-Hosted on EC2)

Features:

* Persistent Docker volume storage
* Automated backups via cron jobs
* Manual restore procedures
* Internal network access only

### Trade-offs:

* Lower cost
* Higher operational responsibility
* No managed HA or PITR by default

---

## CI/CD Pipeline

### Deployment Flow

```text
Developer Push
        │
        ▼
GitHub Actions
        │
        ├── Run Tests
        ├── Lint
        ├── Build Docker Image
        ├── Security Scan
        ├── Push To Registry
        └── SSH Deploy to EC2 (Docker Compose)
```

### Objectives

* Automated deployments
* Consistent environment parity
* Fast rollback via previous image tags

---

## Monitoring

### Prometheus

Collected Metrics:

* CPU Usage
* Memory Usage
* Container Health
* Request Count
* Request Latency
* Error Rate

### Grafana Dashboards

#### Application Dashboard

* Requests Per Second
* P95 Latency
* Error Percentage
* Active Users

#### Infrastructure Dashboard

* EC2 CPU
* Memory
* Disk usage
* Network traffic

---

## Logging

### Centralized Logging

Components:

* Loki
* Promtail

Log Types:

* Application Logs
* System Logs
* Access Logs
* Error Logs

---

## Distributed Tracing

### OpenTelemetry

Trace Flow:

```text
Browser
  │
  ▼
Frontend
  │
  ▼
Backend API
  │
  ▼
PostgreSQL
```

Benefits:

* End-to-end request visibility
* Latency bottleneck identification
* Faster incident debugging

---

## Security

### Secrets Management

Managed using:

* GitHub Actions Secrets
* EC2 environment variables (.env files)

Examples:

* Database credentials
* JWT secrets
* API keys

### Container Security

* Non-root containers
* Minimal base images
* Image vulnerability scanning

### IAM

* Least privilege access
* Separate roles for CI/CD and infrastructure
* Environment isolation via Terraform

---

## Disaster Recovery

### Backup Strategy

Database:

* Daily PostgreSQL dumps via cron job
* Manual restore procedures documented

Storage:

* Versioned S3 buckets (if used)

### Recovery Objectives

| Metric | Target  |
| ------ | ------- |
| RPO    | 1 hour  |
| RTO    | 2 hours |

---

## Environments

### Development

* Local Docker Compose setup
* Feature development and testing

### Staging

* EC2-based staging environment
* Pre-production validation

### Production

* Single or multi-instance EC2 setup
* Load balancer fronted
* Production traffic handling

---

## Operational Responsibilities

This infrastructure supports:

* Infrastructure provisioning via Terraform
* Automated CI/CD deployments
* Monitoring and alerting
* Log aggregation and debugging
* Backup and restore procedures
* Capacity planning

---

## Future Improvements

* ECS migration (optional scaling improvement)
* GitOps using ArgoCD
* Multi-node database setup
* Automated scaling policies
* Cost optimization dashboards
* Service mesh adoption (if needed)

---

## Skills Demonstrated

* Go backend operations in production
* AWS EC2-based infrastructure design
* Terraform infrastructure automation
* Docker and Docker Compose orchestration
* PostgreSQL self-managed operations
* CI/CD with GitHub Actions
* Observability with Prometheus + Grafana
* Logging and tracing systems
* Production incident handling
* Cloud security fundamentals
