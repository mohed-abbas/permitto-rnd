# Permitto Implementation Workflow v2

**Generated:** January 15, 2026
**Strategy:** Systematic | **Depth:** Deep
**Source:** PRD v2.0

---

## Executive Summary

This workflow provides a comprehensive, phase-by-phase implementation plan for the Permitto Permit To Work (PTW) application with a **separate Node.js backend architecture**.

**Target MVP:** 8 weeks (extended from 6 weeks due to backend separation)
**Architecture:** Monorepo with Next.js Frontend + Express.js Backend + Docker

---

## Architecture Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                         MONOREPO                                 │
├─────────────────────────────────────────────────────────────────┤
│  apps/web (Next.js)     │     apps/api (Express.js)            │
│  ├── React UI           │     ├── REST API                     │
│  ├── TanStack Query     │     ├── JWT Auth                     │
│  └── Zustand            │     ├── Prisma ORM                   │
│                         │     ├── BullMQ Jobs                  │
│                         │     └── R2 Integration               │
├─────────────────────────────────────────────────────────────────┤
│  packages/shared        │     Infrastructure                   │
│  ├── Types              │     ├── PostgreSQL (Railway)         │
│  ├── Constants          │     ├── Redis (Railway)              │
│  └── Validators (Zod)   │     └── Cloudflare R2                │
└─────────────────────────────────────────────────────────────────┘
```

---

## Tech Stack v2

| Layer | Technology | Version |
|-------|------------|---------|
| **Monorepo** | Turborepo | latest |
| **Frontend** | Next.js (App Router) | 14.x+ |
| **Backend** | Express.js | 4.x |
| **Language** | TypeScript (strict) | 5.x |
| **UI Components** | shadcn/ui | latest |
| **Styling** | Tailwind CSS | 3.x |
| **Forms** | React Hook Form + Zod | 7.x / 3.x |
| **Client State** | Zustand | 4.x |
| **Server State** | TanStack Query | 5.x |
| **Auth** | JWT (jsonwebtoken) | 9.x |
| **ORM** | Prisma | 5.x |
| **Database** | PostgreSQL | 15+ |
| **Cache/Queue** | Redis + BullMQ | 7.x / 5.x |
| **File Storage** | Cloudflare R2 | - |
| **Email** | Resend | latest |
| **PDF** | Puppeteer | 21.x |
| **Signatures** | react-signature-canvas | 1.x |
| **Containerization** | Docker + Docker Compose | latest |
| **Hosting** | Railway | - |
| **Monitoring** | Sentry | latest |

---

## Project Structure

```
permitto/
├── apps/
│   ├── web/                          # Next.js Frontend
│   │   ├── src/
│   │   │   ├── app/                  # App Router pages
│   │   │   │   ├── (auth)/
│   │   │   │   │   ├── login/
│   │   │   │   │   ├── register/
│   │   │   │   │   └── forgot-password/
│   │   │   │   ├── (dashboard)/
│   │   │   │   │   ├── layout.tsx
│   │   │   │   │   ├── page.tsx
│   │   │   │   │   ├── projects/
│   │   │   │   │   ├── users/
│   │   │   │   │   ├── templates/
│   │   │   │   │   ├── permits/
│   │   │   │   │   └── settings/
│   │   │   │   └── layout.tsx
│   │   │   ├── components/
│   │   │   │   ├── ui/               # shadcn/ui
│   │   │   │   ├── forms/
│   │   │   │   ├── permits/
│   │   │   │   ├── templates/
│   │   │   │   └── layout/
│   │   │   ├── hooks/
│   │   │   │   ├── use-auth.ts
│   │   │   │   ├── use-permissions.ts
│   │   │   │   └── use-notifications.ts
│   │   │   ├── lib/
│   │   │   │   ├── api-client.ts     # Axios/fetch wrapper
│   │   │   │   └── utils.ts
│   │   │   ├── stores/
│   │   │   │   ├── auth-store.ts
│   │   │   │   └── notification-store.ts
│   │   │   └── providers/
│   │   │       ├── query-provider.tsx
│   │   │       └── auth-provider.tsx
│   │   ├── public/
│   │   ├── Dockerfile
│   │   ├── next.config.js
│   │   └── package.json
│   │
│   └── api/                          # Express.js Backend
│       ├── src/
│       │   ├── config/
│       │   │   ├── database.ts
│       │   │   ├── redis.ts
│       │   │   ├── r2.ts
│       │   │   └── env.ts
│       │   ├── controllers/
│       │   │   ├── auth.controller.ts
│       │   │   ├── users.controller.ts
│       │   │   ├── projects.controller.ts
│       │   │   ├── templates.controller.ts
│       │   │   ├── permits.controller.ts
│       │   │   ├── files.controller.ts
│       │   │   └── notifications.controller.ts
│       │   ├── middleware/
│       │   │   ├── auth.middleware.ts
│       │   │   ├── rbac.middleware.ts
│       │   │   ├── validate.middleware.ts
│       │   │   ├── error.middleware.ts
│       │   │   └── rate-limit.middleware.ts
│       │   ├── routes/
│       │   │   ├── index.ts
│       │   │   ├── auth.routes.ts
│       │   │   ├── users.routes.ts
│       │   │   ├── projects.routes.ts
│       │   │   ├── templates.routes.ts
│       │   │   ├── permits.routes.ts
│       │   │   ├── files.routes.ts
│       │   │   └── notifications.routes.ts
│       │   ├── services/
│       │   │   ├── auth.service.ts
│       │   │   ├── user.service.ts
│       │   │   ├── permit.service.ts
│       │   │   ├── permit-state-machine.ts
│       │   │   ├── template.service.ts
│       │   │   ├── file.service.ts
│       │   │   ├── notification.service.ts
│       │   │   ├── email.service.ts
│       │   │   └── pdf.service.ts
│       │   ├── jobs/
│       │   │   ├── queue.ts
│       │   │   ├── daily-reminder.job.ts
│       │   │   ├── check-suspensions.job.ts
│       │   │   ├── expiry-warning.job.ts
│       │   │   ├── generate-pdf.job.ts
│       │   │   └── send-email.job.ts
│       │   ├── utils/
│       │   │   ├── logger.ts
│       │   │   ├── response.ts
│       │   │   └── permit-number.ts
│       │   └── index.ts
│       ├── prisma/
│       │   ├── schema.prisma
│       │   ├── migrations/
│       │   └── seed.ts
│       ├── Dockerfile
│       └── package.json
│
├── packages/
│   └── shared/                       # Shared code
│       ├── src/
│       │   ├── types/
│       │   │   ├── user.ts
│       │   │   ├── permit.ts
│       │   │   ├── template.ts
│       │   │   └── api.ts
│       │   ├── constants/
│       │   │   ├── permissions.ts
│       │   │   ├── permit-status.ts
│       │   │   └── roles.ts
│       │   └── validators/
│       │       ├── auth.validator.ts
│       │       ├── user.validator.ts
│       │       ├── permit.validator.ts
│       │       └── template.validator.ts
│       └── package.json
│
├── docker-compose.yml                # Development
├── docker-compose.prod.yml           # Production reference
├── .github/
│   └── workflows/
│       ├── ci.yml                    # CI pipeline
│       ├── deploy-staging.yml        # Staging deployment
│       └── deploy-production.yml     # Production deployment
├── .env.example
├── turbo.json
├── package.json
└── README.md
```

---

## Phase 0: Project Setup (Week 1)

### 0.1 Monorepo Initialization

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 0.1.1 | Initialize Turborepo with pnpm workspaces | P0 | 2 |
| 0.1.2 | Create apps/web (Next.js 14) | P0 | 1 |
| 0.1.3 | Create apps/api (Express.js + TypeScript) | P0 | 2 |
| 0.1.4 | Create packages/shared | P0 | 1 |
| 0.1.5 | Configure TypeScript paths and references | P0 | 1 |
| 0.1.6 | Set up ESLint + Prettier (shared config) | P0 | 1 |
| 0.1.7 | Configure Husky pre-commit hooks | P1 | 1 |

**Commands:**
```bash
# Initialize monorepo
pnpm dlx create-turbo@latest permitto --package-manager pnpm

# Create Next.js app
cd apps && pnpm create next-app web --typescript --tailwind --app --src-dir

# Create Express app
mkdir api && cd api && pnpm init
pnpm add express cors helmet morgan dotenv
pnpm add -D typescript @types/express @types/node ts-node nodemon
```

**Deliverables:**
- [ ] Monorepo runs with `pnpm dev`
- [ ] Both apps start without errors
- [ ] Shared package imports work

---

### 0.2 Docker Setup

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 0.2.1 | Create Dockerfile for apps/api | P0 | 2 |
| 0.2.2 | Create Dockerfile for apps/web | P0 | 2 |
| 0.2.3 | Create docker-compose.yml (dev) | P0 | 2 |
| 0.2.4 | Configure PostgreSQL container | P0 | 1 |
| 0.2.5 | Configure Redis container | P0 | 1 |
| 0.2.6 | Create .env.example with all variables | P0 | 1 |
| 0.2.7 | Document local setup in README | P1 | 1 |

**docker-compose.yml (Development):**
```yaml
version: '3.8'

services:
  db:
    image: postgres:15-alpine
    environment:
      POSTGRES_USER: permitto
      POSTGRES_PASSWORD: permitto_dev
      POSTGRES_DB: permitto
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U permitto"]
      interval: 5s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data

  api:
    build:
      context: .
      dockerfile: apps/api/Dockerfile
      target: development
    environment:
      NODE_ENV: development
      DATABASE_URL: postgresql://permitto:permitto_dev@db:5432/permitto
      REDIS_URL: redis://redis:6379
    ports:
      - "3001:3001"
    volumes:
      - ./apps/api:/app/apps/api
      - ./packages:/app/packages
      - /app/node_modules
    depends_on:
      - db
      - redis

  web:
    build:
      context: .
      dockerfile: apps/web/Dockerfile
      target: development
    environment:
      NEXT_PUBLIC_API_URL: http://localhost:3001/api/v1
    ports:
      - "3000:3000"
    volumes:
      - ./apps/web:/app/apps/web
      - ./packages:/app/packages
      - /app/node_modules
    depends_on:
      - api

volumes:
  postgres_data:
  redis_data:
```

**Deliverables:**
- [ ] `docker-compose up` starts all services
- [ ] API connects to PostgreSQL and Redis
- [ ] Hot reload works in development

---

### 0.3 CI/CD Pipeline Setup

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 0.3.1 | Create GitHub Actions CI workflow | P0 | 2 |
| 0.3.2 | Add lint and type-check jobs | P0 | 1 |
| 0.3.3 | Add test job (placeholder) | P0 | 1 |
| 0.3.4 | Create staging deployment workflow | P1 | 3 |
| 0.3.5 | Create production deployment workflow | P1 | 3 |
| 0.3.6 | Configure Railway project and services | P0 | 2 |
| 0.3.7 | Set up GitHub secrets for deployment | P0 | 1 |

**.github/workflows/ci.yml:**
```yaml
name: CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  lint:
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
      - run: pnpm lint

  typecheck:
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

  test:
    runs-on: ubuntu-latest
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
      - run: pnpm test
        env:
          DATABASE_URL: postgresql://test:test@localhost:5432/permitto_test

  build:
    runs-on: ubuntu-latest
    needs: [lint, typecheck, test]
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
      - run: pnpm build
```

**.github/workflows/deploy-staging.yml:**
```yaml
name: Deploy to Staging

on:
  push:
    branches: [develop]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install Railway CLI
        run: npm i -g @railway/cli

      - name: Deploy API to Railway
        run: railway up --service permitto-api-staging
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}

      - name: Deploy Web to Railway
        run: railway up --service permitto-web-staging
        env:
          RAILWAY_TOKEN: ${{ secrets.RAILWAY_TOKEN }}
```

**Deliverables:**
- [ ] CI runs on every PR
- [ ] Staging deploys automatically on develop merge
- [ ] Production requires manual trigger

---

## Phase 1: Backend Foundation (Week 2)

### 1.1 Express.js Setup

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 1.1.1 | Set up Express with TypeScript | P0 | 2 |
| 1.1.2 | Configure middleware (cors, helmet, morgan) | P0 | 1 |
| 1.1.3 | Create error handling middleware | P0 | 2 |
| 1.1.4 | Create response utility (consistent format) | P0 | 1 |
| 1.1.5 | Set up environment configuration | P0 | 1 |
| 1.1.6 | Create health check endpoint | P0 | 0.5 |

**apps/api/src/index.ts:**
```typescript
import express from 'express';
import cors from 'cors';
import helmet from 'helmet';
import morgan from 'morgan';
import { errorHandler } from './middleware/error.middleware';
import routes from './routes';
import { env } from './config/env';

const app = express();

// Middleware
app.use(helmet());
app.use(cors({ origin: env.CORS_ORIGIN, credentials: true }));
app.use(morgan('combined'));
app.use(express.json());

// Routes
app.use('/api/v1', routes);

// Health check
app.get('/health', (req, res) => {
  res.json({ status: 'ok', timestamp: new Date().toISOString() });
});

// Error handling
app.use(errorHandler);

app.listen(env.PORT, () => {
  console.log(`Server running on port ${env.PORT}`);
});
```

---

### 1.2 Database Setup

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 1.2.1 | Install Prisma and configure | P0 | 1 |
| 1.2.2 | Create schema.prisma (from PRD v2) | P0 | 3 |
| 1.2.3 | Run initial migration | P0 | 0.5 |
| 1.2.4 | Create Prisma client singleton | P0 | 0.5 |
| 1.2.5 | Create seed script | P1 | 2 |

---

### 1.3 Redis Setup

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 1.3.1 | Install ioredis | P0 | 0.5 |
| 1.3.2 | Create Redis client singleton | P0 | 1 |
| 1.3.3 | Create session service | P0 | 2 |
| 1.3.4 | Create rate limiter middleware | P1 | 2 |

---

### 1.4 Authentication

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 1.4.1 | Install jsonwebtoken, bcrypt | P0 | 0.5 |
| 1.4.2 | Create auth service (login, register, refresh) | P0 | 4 |
| 1.4.3 | Create auth middleware (JWT validation) | P0 | 2 |
| 1.4.4 | Create auth routes | P0 | 2 |
| 1.4.5 | Implement password reset flow | P1 | 3 |
| 1.4.6 | Store refresh tokens in Redis | P0 | 1 |

---

### 1.5 RBAC Implementation

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 1.5.1 | Define permissions in packages/shared | P0 | 2 |
| 1.5.2 | Create RBAC middleware | P0 | 3 |
| 1.5.3 | Add role to JWT payload | P0 | 1 |
| 1.5.4 | Write permission check tests | P0 | 2 |

**packages/shared/src/constants/permissions.ts:**
```typescript
export const PERMISSIONS = {
  // Projects
  'project:create': ['SUPER_ADMIN'],
  'project:read': ['SUPER_ADMIN', 'COORDINATOR', 'ISSUER', 'RECEIVER'],
  'project:update': ['SUPER_ADMIN'],
  'project:delete': ['SUPER_ADMIN'],

  // Users
  'user:create:any': ['SUPER_ADMIN'],
  'user:create:issuer': ['SUPER_ADMIN', 'COORDINATOR'],
  'user:create:receiver': ['SUPER_ADMIN', 'COORDINATOR', 'ISSUER'],
  'user:read': ['SUPER_ADMIN', 'COORDINATOR', 'ISSUER'],
  'user:update': ['SUPER_ADMIN'],

  // Templates
  'template:create': ['COORDINATOR'],
  'template:read': ['SUPER_ADMIN', 'COORDINATOR', 'ISSUER', 'RECEIVER'],
  'template:update': ['COORDINATOR'],

  // Permits
  'permit:create': ['RECEIVER'],
  'permit:read:own': ['RECEIVER'],
  'permit:read:project': ['ISSUER'],
  'permit:read:all': ['SUPER_ADMIN', 'COORDINATOR'],
  'permit:approve:issuer': ['ISSUER'],
  'permit:approve:coordinator': ['COORDINATOR'],
  'permit:close': ['COORDINATOR'],

  // Files
  'file:upload': ['SUPER_ADMIN', 'COORDINATOR', 'ISSUER', 'RECEIVER'],
  'file:read': ['SUPER_ADMIN', 'COORDINATOR', 'ISSUER', 'RECEIVER'],
  'file:delete': ['SUPER_ADMIN', 'COORDINATOR'],
} as const;
```

---

### Phase 1 Quality Gate

Before proceeding to Phase 2, verify:

- [ ] **API:** Express server starts and responds to /health
- [ ] **Database:** Prisma connects, migrations applied
- [ ] **Redis:** Connection established, sessions work
- [ ] **Auth:** Login returns JWT, refresh works
- [ ] **RBAC:** Permissions enforced on protected routes
- [ ] **Docker:** `docker-compose up` runs all services
- [ ] **CI:** Pipeline passes on PR

---

## Phase 2: Core API Endpoints (Week 3)

### 2.1 User Management API

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 2.1.1 | Create user.service.ts | P0 | 3 |
| 2.1.2 | Create user.controller.ts | P0 | 2 |
| 2.1.3 | Create user.routes.ts | P0 | 1 |
| 2.1.4 | Add user validators (Zod) | P0 | 1 |
| 2.1.5 | Write user API tests | P1 | 2 |

---

### 2.2 Project Management API

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 2.2.1 | Create project.service.ts | P0 | 3 |
| 2.2.2 | Create project.controller.ts | P0 | 2 |
| 2.2.3 | Create project.routes.ts | P0 | 1 |
| 2.2.4 | Implement user-project assignment | P0 | 2 |
| 2.2.5 | Write project API tests | P1 | 2 |

---

### 2.3 Template Management API

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 2.3.1 | Create template.service.ts | P0 | 3 |
| 2.3.2 | Create template.controller.ts | P0 | 2 |
| 2.3.3 | Create template.routes.ts | P0 | 1 |
| 2.3.4 | Implement template versioning | P1 | 2 |
| 2.3.5 | Create 4 default templates (seed) | P0 | 3 |

---

### 2.4 File Upload API (Cloudflare R2)

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 2.4.1 | Install @aws-sdk/client-s3 | P0 | 0.5 |
| 2.4.2 | Create R2 client configuration | P0 | 1 |
| 2.4.3 | Create file.service.ts | P0 | 3 |
| 2.4.4 | Implement pre-signed URL generation | P0 | 2 |
| 2.4.5 | Create file.controller.ts | P0 | 2 |
| 2.4.6 | Create file.routes.ts | P0 | 1 |
| 2.4.7 | Add file size and type validation | P0 | 1 |

**apps/api/src/config/r2.ts:**
```typescript
import { S3Client } from '@aws-sdk/client-s3';
import { env } from './env';

export const r2Client = new S3Client({
  region: 'auto',
  endpoint: `https://${env.R2_ACCOUNT_ID}.r2.cloudflarestorage.com`,
  credentials: {
    accessKeyId: env.R2_ACCESS_KEY_ID,
    secretAccessKey: env.R2_SECRET_ACCESS_KEY,
  },
});
```

---

## Phase 3: Permit Workflow API (Week 4)

### 3.1 Permit CRUD

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 3.1.1 | Create permit.service.ts | P0 | 4 |
| 3.1.2 | Create permit-state-machine.ts | P0 | 4 |
| 3.1.3 | Create permit.controller.ts | P0 | 3 |
| 3.1.4 | Create permit.routes.ts | P0 | 1 |
| 3.1.5 | Implement permit number generation | P0 | 1 |
| 3.1.6 | Write permit state machine tests | P0 | 3 |

---

### 3.2 Approval Workflow

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 3.2.1 | Implement submit endpoint | P0 | 2 |
| 3.2.2 | Implement issuer approve/reject | P0 | 3 |
| 3.2.3 | Implement coordinator approve/reject | P0 | 3 |
| 3.2.4 | Create audit log entries | P0 | 2 |
| 3.2.5 | Write approval flow tests | P0 | 3 |

---

### 3.3 Daily Re-Approval

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 3.3.1 | Create daily-approval.service.ts | P0 | 3 |
| 3.3.2 | Implement daily approve endpoint | P0 | 2 |
| 3.3.3 | Implement bulk approve endpoint | P1 | 2 |
| 3.3.4 | Create DailyApproval records on permit activation | P0 | 2 |

---

### 3.4 Permit Close-Out

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 3.4.1 | Implement close endpoint | P0 | 2 |
| 3.4.2 | Implement early close-out request | P1 | 2 |
| 3.4.3 | Archive permit files | P1 | 1 |

---

## Phase 4: Background Jobs (Week 5)

### 4.1 Queue Setup

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 4.1.1 | Install and configure BullMQ | P0 | 2 |
| 4.1.2 | Create queue factory | P0 | 1 |
| 4.1.3 | Create worker process | P0 | 2 |
| 4.1.4 | Add job monitoring (Bull Board) | P1 | 1 |

**apps/api/src/jobs/queue.ts:**
```typescript
import { Queue, Worker, QueueScheduler } from 'bullmq';
import { redis } from '../config/redis';

const connection = { connection: redis };

export const emailQueue = new Queue('email', connection);
export const pdfQueue = new Queue('pdf', connection);
export const cronQueue = new Queue('cron', connection);

// Scheduler for delayed jobs
new QueueScheduler('email', connection);
new QueueScheduler('pdf', connection);
new QueueScheduler('cron', connection);
```

---

### 4.2 Scheduled Jobs (Cron)

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 4.2.1 | Create daily-reminder job (7:00 AM) | P0 | 2 |
| 4.2.2 | Create check-suspensions job (8:05 AM) | P0 | 3 |
| 4.2.3 | Create expiry-warning job (12:00 AM) | P0 | 2 |
| 4.2.4 | Create end-of-day job (5:00 PM) | P0 | 2 |
| 4.2.5 | Add cron job scheduling on startup | P0 | 1 |

---

### 4.3 Email Integration

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 4.3.1 | Install Resend SDK | P0 | 0.5 |
| 4.3.2 | Create email.service.ts | P0 | 2 |
| 4.3.3 | Create email templates (9 types) | P0 | 4 |
| 4.3.4 | Create send-email job processor | P0 | 2 |
| 4.3.5 | Integrate email triggers into workflow | P0 | 2 |

---

### 4.4 PDF Generation

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 4.4.1 | Install Puppeteer | P0 | 1 |
| 4.4.2 | Create PDF template (HTML) | P0 | 4 |
| 4.4.3 | Create pdf.service.ts | P0 | 3 |
| 4.4.4 | Create generate-pdf job processor | P0 | 2 |
| 4.4.5 | Upload generated PDF to R2 | P0 | 1 |
| 4.4.6 | Create PDF download endpoint | P0 | 1 |

---

## Phase 5: Frontend Foundation (Week 6)

### 5.1 Next.js Setup

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 5.1.1 | Configure Tailwind with design tokens | P0 | 1 |
| 5.1.2 | Install and configure shadcn/ui | P0 | 2 |
| 5.1.3 | Create API client (axios) | P0 | 2 |
| 5.1.4 | Set up TanStack Query provider | P0 | 1 |
| 5.1.5 | Create auth provider and hooks | P0 | 3 |
| 5.1.6 | Create permission hook | P0 | 2 |

---

### 5.2 Layout & Navigation

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 5.2.1 | Create app shell layout | P0 | 3 |
| 5.2.2 | Create sidebar navigation | P0 | 2 |
| 5.2.3 | Create mobile navigation | P0 | 2 |
| 5.2.4 | Create header with user menu | P0 | 2 |
| 5.2.5 | Create role-based dashboard pages | P0 | 4 |

---

### 5.3 Auth Pages

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 5.3.1 | Create login page | P0 | 2 |
| 5.3.2 | Create register page | P0 | 2 |
| 5.3.3 | Create forgot password page | P1 | 2 |
| 5.3.4 | Create reset password page | P1 | 2 |
| 5.3.5 | Implement auth state persistence | P0 | 2 |

---

## Phase 6: Frontend Features (Week 7)

### 6.1 User & Project Management UI

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 6.1.1 | Create user list page | P0 | 3 |
| 6.1.2 | Create user form modal | P0 | 2 |
| 6.1.3 | Create project list page | P0 | 3 |
| 6.1.4 | Create project detail page | P0 | 3 |
| 6.1.5 | Create user assignment UI | P0 | 2 |

---

### 6.2 Template Builder UI

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 6.2.1 | Create template list page | P0 | 2 |
| 6.2.2 | Create template builder page | P0 | 6 |
| 6.2.3 | Create field palette | P0 | 3 |
| 6.2.4 | Create field property editor | P0 | 3 |
| 6.2.5 | Create template preview | P0 | 2 |

---

### 6.3 Permit Form UI

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 6.3.1 | Create dynamic form renderer | P0 | 6 |
| 6.3.2 | Create all field type components | P0 | 4 |
| 6.3.3 | Create step navigation | P0 | 2 |
| 6.3.4 | Implement auto-save | P0 | 2 |
| 6.3.5 | Create file upload component | P0 | 3 |
| 6.3.6 | Create signature capture component | P0 | 2 |

---

### 6.4 Approval UI

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 6.4.1 | Create pending approvals dashboard | P0 | 3 |
| 6.4.2 | Create permit detail view | P0 | 4 |
| 6.4.3 | Create approve/reject modals | P0 | 2 |
| 6.4.4 | Create daily approval UI | P0 | 3 |
| 6.4.5 | Create bulk approve UI | P1 | 2 |

---

### 6.5 Notifications UI

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 6.5.1 | Create notification bell component | P0 | 2 |
| 6.5.2 | Create notification dropdown | P0 | 2 |
| 6.5.3 | Implement mark as read | P0 | 1 |
| 6.5.4 | Set up Web Push subscription | P1 | 3 |

---

## Phase 7: Polish & Testing (Week 8)

### 7.1 Mobile Responsiveness

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 7.1.1 | Audit all pages for mobile | P0 | 4 |
| 7.1.2 | Fix layout issues | P0 | 4 |
| 7.1.3 | Optimize touch targets | P0 | 2 |
| 7.1.4 | Test signature on mobile | P0 | 1 |

---

### 7.2 Testing

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 7.2.1 | Write API unit tests | P0 | 8 |
| 7.2.2 | Write API integration tests | P0 | 6 |
| 7.2.3 | Write E2E tests (Playwright) | P0 | 8 |
| 7.2.4 | Manual QA testing | P0 | 4 |

---

### 7.3 Documentation & Deployment

| Task ID | Task | Priority | Est. Hours |
|---------|------|----------|------------|
| 7.3.1 | Write API documentation | P1 | 4 |
| 7.3.2 | Write deployment guide | P0 | 2 |
| 7.3.3 | Configure production environment | P0 | 4 |
| 7.3.4 | Production deployment | P0 | 2 |
| 7.3.5 | Production smoke test | P0 | 2 |

---

## Phase Quality Gates

### Phase 0 Gate
- [ ] Monorepo working with `pnpm dev`
- [ ] Docker Compose starts all services
- [ ] CI pipeline runs successfully

### Phase 1 Gate
- [ ] Auth flow works (login, refresh, logout)
- [ ] RBAC enforced on all routes
- [ ] Database and Redis connected

### Phase 2 Gate
- [ ] All CRUD APIs working
- [ ] File upload to R2 working
- [ ] API tests passing

### Phase 3 Gate
- [ ] Full permit workflow (Draft → Approved → Closed)
- [ ] Daily re-approval working
- [ ] State machine tests passing

### Phase 4 Gate
- [ ] Cron jobs running reliably
- [ ] Emails sending correctly
- [ ] PDFs generating correctly

### Phase 5 Gate
- [ ] Auth UI working
- [ ] Navigation and layout complete
- [ ] API integration working

### Phase 6 Gate
- [ ] All UI features working
- [ ] Forms render correctly
- [ ] Approvals work end-to-end

### Phase 7 Gate (MVP Complete)
- [ ] Mobile responsive
- [ ] Tests passing
- [ ] Production deployed
- [ ] Documentation complete

---

## Task Summary

| Phase | Description | Est. Hours | Priority P0 | Priority P1 |
|-------|-------------|------------|-------------|-------------|
| 0 | Project Setup | 30 | 25 | 5 |
| 1 | Backend Foundation | 35 | 30 | 5 |
| 2 | Core API Endpoints | 40 | 35 | 5 |
| 3 | Permit Workflow API | 35 | 32 | 3 |
| 4 | Background Jobs | 35 | 30 | 5 |
| 5 | Frontend Foundation | 25 | 23 | 2 |
| 6 | Frontend Features | 55 | 50 | 5 |
| 7 | Polish & Testing | 45 | 40 | 5 |
| **Total** | | **300** | **265** | **35** |

**Estimated Duration:** 8 weeks (with 1 developer, ~40 hours/week)

---

## CI/CD Implementation Details

### GitHub Actions Workflows

| Workflow | Trigger | Actions |
|----------|---------|---------|
| `ci.yml` | PR, push to main/develop | Lint, typecheck, test, build |
| `deploy-staging.yml` | Push to develop | Build → Deploy to Railway staging |
| `deploy-production.yml` | Manual or tag | Build → Deploy to Railway production |

### Railway Configuration

| Service | Build Command | Start Command |
|---------|---------------|---------------|
| permitto-api | `pnpm build --filter=api` | `pnpm start --filter=api` |
| permitto-web | `pnpm build --filter=web` | `pnpm start --filter=web` |
| permitto-db | N/A (managed) | N/A |
| permitto-redis | N/A (managed) | N/A |

### Deployment Checklist

**Staging:**
- [ ] CI passes
- [ ] Auto-deploy on merge to develop
- [ ] Database migrations run

**Production:**
- [ ] All tests pass
- [ ] Manual approval required
- [ ] Database backup before migration
- [ ] Zero-downtime deployment
- [ ] Rollback plan ready

---

*This workflow was generated from PRD v2.0 with separate backend architecture, Docker containerization, and CI/CD implementation details.*
