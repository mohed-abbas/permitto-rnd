# Permitto Implementation Progress

**Started:** _[Add date when starting]_
**Target MVP:** 8 weeks
**Reference:** See `WORKFLOW_v2.md` for detailed task breakdowns

---

## Phase 0: Project Setup (Week 1)

### 0.1 Monorepo Initialization

- [ ] Initialize Turborepo with pnpm workspaces
- [ ] Create apps/web (Next.js 14)
- [ ] Create apps/api (Express.js + TypeScript)
- [ ] Create packages/shared
- [ ] Configure TypeScript paths and references
- [ ] Set up ESLint + Prettier (shared config)
- [ ] Configure Husky pre-commit hooks

### 0.2 Docker Setup

- [ ] Create Dockerfile for apps/api
- [ ] Create Dockerfile for apps/web
- [ ] Create docker-compose.yml (dev)
- [ ] Configure PostgreSQL container
- [ ] Configure Redis container
- [ ] Create .env.example with all variables
- [ ] Document local setup in README

### 0.3 CI/CD Pipeline Setup

- [ ] Create GitHub Actions CI workflow
- [ ] Add lint and type-check jobs
- [ ] Add test job (placeholder)
- [ ] Create staging deployment workflow
- [ ] Create production deployment workflow
- [ ] Configure Railway project and services
- [ ] Set up GitHub secrets for deployment

### Phase 0 Quality Gate

- [ ] Monorepo runs with `pnpm dev`
- [ ] Docker Compose starts all services
- [ ] CI pipeline runs successfully

**Phase 0 Completed:** _[Add date]_

---

## Phase 1: Backend Foundation (Week 2)

### 1.1 Express.js Setup

- [ ] Set up Express with TypeScript
- [ ] Configure middleware (cors, helmet, morgan)
- [ ] Create error handling middleware
- [ ] Create response utility (consistent format)
- [ ] Set up environment configuration
- [ ] Create health check endpoint

### 1.2 Database Setup

- [ ] Install Prisma and configure
- [ ] Create schema.prisma (from PRD v2)
- [ ] Run initial migration
- [ ] Create Prisma client singleton
- [ ] Create seed script

### 1.3 Redis Setup

- [ ] Install ioredis
- [ ] Create Redis client singleton
- [ ] Create session service
- [ ] Create rate limiter middleware

### 1.4 Authentication

- [ ] Install jsonwebtoken, bcrypt
- [ ] Create auth service (login, register, refresh)
- [ ] Create auth middleware (JWT validation)
- [ ] Create auth routes
- [ ] Implement password reset flow
- [ ] Store refresh tokens in Redis

### 1.5 RBAC Implementation

- [ ] Define permissions in packages/shared
- [ ] Create RBAC middleware
- [ ] Add role to JWT payload
- [ ] Write permission check tests

### Phase 1 Quality Gate

- [ ] API: Express server starts and responds to /health
- [ ] Database: Prisma connects, migrations applied
- [ ] Redis: Connection established, sessions work
- [ ] Auth: Login returns JWT, refresh works
- [ ] RBAC: Permissions enforced on protected routes
- [ ] Docker: `docker-compose up` runs all services
- [ ] CI: Pipeline passes on PR

**Phase 1 Completed:** _[Add date]_

---

## Phase 2: Core API Endpoints (Week 3)

### 2.1 User Management API

- [ ] Create user.service.ts
- [ ] Create user.controller.ts
- [ ] Create user.routes.ts
- [ ] Add user validators (Zod)
- [ ] Write user API tests

### 2.2 Project Management API

- [ ] Create project.service.ts
- [ ] Create project.controller.ts
- [ ] Create project.routes.ts
- [ ] Implement user-project assignment
- [ ] Write project API tests

### 2.3 Template Management API

- [ ] Create template.service.ts
- [ ] Create template.controller.ts
- [ ] Create template.routes.ts
- [ ] Implement template versioning
- [ ] Create 4 default templates (seed)

### 2.4 File Upload API (Cloudflare R2)

- [ ] Install @aws-sdk/client-s3
- [ ] Create R2 client configuration
- [ ] Create file.service.ts
- [ ] Implement pre-signed URL generation
- [ ] Create file.controller.ts
- [ ] Create file.routes.ts
- [ ] Add file size and type validation

### Phase 2 Quality Gate

- [ ] All CRUD APIs working
- [ ] File upload to R2 working
- [ ] API tests passing

**Phase 2 Completed:** _[Add date]_

---

## Phase 3: Permit Workflow API (Week 4)

### 3.1 Permit CRUD

- [ ] Create permit.service.ts
- [ ] Create permit-state-machine.ts
- [ ] Create permit.controller.ts
- [ ] Create permit.routes.ts
- [ ] Implement permit number generation
- [ ] Write permit state machine tests

### 3.2 Approval Workflow

- [ ] Implement submit endpoint
- [ ] Implement issuer approve/reject
- [ ] Implement coordinator approve/reject
- [ ] Create audit log entries
- [ ] Write approval flow tests

### 3.3 Daily Re-Approval

- [ ] Create daily-approval.service.ts
- [ ] Implement daily approve endpoint
- [ ] Implement bulk approve endpoint
- [ ] Create DailyApproval records on permit activation

### 3.4 Permit Close-Out

- [ ] Implement close endpoint
- [ ] Implement early close-out request
- [ ] Archive permit files

### Phase 3 Quality Gate

- [ ] Full permit workflow (Draft → Approved → Closed)
- [ ] Daily re-approval working
- [ ] State machine tests passing

**Phase 3 Completed:** _[Add date]_

---

## Phase 4: Background Jobs (Week 5)

### 4.1 Queue Setup

- [ ] Install and configure BullMQ
- [ ] Create queue factory
- [ ] Create worker process
- [ ] Add job monitoring (Bull Board)

### 4.2 Scheduled Jobs (Cron)

- [ ] Create daily-reminder job (7:00 AM)
- [ ] Create check-suspensions job (8:05 AM)
- [ ] Create expiry-warning job (12:00 AM)
- [ ] Create end-of-day job (5:00 PM)
- [ ] Add cron job scheduling on startup

### 4.3 Email Integration

- [ ] Install Resend SDK
- [ ] Create email.service.ts
- [ ] Create email templates (9 types)
- [ ] Create send-email job processor
- [ ] Integrate email triggers into workflow

### 4.4 PDF Generation

- [ ] Install Puppeteer
- [ ] Create PDF template (HTML)
- [ ] Create pdf.service.ts
- [ ] Create generate-pdf job processor
- [ ] Upload generated PDF to R2
- [ ] Create PDF download endpoint

### Phase 4 Quality Gate

- [ ] Cron jobs running reliably
- [ ] Emails sending correctly
- [ ] PDFs generating correctly

**Phase 4 Completed:** _[Add date]_

---

## Phase 5: Frontend Foundation (Week 6)

### 5.1 Next.js Setup

- [ ] Configure Tailwind with design tokens
- [ ] Install and configure shadcn/ui
- [ ] Create API client (axios)
- [ ] Set up TanStack Query provider
- [ ] Create auth provider and hooks
- [ ] Create permission hook

### 5.2 Layout & Navigation

- [ ] Create app shell layout
- [ ] Create sidebar navigation
- [ ] Create mobile navigation
- [ ] Create header with user menu
- [ ] Create role-based dashboard pages

### 5.3 Auth Pages

- [ ] Create login page
- [ ] Create register page
- [ ] Create forgot password page
- [ ] Create reset password page
- [ ] Implement auth state persistence

### Phase 5 Quality Gate

- [ ] Auth UI working
- [ ] Navigation and layout complete
- [ ] API integration working

**Phase 5 Completed:** _[Add date]_

---

## Phase 6: Frontend Features (Week 7)

### 6.1 User & Project Management UI

- [ ] Create user list page
- [ ] Create user form modal
- [ ] Create project list page
- [ ] Create project detail page
- [ ] Create user assignment UI

### 6.2 Template Builder UI

- [ ] Create template list page
- [ ] Create template builder page
- [ ] Create field palette
- [ ] Create field property editor
- [ ] Create template preview

### 6.3 Permit Form UI

- [ ] Create dynamic form renderer
- [ ] Create all field type components
- [ ] Create step navigation
- [ ] Implement auto-save
- [ ] Create file upload component
- [ ] Create signature capture component

### 6.4 Approval UI

- [ ] Create pending approvals dashboard
- [ ] Create permit detail view
- [ ] Create approve/reject modals
- [ ] Create daily approval UI
- [ ] Create bulk approve UI

### 6.5 Notifications UI

- [ ] Create notification bell component
- [ ] Create notification dropdown
- [ ] Implement mark as read
- [ ] Set up Web Push subscription

### Phase 6 Quality Gate

- [ ] All UI features working
- [ ] Forms render correctly
- [ ] Approvals work end-to-end

**Phase 6 Completed:** _[Add date]_

---

## Phase 7: Polish & Testing (Week 8)

### 7.1 Mobile Responsiveness

- [ ] Audit all pages for mobile
- [ ] Fix layout issues
- [ ] Optimize touch targets
- [ ] Test signature on mobile

### 7.2 Testing

- [ ] Write API unit tests
- [ ] Write API integration tests
- [ ] Write E2E tests (Playwright)
- [ ] Manual QA testing

### 7.3 Documentation & Deployment

- [ ] Write API documentation
- [ ] Write deployment guide
- [ ] Configure production environment
- [ ] Production deployment
- [ ] Production smoke test

### Phase 7 Quality Gate (MVP Complete)

- [ ] Mobile responsive
- [ ] Tests passing
- [ ] Production deployed
- [ ] Documentation complete

**Phase 7 Completed:** _[Add date]_

---

## MVP Summary

| Phase | Status | Completed |
| ----- | ------ | --------- |
| Phase 0: Project Setup | Not Started | |
| Phase 1: Backend Foundation | Not Started | |
| Phase 2: Core API Endpoints | Not Started | |
| Phase 3: Permit Workflow API | Not Started | |
| Phase 4: Background Jobs | Not Started | |
| Phase 5: Frontend Foundation | Not Started | |
| Phase 6: Frontend Features | Not Started | |
| Phase 7: Polish & Testing | Not Started | |

**MVP Completed:** _[Add date]_
