# School Management SaaS — case study

Multi-branch school operations platform. This repo is the public architecture and operations record. Application source is private.

**Status:** production (client product at Meraki). Observability beyond health checks is not shipped.

---

## What it does

Day-to-day operations for multi-branch schools: students, classes, assessments, fees, notifications, and staff workflows.

The request path stays in a **Go / Gin REST API** on **PostgreSQL**. Email, SMS, and notifications run through **Redis and Asynq**. An **outbox pattern** keeps those side effects aligned with database writes. **Role-based access control** sits in the API.

---

## What I built

- Go/Gin APIs, PostgreSQL schema, and query work
- Authentication and authorization middleware (RBAC)
- Redis/Asynq workers and outbox-backed side effects
- Docker packaging of backend services
- Terraform for VPC, subnets, security groups, and EC2
- GitHub Actions: format, vet, lint, tests, vulnerability scan, image publish to ECR
- Production deploy and health checks
- Daily PostgreSQL dumps to versioned S3, with a documented restore path

A live demo exists for role walkthroughs. Credentials are not stored in this repository.

**One-pager:** [school-management-saas.pdf](docs/school-management-saas.pdf)

---

## Stack

| Layer    | Choice                | Notes                              |
| -------- | --------------------- | ---------------------------------- |
| Backend  | Go, Gin               | REST API, authz, workers           |
| Frontend | Next.js               | Admin and student UI               |
| Data     | PostgreSQL            | Self-hosted on EC2                 |
| Jobs     | Redis, Asynq          | Email, SMS, notifications          |
| Files    | S3 / MinIO            | Uploads and DB dumps               |
| Edge     | Nginx                 | Reverse proxy                      |
| Runtime  | Docker Compose on EC2 | Frontend, API, workers             |
| Infra    | AWS, Terraform        | VPC, subnets, security groups, EC2 |
| CI/CD    | GitHub Actions, ECR   | Test, scan, build, publish         |

**Not shipped:** Prometheus, Grafana, Loki, OpenTelemetry.

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
```

---

## CI/CD

```text
Developer Push
      │
      ▼
GitHub Repository
      │
      ├──────── non-main ─────────┐
      │                           │
      ▼                           ▼
  Format, vet, lint,          Format, vet, lint,
  tests, build                tests, build
  test environment            security scan
                              Docker build
                              Publish to ECR (SHA tag)
                                      │
                              Manual deploy workflow
                                      │
                                      ▼
                              EC2 / Docker Compose
                              Frontend · API · Workers
```

---

## Operations

**Database.** PostgreSQL runs in Docker on EC2 with a persistent volume, internal-network access only, scheduled `pg_dump` jobs, and a manual restore procedure.

**Secrets.** GitHub Actions secrets plus EC2 environment files. IAM is least-privilege, with separate roles for CI/CD and infrastructure.

**Containers.** Non-root, minimal base images, image vulnerability scanning.

More detail: [infra.md](infra.md) and [Docs/architecture-decisions](Docs/architecture-decisions).
