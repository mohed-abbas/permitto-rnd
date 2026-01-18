# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Permitto is a Permit To Work (PTW) web application for construction/manufacturing operations. It digitalizes permit approval workflows with multi-step forms, digital signatures, daily re-approval cycles, and PDF generation.

**Architecture:** Turborepo monorepo with separate frontend and backend
- `apps/web` - Next.js 14+ frontend (App Router, React 18, shadcn/ui, TanStack Query, Zustand)
- `apps/api` - Express.js backend (REST API, JWT auth, Prisma ORM, BullMQ jobs)
- `packages/shared` - Shared types, constants, and Zod validators

**Infrastructure:** Docker-based development, Railway deployment, PostgreSQL, Redis, Cloudflare R2

## Directory Structure

This repository has two distinct areas:

### Root Directory (`/permitto`)
- Contains AI/development documentation (CLAUDE.md, PROGRESS.md, WORKFLOW files, claudedocs/)
- Used for planning, tracking, and development guidance
- Pushes to: `https://github.com/mohed-abbas/permitto-rnd.git`

### Code Directory (`/permitto/permitto-dev`)
- **Contains only production code** - the actual Turborepo monorepo with apps/web, apps/api, packages/
- **Only allowed documentation:** README.md and CHANGELOG.md (if needed)
- **No AI documentation files** (CLAUDE.md, PROGRESS.md, WORKFLOW files, etc.) should ever be committed here
- All CI/CD workflows, Docker configurations, and project setup live here
- Pushes to: `https://github.com/mohed-abbas/permitto.git`

### Important Rules
1. When working on code, always operate within `permitto-dev/`
2. All git operations for the production codebase must target `permitto-dev/` and its remote (`https://github.com/mohed-abbas/permitto.git`)
3. Never push AI-related documentation to the permitto-dev repository
4. The root directory is for development planning only; permitto-dev is for deployable code

## Commands

### Development
```bash
# Start all services (PostgreSQL, Redis, API, Web)
docker-compose up

# Start in background
docker-compose up -d

# View logs for specific service
docker-compose logs -f api

# Rebuild after dependency changes
docker-compose up --build

# Stop all services
docker-compose down

# Stop and remove volumes (wipes DB data)
docker-compose down -v
```

### Package Management (pnpm workspaces)
```bash
# Install dependencies
pnpm install

# Run command in specific workspace
pnpm --filter api <command>
pnpm --filter web <command>
pnpm --filter shared <command>

# Build all packages
pnpm build

# Lint all packages
pnpm lint

# Type check all packages
pnpm typecheck

# Run all tests
pnpm test
```

### Database (Prisma)
```bash
# Create new migration (local dev only)
pnpm --filter api prisma migrate dev --name <migration_name>

# Apply migrations (staging/production)
pnpm --filter api prisma migrate deploy

# Generate Prisma client after schema changes
pnpm --filter api prisma generate

# Open Prisma Studio GUI
pnpm --filter api prisma studio

# Reset database (local only - destructive!)
pnpm --filter api prisma migrate reset

# Seed database
pnpm --filter api prisma db seed
```

### Running Single Tests
```bash
# Run specific test file
pnpm --filter api test -- <test-file-path>

# Run tests matching pattern
pnpm --filter api test -- --grep "<pattern>"

# Run tests in watch mode
pnpm --filter api test -- --watch
```

## Architecture

### User Roles & Permissions
Four roles with hierarchical permissions:
1. **Super Admin** - Creates projects, all user types, system-wide visibility
2. **Coordinator** - Creates templates, creates Issuers, final approval, closes permits
3. **Issuer** - First-level approval, creates Receivers, daily re-approval
4. **Receiver** - Requests permits, fills forms, daily confirmation

### Permit Workflow State Machine
```
DRAFT → SUBMITTED → ISSUER_APPROVED → APPROVED → PENDING_DAILY/ACTIVE → PENDING_CLOSEOUT → CLOSED
                  ↓                 ↓                              ↓
            REJECTED_ISSUER    REJECTED_COORD                 SUSPENDED
                  ↓                 ↓
              (revise and resubmit to DRAFT)
```

### API Structure (apps/api/src/)
- `controllers/` - Route handlers
- `services/` - Business logic
- `middleware/` - Auth, RBAC, validation, error handling
- `routes/` - API route definitions
- `jobs/` - BullMQ background jobs (email, PDF, cron)
- `config/` - Database, Redis, R2, environment

### Frontend Structure (apps/web/src/)
- `app/` - Next.js App Router pages
- `components/` - React components (ui/, forms/, permits/, templates/, layout/)
- `hooks/` - Custom hooks (use-auth, use-permissions, use-notifications)
- `stores/` - Zustand stores
- `lib/` - API client, utilities
- `providers/` - React context providers

### Shared Package (packages/shared/src/)
- `types/` - TypeScript interfaces for User, Permit, Template, API responses
- `constants/` - Permissions matrix, permit statuses, roles
- `validators/` - Zod schemas shared between frontend and backend

### Key Background Jobs
- `daily-reminder` - 7:00 AM daily re-approval reminders
- `check-suspensions` - 8:05 AM suspend permits with missed approvals
- `expiry-warning` - 12:00 AM 24h expiry notifications
- `generate-pdf` - Queue-triggered PDF generation
- `send-email` - Queue-triggered email sending

## Key Patterns

### API Response Format
```typescript
// Success
{ success: true, data: {...}, meta: { page, limit, total } }

// Error
{ success: false, error: { code: "...", message: "...", details: [...] } }
```

### File Upload Pattern
Uses pre-signed URLs for direct browser-to-R2 uploads:
1. Client requests upload URL from API
2. API generates pre-signed URL from R2
3. Client uploads directly to R2
4. Client confirms upload to API

### JWT Authentication
- Access tokens (15min) + Refresh tokens (7 days)
- Tokens stored in httpOnly cookies
- Refresh token rotation on use
- Redis-backed session management

### RBAC Enforcement
Permissions defined in `packages/shared/src/constants/permissions.ts` and enforced via middleware on all API routes. UI hides inaccessible features.

## Environment Variables

Key variables needed (see `.env.example`):
- `DATABASE_URL` - PostgreSQL connection string
- `REDIS_URL` - Redis connection string
- `JWT_SECRET`, `JWT_REFRESH_SECRET` - Token signing secrets
- `R2_ACCOUNT_ID`, `R2_ACCESS_KEY_ID`, `R2_SECRET_ACCESS_KEY`, `R2_BUCKET_NAME` - Cloudflare R2
- `RESEND_API_KEY` - Email service
- `SENTRY_DSN` - Error tracking (optional)

## CI/CD

- **CI Pipeline** - Runs lint, typecheck, test, build on every PR
- **Staging** - Auto-deploys on merge to `develop` branch
- **Production** - Manual trigger with approval required

## Branch Management

### Branch Structure

```
main (production)
  └── develop (staging)
        ├── feat/authentication
        ├── feat/permit-workflow
        ├── fix/login-token-refresh
        └── ...
```

### Branch Naming

- `main` - Production-ready code
- `develop` - Integration branch (deploys to staging)
- `feat/<feature-name>` - New features (e.g., `feat/authentication`, `feat/permit-workflow`)
- `fix/<issue-name>` - Bug fixes (e.g., `fix/token-refresh`, `fix/form-validation`)

### Feature Branch Workflow

1. Create feature branch from `develop`:

   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feat/authentication
   ```

2. Develop the feature (multiple commits as you progress)

3. Before final push, verify locally:

   ```bash
   pnpm lint && pnpm typecheck && pnpm build
   ```

4. Push and create PR: `feat/authentication` → `develop`

5. CI runs automatically, merge when passing

6. Auto-deploys to staging for testing

7. When staging verified, PR from `develop` → `main` for production

### Phase-to-Branch Mapping

Suggested branches aligned with WORKFLOW_v2.md phases:

| Phase | Branch Name | Scope |
| ----- | ----------- | ----- |
| Phase 0 | `feat/project-setup` | Monorepo, Docker, CI/CD |
| Phase 1 | `feat/backend-foundation` | Express, Prisma, Redis, Auth, RBAC |
| Phase 2 | `feat/core-api` | User, Project, Template, File APIs |
| Phase 3 | `feat/permit-workflow` | Permit CRUD, approvals, daily re-approval |
| Phase 4 | `feat/background-jobs` | BullMQ, email, PDF generation |
| Phase 5 | `feat/frontend-foundation` | Next.js setup, auth UI, layout |
| Phase 6 | `feat/frontend-features` | Forms, approvals, notifications UI |
| Phase 7 | `feat/polish-testing` | Mobile, tests, documentation |

You may split phases into multiple branches if they become too large (e.g., `feat/auth` and `feat/rbac` instead of one `feat/backend-foundation`).

## Git Workflow Guidelines

### Before Committing

Always verify locally before creating a commit:

```bash
pnpm lint
pnpm typecheck
pnpm build
```

Fix any errors before committing. Do not commit code with lint or build failures.

### Commit Messages

- Write clear, descriptive commit messages focused on the "what" and "why"
- **Do NOT mention Claude Code, AI, or assistant usage in commit messages**
- Follow conventional commit style when appropriate (feat:, fix:, chore:, etc.)

### Commit Frequency

- Commit after completing each logical unit of work (task from WORKFLOW_v2.md)
- Push to feature branch at the end of each work session
- Create PR to `develop` when a phase deliverable is ready

## Progress Tracking

### File Locations

- `PROGRESS.md` - **Live progress tracker** (update this as you work)
- `WORKFLOW_v2.md` - Detailed reference with task breakdowns (read-only reference)

### Tracking Workflow

1. **Before starting a task:** Check `PROGRESS.md` for current status
2. **While working:** Mark tasks as complete in `PROGRESS.md` as you finish them
3. **After completing a phase:**
   - Mark all completed items with `[x]` in `PROGRESS.md`
   - Update the phase status in the MVP Summary table
   - Add completion date
   - Commit the updated `PROGRESS.md`

### Example

```markdown
## Phase 0: Project Setup (Week 1)

### 0.1 Monorepo Initialization

- [x] Initialize Turborepo with pnpm workspaces
- [x] Create apps/web (Next.js 14)
- [ ] Create apps/api (Express.js + TypeScript)  <-- Currently working on
...

**Phase 0 Completed:** 2026-01-20
```

## Session Management

### After Each Phase

When completing a phase (Phase 0, Phase 1, etc. from WORKFLOW_v2.md):

1. Ensure all phase deliverables are committed and pushed
2. Verify the phase quality gate checklist in `PROGRESS.md`
3. Update `PROGRESS.md` with completion status and date
4. Commit the progress update
5. **Run `/compact` to summarize the conversation** before starting the next phase

This keeps context manageable and ensures important decisions are preserved in the summary.

## Docker in Development vs Production

| Aspect | Development | Production |
|--------|-------------|------------|
| Source code | Volume mounted (hot reload) | Baked into image |
| Dockerfile target | `development` | `production` |
| Database | Local container | Railway managed |
| Environment vars | docker-compose.yml | Railway dashboard |

## Related Documentation

- `PROGRESS.md` - Live implementation progress tracker (update as you work)
- `WORKFLOW_v2.md` - Detailed implementation phases and task breakdown
- `claudedocs/PRD_permitto_v2.md` - Full product requirements, data model, API endpoints
- `claudedocs/DOCKER_CICD_GUIDE.md` - Docker and CI/CD deep dive
