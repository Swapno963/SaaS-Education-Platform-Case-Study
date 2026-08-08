# SaaS Education Platform Infrastructure



## Overview
An education institution management system designed to streamline the day-to-day operations of multi branch schools and educational institutions. The platform provides features for managing students, branches, classes, assessments, fees, notifications, and other administrative workflows.


### Application Stack

* Backend: Go
* Frontend: Next.js
* Database: PostgreSQL (self-hosted on EC2)
* Containerization: Docker
* Infrastructure: Terraform
* Cloud Provider: AWS
* CI/CD: GitHub Actions
* Monitoring: Prometheus + Grafana (Upcoming)
* Logging: Loki (Upcoming)
* Tracing: OpenTelemetry (Upcoming)




### My Contribution

* Designed and implemented backend APIs using Go and Gin
* Designed PostgreSQL schemas and optimized database queries
* Implemented authentication and authorization middleware
* Built asynchronous background processing using Redis and Asynq
* Containerized backend services with Docker
* Provisioned AWS infrastructure using Terraform
* Configured VPC, subnets, security groups and EC2
* Built CI/CD pipelines with GitHub Actions
* Published Docker images to AWS ECR
* Implemented automated testing, linting, vulnerability scanning and deployment
* Implemented production deployment and health-check mechanisms

---

## Architecture

```text
                    Internet
                       |
                    Nginx
                       |
             +---------+---------+
             |                   |
        Next.js              Go API
                                 |
                +----------------+----------------+
                |                |                |
            PostgreSQL         Redis            MinIO
                                 |
                              Asynq
                               Worker
                                 |
                              Jobs
```

---

## CI/CD Pipeline


```text
Developer Push
      │
      ▼
GitHub Repository
      │
      ├─────────────────────────────────────────────┐
      │                                             │
      ▼                                             ▼
Non-Main Branch                              Main Branch
      │                                             │
      ▼                                             ▼
GitHub Actions                                GitHub Actions
      │                                             │
      ├── Format                                  ├── Format
      ├── Vet                                     ├── Vet
      ├── Lint                                    ├── Lint
      ├── Tests                                   ├── Tests
      ├── Build                                   ├── Build
      └── Test Environment                        ├── Security Scan
                                                  ├── Docker Build
                                                  └── Publish Image
                                                         │
                                                         ▼
                                                    AWS ECR
                                                         │
                                                   SHA-based Tag
                                                         │
                                                         ▼
                                               ┌─────────────────┐
                                               │ Manual Deploy   │
                                               │ Run Workflow    │
                                               └────────┬────────┘
                                                        │
                                  ┌─────────────────────┴─────────────────────┐
                                  │                                           │
                                  ▼                                           ▼
                         Frontend Image Tag                           Backend Image Tag
                                  │                                           │
                                  └─────────────────────┬─────────────────────┘
                                                        ▼
                                                     AWS EC2
                                                        │
                                                        ▼
                                               Docker Compose
                                                        │
                                  ┌─────────────────────┼─────────────────────┐
                                  ▼                     ▼                     ▼
                              Frontend                API                  Workers
```






---

## Database

### PostgreSQL (Self-Hosted on EC2)

* Persistent Docker volume storage
* Automated backups via cron jobs
* Manual restore procedures
* Internal network access only



## Security

### Secrets Management

Managed using:

* GitHub Actions Secrets
* EC2 environment variables (.env files)


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

* Versioned S3 buckets 




## Live Demo

A live demo environment is available to showcase the system and demonstrate different user roles.

**Live Demo:** [`54.179.197.223`]

### Demo Accounts

| Role         | Description                       |                 Email                  | Password   |
| ------------ | --------------------------------- | -------------------------------------- | ---------- |
| Branch Admin | Manages a specific branch         | `demo.branch.admin@shaplamodel.edu.bd` | `12345678` |
| Teacher      | Accesses teacher-related features | `demo.teacher001@shaplamodel.edu.bd`   | `12345678` |
| Student      | Accesses student-related features | `demo.c6.student01@shaplamodel.edu.bd` | `12345678` |

> These accounts are provided for demonstration purposes only. Please do not modify or delete shared demo data.

