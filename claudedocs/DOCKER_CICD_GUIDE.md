# Docker & CI/CD in Permitto: Complete Guide

**Generated:** January 17, 2026
**Context:** Brainstorm session on architecture decisions
**Related Documents:** [PRD v2](./PRD_permitto_v2.md) | [WORKFLOW v2](../WORKFLOW_v2.md)

---

## Table of Contents

1. [Part 1: Docker's Role](#part-1-dockers-role)
   - [What Problem Does Docker Solve?](#what-problem-does-docker-solve)
   - [Docker Core Concepts](#docker-core-concepts)
   - [Docker in Development](#docker-in-development-docker-composeyml)
   - [How Containers Communicate](#how-containers-communicate)
   - [Dockerfile: Multi-Stage Build](#dockerfile-multi-stage-build-production)
   - [Development vs Production Docker](#development-vs-production-docker)
2. [Part 2: CI/CD Approach](#part-2-cicd-approach)
   - [What is CI/CD?](#what-is-cicd)
   - [Pipeline Stages Explained](#pipeline-stages-explained)
   - [Branch Strategy & Environments](#branch-strategy--environments)
   - [GitHub Actions Workflows](#github-actions-workflows)
   - [Railway Deployment Architecture](#railway-deployment-architecture)
   - [Database Migration Strategy](#database-migration-strategy)
   - [Rollback Strategy](#rollback-strategy)
   - [Secrets Management](#secrets-management)
   - [Complete Developer Workflow](#complete-developer-workflow)
3. [Summary Tables](#summary-tables)

---

## Part 1: Docker's Role

### What Problem Does Docker Solve?

**The Classic Problem: "Works on My Machine"**

```
Developer A (macOS):     Developer B (Windows):     Production (Linux):
├── Node 20.10.0         ├── Node 18.17.0           ├── Node 20.9.0
├── PostgreSQL 15        ├── PostgreSQL 14          ├── PostgreSQL 15
├── Redis 7.2            ├── Redis not installed    ├── Redis 7.0
└── Works ✅             └── Crashes ❌             └── Different behavior ⚠️
```

**Docker's Solution: Containerization**

```
┌─────────────────────────────────────────────────────────────┐
│                     DOCKER CONTAINER                        │
│  ┌───────────────────────────────────────────────────────┐  │
│  │  Exact same environment everywhere:                   │  │
│  │  • Node 20.10.0                                       │  │
│  │  • All dependencies at specific versions              │  │
│  │  • Same OS (Linux Alpine)                             │  │
│  │  • Same file paths                                    │  │
│  └───────────────────────────────────────────────────────┘  │
│                                                             │
│  Runs identically on: macOS | Windows | Linux | Railway    │
└─────────────────────────────────────────────────────────────┘
```

---

### Docker Core Concepts

| Concept | What It Is | Analogy |
|---------|-----------|---------|
| **Image** | Blueprint defining environment + code | Recipe for a cake |
| **Container** | Running instance of an image | The actual cake |
| **Dockerfile** | Instructions to build an image | The recipe document |
| **Volume** | Persistent storage outside container | Refrigerator (survives restart) |
| **Network** | Communication channel between containers | Phone line between services |
| **Docker Compose** | Tool to run multiple containers together | Kitchen manager orchestrating cooks |

---

### Docker in Development (docker-compose.yml)

```yaml
# docker-compose.yml - Development Environment
version: '3.8'

services:
  # ═══════════════════════════════════════════════════════════
  # DATABASE: PostgreSQL
  # ═══════════════════════════════════════════════════════════
  db:
    image: postgres:15-alpine        # Pre-built official image
    container_name: permitto-db
    environment:
      POSTGRES_USER: permitto
      POSTGRES_PASSWORD: permitto_dev
      POSTGRES_DB: permitto
    ports:
      - "5432:5432"                   # Host:Container port mapping
    volumes:
      - postgres_data:/var/lib/postgresql/data  # Data persists!
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U permitto"]
      interval: 5s
      timeout: 5s
      retries: 5

  # ═══════════════════════════════════════════════════════════
  # CACHE/QUEUE: Redis
  # ═══════════════════════════════════════════════════════════
  redis:
    image: redis:7-alpine
    container_name: permitto-redis
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

  # ═══════════════════════════════════════════════════════════
  # BACKEND: Express.js API
  # ═══════════════════════════════════════════════════════════
  api:
    build:
      context: .
      dockerfile: apps/api/Dockerfile
      target: development            # Use development stage
    container_name: permitto-api
    environment:
      NODE_ENV: development
      DATABASE_URL: postgresql://permitto:permitto_dev@db:5432/permitto
      REDIS_URL: redis://redis:6379  # Container name as hostname!
      JWT_SECRET: dev-secret-change-in-production
    ports:
      - "3001:3001"
    volumes:
      # Mount source code for hot reload
      - ./apps/api/src:/app/apps/api/src
      - ./packages:/app/packages
      # Don't mount node_modules (use container's version)
      - /app/node_modules
      - /app/apps/api/node_modules
    depends_on:
      db:
        condition: service_healthy   # Wait for DB to be ready
      redis:
        condition: service_started
    command: npm run dev             # Development command with hot reload

  # ═══════════════════════════════════════════════════════════
  # FRONTEND: Next.js Web
  # ═══════════════════════════════════════════════════════════
  web:
    build:
      context: .
      dockerfile: apps/web/Dockerfile
      target: development
    container_name: permitto-web
    environment:
      NEXT_PUBLIC_API_URL: http://localhost:3001/api/v1
    ports:
      - "3000:3000"
    volumes:
      - ./apps/web/src:/app/apps/web/src
      - ./packages:/app/packages
      - /app/node_modules
      - /app/apps/web/node_modules
      - /app/apps/web/.next
    depends_on:
      - api
    command: npm run dev

# Named volumes persist data between container restarts
volumes:
  postgres_data:
  redis_data:
```

---

### How Containers Communicate

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    DOCKER NETWORK (permitto_default)                    │
│                                                                         │
│  ┌─────────────┐      ┌─────────────┐      ┌─────────────────────────┐ │
│  │     db      │      │    redis    │      │          api            │ │
│  │  Port 5432  │◄────►│  Port 6379  │◄────►│       Port 3001         │ │
│  │             │      │             │      │                         │ │
│  │ hostname:   │      │ hostname:   │      │ DATABASE_URL=           │ │
│  │ "db"        │      │ "redis"     │      │ postgresql://...@db:5432│ │
│  └─────────────┘      └─────────────┘      └────────────┬────────────┘ │
│                                                         │              │
│                                            ┌────────────▼────────────┐ │
│                                            │          web            │ │
│                                            │       Port 3000         │ │
│                                            │                         │ │
│                                            │ API_URL=                │ │
│                                            │ http://api:3001         │ │
│                                            └─────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────────┘
                              │
                              │ Exposed to host machine
                              ▼
                    localhost:3000 (web)
                    localhost:3001 (api)
                    localhost:5432 (db)
                    localhost:6379 (redis)
```

**Key Insight:** Inside Docker network, containers use **service names** as hostnames (`db`, `redis`, `api`), not `localhost`.

---

### Dockerfile: Multi-Stage Build (Production)

```dockerfile
# apps/api/Dockerfile

# ═══════════════════════════════════════════════════════════════════════
# STAGE 1: Base - Common setup for all stages
# ═══════════════════════════════════════════════════════════════════════
FROM node:20-alpine AS base
WORKDIR /app

# Install pnpm
RUN corepack enable && corepack prepare pnpm@8 --activate

# ═══════════════════════════════════════════════════════════════════════
# STAGE 2: Dependencies - Install all dependencies
# ═══════════════════════════════════════════════════════════════════════
FROM base AS deps
COPY package.json pnpm-lock.yaml pnpm-workspace.yaml ./
COPY apps/api/package.json ./apps/api/
COPY packages/shared/package.json ./packages/shared/
RUN pnpm install --frozen-lockfile

# ═══════════════════════════════════════════════════════════════════════
# STAGE 3: Development - Used by docker-compose for local dev
# ═══════════════════════════════════════════════════════════════════════
FROM deps AS development
COPY . .
WORKDIR /app/apps/api
EXPOSE 3001
CMD ["pnpm", "dev"]

# ═══════════════════════════════════════════════════════════════════════
# STAGE 4: Builder - Compile TypeScript to JavaScript
# ═══════════════════════════════════════════════════════════════════════
FROM deps AS builder
COPY . .

# Generate Prisma client
WORKDIR /app/apps/api
RUN pnpm prisma generate

# Build TypeScript
RUN pnpm build

# ═══════════════════════════════════════════════════════════════════════
# STAGE 5: Production - Minimal image for deployment
# ═══════════════════════════════════════════════════════════════════════
FROM node:20-alpine AS production
WORKDIR /app

# Security: Run as non-root user
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 expressjs

# Copy only what's needed for production
COPY --from=builder /app/apps/api/dist ./dist
COPY --from=builder /app/apps/api/package.json ./
COPY --from=builder /app/apps/api/prisma ./prisma
COPY --from=builder /app/node_modules ./node_modules

USER expressjs

EXPOSE 3001

# Production command
CMD ["node", "dist/index.js"]
```

**Multi-Stage Benefits:**

| Stage | Contents | Size |
|-------|----------|------|
| deps | All node_modules | ~500MB |
| builder | Compiled code + devDependencies | ~600MB |
| **production** | Only runtime files | **~150MB** ✅ |

---

### Development vs Production Docker

| Aspect | Development | Production |
|--------|-------------|------------|
| **Base command** | `docker-compose up` | Railway builds & runs |
| **Source code** | Mounted as volume (hot reload) | Baked into image |
| **node_modules** | In container, not mounted | Installed at build time |
| **Dockerfile target** | `development` stage | `production` stage |
| **Environment vars** | In docker-compose.yml | In Railway dashboard |
| **Database** | Local PostgreSQL container | Railway managed PostgreSQL |
| **Rebuild needed** | Only for dependency changes | Every deployment |

---

## Part 2: CI/CD Approach

### What is CI/CD?

```
┌─────────────────────────────────────────────────────────────────────────┐
│                                                                         │
│  CI (Continuous Integration)          CD (Continuous Deployment)        │
│  ─────────────────────────────        ──────────────────────────        │
│                                                                         │
│  • Automatically test code            • Automatically deploy code       │
│  • On every push/PR                   • After CI passes                 │
│  • Catch bugs early                   • Get changes to users fast       │
│  • Ensure code quality                • Reduce manual deployment        │
│                                                                         │
│  ┌─────────┐    ┌─────────┐    ┌─────────┐    ┌─────────────────────┐  │
│  │  Lint   │───►│  Test   │───►│  Build  │───►│  Deploy to Staging  │  │
│  └─────────┘    └─────────┘    └─────────┘    └─────────────────────┘  │
│       │              │              │                   │               │
│       └──────────────┴──────────────┘                   │               │
│                      CI                                 CD              │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

---

### Pipeline Stages Explained

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        CI/CD PIPELINE STAGES                            │
└─────────────────────────────────────────────────────────────────────────┘

   STAGE 1          STAGE 2          STAGE 3          STAGE 4          STAGE 5
  ┌────────┐      ┌──────────┐      ┌────────┐      ┌────────┐      ┌────────┐
  │  LINT  │─────►│TYPECHECK │─────►│  TEST  │─────►│ BUILD  │─────►│ DEPLOY │
  └────────┘      └──────────┘      └────────┘      └────────┘      └────────┘
       │               │                │               │               │
       ▼               ▼                ▼               ▼               ▼
  ┌─────────────────────────────────────────────────────────────────────────┐
  │ ESLint        │ tsc --noEmit   │ Jest/Vitest  │ Docker build │ Railway  │
  │ checks code   │ verifies types │ runs tests   │ creates      │ deploys  │
  │ style         │ compile        │ unit + E2E   │ images       │ containers│
  └─────────────────────────────────────────────────────────────────────────┘
       │               │                │               │               │
       ▼               ▼                ▼               ▼               ▼
  "Is code         "Are types       "Does code      "Can we         "Is it
   formatted?"      correct?"        work?"          package it?"    live?"
```

---

### Branch Strategy & Environments

```
┌─────────────────────────────────────────────────────────────────────────┐
│                     BRANCH → ENVIRONMENT MAPPING                        │
└─────────────────────────────────────────────────────────────────────────┘

  Feature Branches              develop Branch              main Branch
  ────────────────              ──────────────              ───────────
        │                             │                          │
        │ PR Created                  │ Merge                    │ Merge
        ▼                             ▼                          ▼
  ┌───────────────┐            ┌───────────────┐          ┌───────────────┐
  │ CI Only       │            │ CI + CD       │          │ CI + CD       │
  │               │            │               │          │               │
  │ • Lint        │            │ • Lint        │          │ • Lint        │
  │ • Typecheck   │            │ • Typecheck   │          │ • Typecheck   │
  │ • Test        │            │ • Test        │          │ • Test        │
  │ • Build       │            │ • Build       │          │ • Build       │
  │               │            │ • Deploy ───────────┐    │ • Deploy ───────────┐
  └───────────────┘            └───────────────┘     │    └───────────────┘     │
                                                     │                          │
        No deployment                                ▼                          ▼
                                            ┌───────────────┐          ┌───────────────┐
                                            │   STAGING     │          │  PRODUCTION   │
                                            │               │          │               │
                                            │ Auto-deploy   │          │ Manual gate   │
                                            │ For testing   │          │ For users     │
                                            └───────────────┘          └───────────────┘
```

---

### GitHub Actions Workflows

#### CI Workflow (`.github/workflows/ci.yml`)

```yaml
# .github/workflows/ci.yml
name: CI Pipeline

# ═══════════════════════════════════════════════════════════════════════
# TRIGGERS: When does this run?
# ═══════════════════════════════════════════════════════════════════════
on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

# ═══════════════════════════════════════════════════════════════════════
# JOBS: What tasks to run
# ═══════════════════════════════════════════════════════════════════════
jobs:
  # ─────────────────────────────────────────────────────────────────────
  # JOB 1: Lint - Check code style
  # ─────────────────────────────────────────────────────────────────────
  lint:
    name: Lint
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Setup pnpm
        uses: pnpm/action-setup@v2
        with:
          version: 8

      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'pnpm'

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Run ESLint
        run: pnpm lint

  # ─────────────────────────────────────────────────────────────────────
  # JOB 2: Typecheck - Verify TypeScript
  # ─────────────────────────────────────────────────────────────────────
  typecheck:
    name: Typecheck
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v2
        with:
          version: 8
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'pnpm'
      - run: pnpm install --frozen-lockfile
      - run: pnpm typecheck

  # ─────────────────────────────────────────────────────────────────────
  # JOB 3: Test - Run unit and integration tests
  # ─────────────────────────────────────────────────────────────────────
  test:
    name: Test
    runs-on: ubuntu-latest

    # Spin up PostgreSQL for integration tests
    services:
      postgres:
        image: postgres:15-alpine
        env:
          POSTGRES_USER: test
          POSTGRES_PASSWORD: test
          POSTGRES_DB: permitto_test
        ports:
          - 5432:5432
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5

      redis:
        image: redis:7-alpine
        ports:
          - 6379:6379

    steps:
      - uses: actions/checkout@v4
      - uses: pnpm/action-setup@v2
        with:
          version: 8
      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: 'pnpm'

      - run: pnpm install --frozen-lockfile

      # Run database migrations for test DB
      - name: Setup test database
        run: pnpm --filter api prisma migrate deploy
        env:
          DATABASE_URL: postgresql://test:test@localhost:5432/permitto_test

      # Run tests
      - name: Run tests
        run: pnpm test
        env:
          DATABASE_URL: postgresql://test:test@localhost:5432/permitto_test
          REDIS_URL: redis://localhost:6379
          JWT_SECRET: test-secret

  # ─────────────────────────────────────────────────────────────────────
  # JOB 4: Build - Verify Docker images build successfully
  # ─────────────────────────────────────────────────────────────────────
  build:
    name: Build
    runs-on: ubuntu-latest
    needs: [lint, typecheck, test]  # Only run if previous jobs pass

    steps:
      - uses: actions/checkout@v4

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Build API image
        uses: docker/build-push-action@v5
        with:
          context: .
          file: apps/api/Dockerfile
          target: production
          push: false  # Don't push, just verify it builds
          cache-from: type=gha
          cache-to: type=gha,mode=max

      - name: Build Web image
        uses: docker/build-push-action@v5
        with:
          context: .
          file: apps/web/Dockerfile
          target: production
          push: false
          cache-from: type=gha
          cache-to: type=gha,mode=max
```

---

#### Deploy to Staging Workflow (`.github/workflows/deploy-staging.yml`)

```yaml
# .github/workflows/deploy-staging.yml
name: Deploy to Staging

on:
  push:
    branches: [develop]

jobs:
  deploy:
    name: Deploy to Staging
    runs-on: ubuntu-latest
    environment: staging  # GitHub environment for secrets/protection

    steps:
      - uses: actions/checkout@v4

      # ─────────────────────────────────────────────────────────────────
      # STEP 1: Install Railway CLI
      # ─────────────────────────────────────────────────────────────────
      - name: Install Railway CLI
        run: npm install -g @railway/cli

      # ─────────────────────────────────────────────────────────────────
      # STEP 2: Deploy API to Railway
      # ─────────────────────────────────────────────────────────────────
      - name: Deploy API
        run: |
          railway up \
            --service permitto-api-staging \
            --detach
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}

      # ─────────────────────────────────────────────────────────────────
      # STEP 3: Run Database Migrations
      # ─────────────────────────────────────────────────────────────────
      - name: Run Migrations
        run: |
          railway run \
            --service permitto-api-staging \
            -- pnpm prisma migrate deploy
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}

      # ─────────────────────────────────────────────────────────────────
      # STEP 4: Deploy Web to Railway
      # ─────────────────────────────────────────────────────────────────
      - name: Deploy Web
        run: |
          railway up \
            --service permitto-web-staging \
            --detach
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}

      # ─────────────────────────────────────────────────────────────────
      # STEP 5: Health Check
      # ─────────────────────────────────────────────────────────────────
      - name: Health Check
        run: |
          sleep 30  # Wait for deployment
          curl --fail https://api-staging.permitto.example.com/health || exit 1
          curl --fail https://staging.permitto.example.com || exit 1
```

---

#### Deploy to Production Workflow (`.github/workflows/deploy-production.yml`)

```yaml
# .github/workflows/deploy-production.yml
name: Deploy to Production

on:
  # Manual trigger only
  workflow_dispatch:
    inputs:
      confirm:
        description: 'Type "deploy" to confirm production deployment'
        required: true

jobs:
  validate:
    name: Validate Deployment
    runs-on: ubuntu-latest
    steps:
      - name: Check confirmation
        if: github.event.inputs.confirm != 'deploy'
        run: |
          echo "Deployment not confirmed. Type 'deploy' to proceed."
          exit 1

  deploy:
    name: Deploy to Production
    runs-on: ubuntu-latest
    needs: validate
    environment: production  # Requires manual approval in GitHub

    steps:
      - uses: actions/checkout@v4

      - name: Install Railway CLI
        run: npm install -g @railway/cli

      # ─────────────────────────────────────────────────────────────────
      # BACKUP: Create database backup before deployment
      # ─────────────────────────────────────────────────────────────────
      - name: Backup Database
        run: |
          railway run \
            --service permitto-db-production \
            -- pg_dump -Fc > backup-$(date +%Y%m%d-%H%M%S).dump
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN_PROD }}

      # ─────────────────────────────────────────────────────────────────
      # DEPLOY: Blue-green deployment
      # ─────────────────────────────────────────────────────────────────
      - name: Deploy API
        run: |
          railway up \
            --service permitto-api-production \
            --detach
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN_PROD }}

      - name: Run Migrations
        run: |
          railway run \
            --service permitto-api-production \
            -- pnpm prisma migrate deploy
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN_PROD }}

      - name: Deploy Web
        run: |
          railway up \
            --service permitto-web-production \
            --detach
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN_PROD }}

      # ─────────────────────────────────────────────────────────────────
      # VERIFY: Health check and monitoring
      # ─────────────────────────────────────────────────────────────────
      - name: Health Check
        run: |
          sleep 30
          curl --fail https://api.permitto.example.com/health || exit 1
          curl --fail https://permitto.example.com || exit 1

      - name: Notify Success
        run: |
          echo "Production deployment successful!"
          # Could add Slack/Discord notification here
```

---

### Railway Deployment Architecture

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        RAILWAY PROJECT: permitto                        │
└─────────────────────────────────────────────────────────────────────────┘

  ┌─────────────────────────────────────────────────────────────────────┐
  │                      STAGING ENVIRONMENT                            │
  │  ┌─────────────┐  ┌─────────────┐  ┌─────────┐  ┌───────────────┐  │
  │  │ api-staging │  │ web-staging │  │db-staging│  │ redis-staging │  │
  │  │             │  │             │  │         │  │               │  │
  │  │ Docker      │  │ Docker      │  │ Managed │  │ Managed       │  │
  │  │ Container   │  │ Container   │  │ Postgres│  │ Redis         │  │
  │  └─────────────┘  └─────────────┘  └─────────┘  └───────────────┘  │
  │        │                │                                          │
  │        └────────────────┴──────────────────────────────────────────│
  │                    staging.permitto.example.com                     │
  └─────────────────────────────────────────────────────────────────────┘

  ┌─────────────────────────────────────────────────────────────────────┐
  │                     PRODUCTION ENVIRONMENT                          │
  │  ┌─────────────┐  ┌─────────────┐  ┌─────────┐  ┌───────────────┐  │
  │  │  api-prod   │  │  web-prod   │  │ db-prod │  │  redis-prod   │  │
  │  │             │  │             │  │         │  │               │  │
  │  │ Docker      │  │ Docker      │  │ Managed │  │ Managed       │  │
  │  │ Container   │  │ Container   │  │ Postgres│  │ Redis         │  │
  │  │             │  │             │  │         │  │               │  │
  │  │ Can scale   │  │ Can scale   │  │ Backups │  │ Persistence   │  │
  │  │ horizontally│  │ horizontally│  │ enabled │  │ enabled       │  │
  │  └─────────────┘  └─────────────┘  └─────────┘  └───────────────┘  │
  │        │                │                                          │
  │        └────────────────┴──────────────────────────────────────────│
  │                      permitto.example.com                          │
  └─────────────────────────────────────────────────────────────────────┘
```

---

### Database Migration Strategy

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    DATABASE MIGRATION FLOW                              │
└─────────────────────────────────────────────────────────────────────────┘

  Developer Machine                CI/CD Pipeline
  ─────────────────                ───────────────

  1. Create migration              4. Deploy to Staging
     │                                │
     ▼                                ▼
  ┌─────────────────────┐         ┌─────────────────────┐
  │ pnpm prisma migrate │         │ prisma migrate      │
  │ dev --name add_email│         │ deploy              │
  └──────────┬──────────┘         └──────────┬──────────┘
             │                               │
             ▼                               ▼
  2. Migration file created        5. Migration runs on
     prisma/migrations/               staging database
     20260115_add_email/
     migration.sql
             │
             ▼                    6. Test on staging
  3. Commit and push                  │
     to Git                           ▼
                                   7. Deploy to Production
                                      │
                                      ▼
                                   8. Migration runs on
                                      production database
```

**Migration Commands:**

| Command | When to Use | Environment |
|---------|-------------|-------------|
| `prisma migrate dev` | Creating new migrations | Local development |
| `prisma migrate deploy` | Applying migrations | Staging/Production |
| `prisma migrate reset` | Wipe and recreate DB | Local only (dangerous!) |

---

### Rollback Strategy

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        ROLLBACK OPTIONS                                 │
└─────────────────────────────────────────────────────────────────────────┘

  OPTION 1: Railway Instant Rollback
  ───────────────────────────────────
  Railway keeps previous deployments. One-click rollback in dashboard.

  ┌─────────────┐     ┌─────────────┐     ┌─────────────┐
  │ Deployment  │     │ Deployment  │     │ Deployment  │
  │ v1.2.0      │     │ v1.2.1      │     │ v1.2.0      │
  │ (previous)  │────►│ (broken)    │────►│ (rollback)  │
  └─────────────┘     └─────────────┘     └─────────────┘
                           │
                           │ Click "Rollback"
                           ▼
                      Instant restore


  OPTION 2: Git Revert + Redeploy
  ────────────────────────────────
  Revert the commit, push, triggers new deployment.

  git revert HEAD
  git push origin main
  # CI/CD deploys the reverted code


  OPTION 3: Database Point-in-Time Recovery
  ──────────────────────────────────────────
  Railway PostgreSQL supports point-in-time recovery.
  Restore database to state before bad migration.

  Warning: Use only for data emergencies, not code rollbacks.
```

---

### Secrets Management

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      SECRETS MANAGEMENT                                 │
└─────────────────────────────────────────────────────────────────────────┘

  ┌─────────────────────────────────────────────────────────────────────┐
  │                    GITHUB SECRETS                                   │
  │  (Used by CI/CD workflows)                                          │
  │                                                                     │
  │  Repository Secrets:                                                │
  │  ├── RAILWAY_TOKEN          # Staging deployment                    │
  │  ├── RAILWAY_TOKEN_PROD     # Production deployment                 │
  │  └── SENTRY_AUTH_TOKEN      # Error tracking                        │
  │                                                                     │
  │  Environment: staging                                               │
  │  └── (inherits repository secrets)                                  │
  │                                                                     │
  │  Environment: production                                            │
  │  ├── (inherits repository secrets)                                  │
  │  └── Required reviewers: @team-lead                                 │
  └─────────────────────────────────────────────────────────────────────┘

  ┌─────────────────────────────────────────────────────────────────────┐
  │                    RAILWAY VARIABLES                                │
  │  (Used by running containers)                                       │
  │                                                                     │
  │  Service: permitto-api-production                                   │
  │  ├── DATABASE_URL          # Auto-linked from Railway Postgres      │
  │  ├── REDIS_URL             # Auto-linked from Railway Redis         │
  │  ├── JWT_SECRET            # Manually set                           │
  │  ├── JWT_REFRESH_SECRET    # Manually set                           │
  │  ├── R2_ACCOUNT_ID         # Cloudflare credentials                 │
  │  ├── R2_ACCESS_KEY_ID                                               │
  │  ├── R2_SECRET_ACCESS_KEY                                           │
  │  ├── R2_BUCKET_NAME                                                 │
  │  ├── RESEND_API_KEY        # Email service                          │
  │  └── SENTRY_DSN            # Error tracking                         │
  └─────────────────────────────────────────────────────────────────────┘
```

---

### Complete Developer Workflow

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    COMPLETE DEVELOPMENT WORKFLOW                        │
└─────────────────────────────────────────────────────────────────────────┘

  ┌──────────────────┐
  │ 1. LOCAL DEV     │
  │                  │
  │ docker-compose up│
  │ Make changes     │
  │ Hot reload       │
  │ Run tests        │
  └────────┬─────────┘
           │
           ▼
  ┌──────────────────┐
  │ 2. PUSH BRANCH   │
  │                  │
  │ git checkout -b  │
  │   feature/xyz    │
  │ git commit       │
  │ git push         │
  └────────┬─────────┘
           │
           ▼
  ┌──────────────────┐     ┌──────────────────┐
  │ 3. CREATE PR     │────►│ 4. CI RUNS       │
  │                  │     │                  │
  │ Open PR to       │     │ • Lint           │
  │ develop branch   │     │ • Typecheck      │
  │                  │     │ • Test           │
  │                  │     │ • Build          │
  └──────────────────┘     └────────┬─────────┘
                                    │
           ┌────────────────────────┘
           ▼
  ┌──────────────────┐
  │ 5. CODE REVIEW   │
  │                  │
  │ Team reviews PR  │
  │ Approve/Request  │
  │ changes          │
  └────────┬─────────┘
           │
           ▼
  ┌──────────────────┐     ┌──────────────────┐
  │ 6. MERGE TO      │────►│ 7. AUTO DEPLOY   │
  │    DEVELOP       │     │    TO STAGING    │
  │                  │     │                  │
  │ Squash & merge   │     │ Railway builds   │
  │                  │     │ and deploys      │
  └──────────────────┘     └────────┬─────────┘
                                    │
           ┌────────────────────────┘
           ▼
  ┌──────────────────┐
  │ 8. TEST STAGING  │
  │                  │
  │ QA testing       │
  │ Stakeholder      │
  │ review           │
  └────────┬─────────┘
           │
           ▼
  ┌──────────────────┐     ┌──────────────────┐
  │ 9. MERGE TO MAIN │────►│ 10. MANUAL       │
  │                  │     │     APPROVAL     │
  │ Create PR to     │     │                  │
  │ main branch      │     │ Team lead        │
  │                  │     │ approves deploy  │
  └──────────────────┘     └────────┬─────────┘
                                    │
           ┌────────────────────────┘
           ▼
  ┌──────────────────┐
  │ 11. PRODUCTION   │
  │     DEPLOY       │
  │                  │
  │ Railway deploys  │
  │ to production    │
  │                  │
  │ LIVE!            │
  └──────────────────┘
```

---

## Summary Tables

### Docker Summary

| Aspect | Development | Production |
|--------|-------------|------------|
| Command | `docker-compose up` | Railway auto-builds |
| Hot reload | Yes (volume mounts) | No (code in image) |
| Database | Local container | Railway managed |
| Image size | Not optimized | Multi-stage (~150MB) |
| Rebuild | Dependency changes only | Every deployment |

### CI/CD Summary

| Stage | Trigger | Actions | Blocks Deployment? |
|-------|---------|---------|-------------------|
| Lint | Every push/PR | ESLint | Yes |
| Typecheck | Every push/PR | tsc --noEmit | Yes |
| Test | Every push/PR | Jest/Vitest | Yes |
| Build | After tests pass | Docker build | Yes |
| Deploy Staging | Merge to develop | Railway deploy | N/A (auto) |
| Deploy Prod | Manual trigger | Railway deploy | N/A (manual gate) |

---

## Quick Reference Commands

### Local Development

```bash
# Start all services
docker-compose up

# Start in background
docker-compose up -d

# View logs
docker-compose logs -f api

# Rebuild after dependency changes
docker-compose up --build

# Stop all services
docker-compose down

# Stop and remove volumes (wipes data!)
docker-compose down -v
```

### Database Operations

```bash
# Create new migration
pnpm --filter api prisma migrate dev --name your_migration_name

# Apply migrations (staging/production)
pnpm --filter api prisma migrate deploy

# Open Prisma Studio (GUI)
pnpm --filter api prisma studio

# Reset database (local only!)
pnpm --filter api prisma migrate reset
```

### CI/CD Operations

```bash
# Run linting locally
pnpm lint

# Run type checking locally
pnpm typecheck

# Run tests locally
pnpm test

# Build all packages
pnpm build
```

---

*This guide was generated during a brainstorm session discussing the architectural decisions in PRD v2 and WORKFLOW v2.*
