
# School Management System (SaaS) – Infrastructure & Scaling Guide

## Overview

This document describes the initial architecture, deployment approach, and future scaling strategy for the School Management System.

The system consists of:

- Frontend: Next.js
- Backend: Go (Golang API)
- Database: PostgreSQL
- File Storage: AWS S3
- Infrastructure: AWS EC2 (initial phase)

---

## 1. Initial Architecture (MVP Stage)

The first version is designed for fast delivery and minimal infrastructure complexity.

### Architecture Diagram

```

Internet
|
EC2 (t3.large)
|
-

| Next.js (Node.js Server)                     |
| Go API                                       |
| PostgreSQL                                   |
| Nginx                                        |
| Backup Agent (cron job → S3)                |
-----------------------------------------------


       |
       |----------------------|
       |                      |
     S3 Bucket (Files)    S3 Bucket (DB Backups)
```



---

## 2. Responsibilities of Each Component

### EC2 Instance
- Hosts all application services
- Runs:
  - Next.js frontend
  - Go backend API
  - PostgreSQL database
  - Nginx reverse proxy
- Acts as a single deployment unit

### PostgreSQL
- Stores all system data:
  - Students
  - Teachers
  - Attendance
  - Exams
  - Reports

### Nginx
- Reverse proxy for:
  - Next.js frontend
  - Go API routing

### S3 (File Storage)
- Stores all uploaded files:
  - Student images
  - Documents
  - Reports
  - Assignments

### S3 (Backups)
- Stores automated PostgreSQL backups
- Created via cron-based backup agent

### Backup Agent
- Runs scheduled jobs:
  - `pg_dump` database export
  - Uploads compressed backup to S3
- Ensures disaster recovery capability

---

## 3. Deployment Strategy (MVP)

### Manual Deployment Flow

1. SSH into EC2
2. Pull latest code from repository
3. Build Go backend
4. Build Next.js frontend
5. Restart services
6. Verify health endpoints

---

## 4. Key Design Principles (MVP Phase)

### 4.1 Stateless Application Design
- No critical data stored on EC2 disk
- All files stored in S3
- Enables future horizontal scaling

### 4.2 Externalized Storage
- Files → S3
- Backups → S3
- Database → PostgreSQL (initially local)

### 4.3 Simplicity First
- No Kubernetes or ECS in MVP phase
- Single machine reduces operational overhead

---

## 5. Reliability & Recovery Strategy

### Backup Strategy
- Daily automated PostgreSQL backups
- Stored in S3
- Retention policy applied (e.g., 30–90 days)

### Recovery Plan
In case of failure:

1. Launch new EC2 instance
2. Restore PostgreSQL from latest backup in S3
3. Deploy application from repository
4. Reconnect S3-based file storage

---

## 6. Known Limitations (MVP Design)

- Single EC2 = single point of failure
- No horizontal scaling
- Database and application share same resources
- Manual deployment process
- Limited high availability

---

## 7. Correct Scaling Path (Future Evolution)

This is the intended migration strategy as the system grows.

---

### Phase 1: Current (MVP)

```

Single EC2 + S3 + Local PostgreSQL

```

✔ Fast development  
✔ Low cost  
❌ No high availability  
❌ No horizontal scaling  

---

### Phase 2: Database Decoupling (Critical First Upgrade)

```

EC2 (App Server)
|
RDS PostgreSQL
|
S3 (Files + Backups)

```

Improvements:
- Managed database
- Automated backups
- Improved reliability
- Reduced operational risk

---

### Phase 3: Application Scaling Layer

```

ALB (Load Balancer)
|
EC2 (App 1)   EC2 (App 2)
|
RDS PostgreSQL
|
S3

```

Improvements:
- Horizontal scaling
- High availability
- Rolling deployments possible

---

### Phase 4: Containerization (Optional)

```

ALB
|
ECS Services (Go API + Next.js)
|
RDS + S3

```

Improvements:
- Better deployment control
- Auto scaling
- Service isolation

---

### Phase 5: Advanced Infrastructure (Large Scale)

```

EKS / Microservices Architecture
Redis (Caching Layer)
Queue System (SQS / Kafka)
Observability Stack (Logs + Metrics + Tracing)
Multi-region setup

```

---

## 8. Key Engineering Guidelines

### Do Not:
- Store files locally on EC2
- Bind system design to single instance assumptions
- Delay backup testing
- Ignore DB connection scaling limits

### Always:
- Keep services stateless
- Externalize storage early (S3)
- Treat backups as production-critical, not optional
- Plan migration paths before scaling becomes urgent

---

## 9. Final Notes

This architecture prioritizes:
- Speed of delivery
- Simple operational model
- Early-stage cost efficiency

Future phases focus on:
- Reliability
- Scalability
- High availability
- Operational maturity
