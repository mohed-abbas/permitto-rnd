# Permitto Implementation Workflow

**Generated:** January 15, 2026
**Strategy:** Systematic | **Depth:** Deep
**Source:** PRD v1.0

---

## Executive Summary

This workflow provides a comprehensive, phase-by-phase implementation plan for the Permitto Permit To Work (PTW) application. The system replaces paper-based permit management with a digital workflow supporting 4 user roles, multi-step approval chains, daily re-approvals, and complete audit trails.

**Target MVP:** 6 weeks
**Architecture:** Next.js 14 (App Router) + Prisma + PostgreSQL + shadcn/ui

---

## Tech Stack Confirmation

| Layer | Technology | Version |
|-------|------------|---------|
| Framework | Next.js (App Router) | 14.x+ |
| Language | TypeScript | 5.x |
| UI Components | shadcn/ui | latest |
| Styling | Tailwind CSS | 3.x |
| Forms | React Hook Form + Zod | 7.x / 3.x |
| State | Zustand | 4.x |
| Auth | NextAuth.js (Auth.js) | 5.x |
| ORM | Prisma | 5.x |
| Database | PostgreSQL | 15+ |
| Email | Resend (or SendGrid) | latest |
| PDF | @react-pdf/renderer | 3.x |
| Signatures | react-signature-canvas | 1.x |
| Hosting | Vercel + Railway | - |

---

## Project Structure

```
permitto/
├── prisma/
│   ├── schema.prisma
│   ├── migrations/
│   └── seed.ts
├── src/
│   ├── app/
│   │   ├── (auth)/
│   │   │   ├── login/
│   │   │   ├── register/
│   │   │   └── forgot-password/
│   │   ├── (dashboard)/
│   │   │   ├── layout.tsx
│   │   │   ├── page.tsx
│   │   │   ├── projects/
│   │   │   ├── users/
│   │   │   ├── templates/
│   │   │   ├── permits/
│   │   │   └── notifications/
│   │   ├── api/
│   │   │   ├── auth/[...nextauth]/
│   │   │   ├── users/
│   │   │   ├── projects/
│   │   │   ├── templates/
│   │   │   ├── permits/
│   │   │   └── notifications/
│   │   ├── layout.tsx
│   │   └── globals.css
│   ├── components/
│   │   ├── ui/                 # shadcn/ui components
│   │   ├── forms/              # Form components
│   │   ├── permits/            # Permit-specific components
│   │   ├── templates/          # Template builder components
│   │   └── layout/             # Layout components
│   ├── lib/
│   │   ├── prisma.ts
│   │   ├── auth.ts
│   │   ├── permissions.ts
│   │   ├── permit-state-machine.ts
│   │   ├── pdf-generator.ts
│   │   └── notifications.ts
│   ├── hooks/
│   │   ├── use-permission.ts
│   │   └── use-notifications.ts
│   ├── stores/
│   │   └── notification-store.ts
│   └── types/
│       ├── form-schema.ts
│       └── permissions.ts
├── public/
├── .env.local
├── .env.example
├── package.json
└── tsconfig.json
```

---

## Phase 1: Foundation (Week 1-2)

### 1.1 Project Initialization

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 1.1.1 | Initialize Next.js 14 with App Router, TypeScript strict mode | P0 | - |
| 1.1.2 | Configure ESLint (strict), Prettier, Husky pre-commit hooks | P0 | 1.1.1 |
| 1.1.3 | Set up Tailwind CSS with custom theme (colors, spacing) | P0 | 1.1.1 |
| 1.1.4 | Install and configure shadcn/ui (button, input, card, dialog, table, toast, dropdown) | P0 | 1.1.3 |
| 1.1.5 | Configure path aliases (@/components, @/lib, @/types) | P0 | 1.1.1 |
| 1.1.6 | Set up environment variables structure (.env.local, .env.example) | P0 | 1.1.1 |

**Deliverables:**
- [ ] Next.js app runs locally with TypeScript
- [ ] shadcn/ui components available
- [ ] ESLint/Prettier configured with pre-commit hooks

---

### 1.2 Database Setup

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 1.2.1 | Create Prisma schema from PRD data model (all 9 models) | P0 | 1.1.1 |
| 1.2.2 | Set up PostgreSQL on Railway (dev environment) | P0 | - |
| 1.2.3 | Configure Prisma client with connection pooling | P0 | 1.2.1, 1.2.2 |
| 1.2.4 | Run initial migration | P0 | 1.2.3 |
| 1.2.5 | Create seed script with demo users (1 per role), 2 projects, sample templates | P1 | 1.2.4 |
| 1.2.6 | Add database indexes for common queries (permitNumber, status, projectId) | P1 | 1.2.4 |

**Prisma Models to Implement:**
1. `User` - with role enum (SUPER_ADMIN, COORDINATOR, ISSUER, RECEIVER)
2. `Project` - with unique code for permit ID prefix
3. `ProjectUser` - many-to-many with assigner tracking
4. `PermitTemplate` - JSON schema storage
5. `Permit` - core permit with status enum
6. `DailyApproval` - daily re-approval tracking
7. `PermitAuditLog` - immutable action log
8. `Notification` - in-app notifications
9. `PermitSequence` - permit number generation

**Deliverables:**
- [ ] Prisma schema matches PRD data model
- [ ] Database running on Railway
- [ ] Migrations applied successfully
- [ ] Seed script populates demo data

---

### 1.3 Authentication

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 1.3.1 | Install and configure NextAuth.js v5 with credentials provider | P0 | 1.2.4 |
| 1.3.2 | Create auth configuration (session strategy: JWT) | P0 | 1.3.1 |
| 1.3.3 | Build login page with email/password form | P0 | 1.3.2 |
| 1.3.4 | Build registration page (requires invite token) | P0 | 1.3.2 |
| 1.3.5 | Implement password reset flow (request + reset pages) | P1 | 1.3.2 |
| 1.3.6 | Add session persistence across browser refresh | P0 | 1.3.2 |
| 1.3.7 | Create auth middleware for protected routes | P0 | 1.3.2 |

**Auth Flow:**
```
Login → Validate credentials → Create JWT → Store in httpOnly cookie
Refresh → Validate JWT → Extend session
Logout → Clear cookie → Redirect to login
```

**Deliverables:**
- [ ] Users can login with email/password
- [ ] Sessions persist across refresh
- [ ] Protected routes redirect to login
- [ ] Password reset emails send (stub for now)

---

### 1.4 Role-Based Access Control (RBAC)

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 1.4.1 | Define permission constants and TypeScript types | P0 | 1.3.2 |
| 1.4.2 | Create `checkPermission(user, action, resource?)` utility | P0 | 1.4.1 |
| 1.4.3 | Build `withPermission` API route middleware | P0 | 1.4.2 |
| 1.4.4 | Create `usePermission` React hook for UI | P0 | 1.4.2 |
| 1.4.5 | Implement role-based route protection in middleware.ts | P0 | 1.4.2 |
| 1.4.6 | Add permission checks to all API routes (as they're built) | P0 | 1.4.3 |

**Permission Matrix Implementation:**
```typescript
// permissions.ts
export const PERMISSIONS = {
  // Projects
  'project:create': ['SUPER_ADMIN'],
  'project:read': ['SUPER_ADMIN', 'COORDINATOR', 'ISSUER', 'RECEIVER'],
  'project:update': ['SUPER_ADMIN'],

  // Users
  'user:create:any': ['SUPER_ADMIN'],
  'user:create:issuer': ['SUPER_ADMIN', 'COORDINATOR'],
  'user:create:receiver': ['SUPER_ADMIN', 'COORDINATOR', 'ISSUER'],

  // Templates
  'template:create': ['COORDINATOR'],
  'template:edit': ['COORDINATOR'],

  // Permits
  'permit:request': ['RECEIVER'],
  'permit:approve:issuer': ['ISSUER'],
  'permit:approve:coordinator': ['COORDINATOR'],
  'permit:close': ['COORDINATOR'],

  // ... etc
} as const;
```

**Deliverables:**
- [ ] Permission system enforces role restrictions
- [ ] API routes protected by middleware
- [ ] UI hides inaccessible features
- [ ] Tests confirm permission enforcement

---

### 1.5 Base Layout & Navigation

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 1.5.1 | Create app shell layout (responsive sidebar + header) | P0 | 1.1.4 |
| 1.5.2 | Build sidebar navigation with role-based menu items | P0 | 1.5.1, 1.4.4 |
| 1.5.3 | Create mobile navigation (hamburger menu, bottom nav) | P0 | 1.5.1 |
| 1.5.4 | Build header with user menu, notification bell | P0 | 1.5.1 |
| 1.5.5 | Create role-specific dashboard home pages (4 variants) | P0 | 1.5.1, 1.4.4 |
| 1.5.6 | Add loading states and skeleton components | P1 | 1.5.1 |

**Navigation Structure by Role:**
```
Super Admin: Dashboard, Projects, Users, Settings
Coordinator: Dashboard, Permits (all), Templates, Users, Reports
Issuer: Dashboard, Pending Approvals, My Approvals, Daily Tasks
Receiver: Dashboard, My Permits, New Request, Daily Tasks
```

**Deliverables:**
- [ ] Responsive layout works on mobile (320px+) and desktop
- [ ] Navigation shows role-appropriate menu items
- [ ] Dashboard displays role-specific content
- [ ] Mobile navigation is thumb-friendly

---

### 1.6 User Management

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 1.6.1 | Create GET /api/users (list with pagination, role filter) | P0 | 1.4.3 |
| 1.6.2 | Create POST /api/users (create user, send invite email) | P0 | 1.4.3 |
| 1.6.3 | Create GET /api/users/[id] (user details) | P0 | 1.4.3 |
| 1.6.4 | Create PATCH /api/users/[id] (update user) | P0 | 1.4.3 |
| 1.6.5 | Create DELETE /api/users/[id] (deactivate, soft delete) | P1 | 1.4.3 |
| 1.6.6 | Build user list page with DataTable | P0 | 1.6.1 |
| 1.6.7 | Build create user form/modal | P0 | 1.6.2 |
| 1.6.8 | Build edit user form/modal | P0 | 1.6.4 |

**API Validation (Zod):**
```typescript
const createUserSchema = z.object({
  email: z.string().email(),
  name: z.string().min(2).max(100),
  role: z.enum(['COORDINATOR', 'ISSUER', 'RECEIVER']),
  phone: z.string().optional(),
});
```

**Deliverables:**
- [ ] Super Admin can create users of any role
- [ ] Coordinator can create Issuers and Receivers
- [ ] Issuer can create Receivers only
- [ ] User list shows filterable, paginated data
- [ ] Invite emails send on user creation

---

### 1.7 Project Management

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 1.7.1 | Create GET /api/projects (list, with user's assignments) | P0 | 1.4.3 |
| 1.7.2 | Create POST /api/projects (Super Admin only) | P0 | 1.4.3 |
| 1.7.3 | Create GET /api/projects/[id] (details + assigned users) | P0 | 1.4.3 |
| 1.7.4 | Create PATCH /api/projects/[id] (update name, status) | P0 | 1.4.3 |
| 1.7.5 | Create POST /api/projects/[id]/users (assign user) | P0 | 1.4.3 |
| 1.7.6 | Create DELETE /api/projects/[id]/users/[userId] (unassign) | P0 | 1.4.3 |
| 1.7.7 | Build project list page | P0 | 1.7.1 |
| 1.7.8 | Build project detail page with user assignment UI | P0 | 1.7.3, 1.7.5 |
| 1.7.9 | Build create/edit project modal | P0 | 1.7.2, 1.7.4 |

**Deliverables:**
- [ ] Super Admin can create projects with unique codes
- [ ] Users can be assigned to projects (respecting role permissions)
- [ ] Users only see their assigned projects (except Super Admin/Coordinator)
- [ ] Project codes are validated as unique

---

### Phase 1 Quality Gate

Before proceeding to Phase 2, verify:

- [ ] **Auth:** Login/logout works, sessions persist
- [ ] **RBAC:** Permissions enforced at API and UI levels
- [ ] **Users:** CRUD operations work with role restrictions
- [ ] **Projects:** CRUD + user assignment works
- [ ] **Mobile:** Layout responsive down to 320px
- [ ] **Database:** Seed data loads, queries perform well
- [ ] **Code Quality:** No TypeScript errors, ESLint passes

---

## Phase 2: Template System (Week 3)

### 2.1 Form Schema Types

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 2.1.1 | Create FormSchema TypeScript interfaces (from PRD section 7.3) | P0 | Phase 1 |
| 2.1.2 | Create field type definitions with validation rules | P0 | 2.1.1 |
| 2.1.3 | Create Zod schemas for form schema validation | P0 | 2.1.1 |
| 2.1.4 | Create utility functions for schema manipulation | P1 | 2.1.1 |

**Deliverables:**
- [ ] Type-safe form schema interfaces
- [ ] All 10 field types defined
- [ ] Validation rules typed

---

### 2.2 Template API

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 2.2.1 | Create GET /api/templates (list with type filter) | P0 | 2.1.3 |
| 2.2.2 | Create POST /api/templates (Coordinator only) | P0 | 2.1.3 |
| 2.2.3 | Create GET /api/templates/[id] | P0 | 2.1.3 |
| 2.2.4 | Create PATCH /api/templates/[id] (creates new version) | P0 | 2.1.3 |
| 2.2.5 | Create POST /api/templates/[id]/publish | P1 | 2.2.4 |
| 2.2.6 | Create POST /api/templates/[id]/archive | P1 | 2.2.4 |

**Deliverables:**
- [ ] Templates can be created, updated, published, archived
- [ ] Only Coordinators can manage templates
- [ ] Version history tracked

---

### 2.3 Template Builder UI

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 2.3.1 | Create template list page with type tabs | P0 | 2.2.1 |
| 2.3.2 | Build template builder page layout (canvas + sidebar) | P0 | 2.3.1 |
| 2.3.3 | Create draggable field palette (all 10 field types) | P0 | 2.3.2 |
| 2.3.4 | Implement step/section management | P0 | 2.3.2 |
| 2.3.5 | Build field property editor panel | P0 | 2.3.3 |
| 2.3.6 | Implement drag-and-drop field reordering | P1 | 2.3.3 |
| 2.3.7 | Add conditional field logic UI | P1 | 2.3.5 |
| 2.3.8 | Create template metadata editor (validity options, signatures) | P0 | 2.3.2 |

**Deliverables:**
- [ ] Visual template builder works
- [ ] All field types can be added and configured
- [ ] Multi-step forms can be created
- [ ] Drag-and-drop works for reordering

---

### 2.4 Template Preview

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 2.4.1 | Create template preview component (renders schema as form) | P0 | 2.3.8 |
| 2.4.2 | Add preview mode toggle in builder | P0 | 2.4.1 |
| 2.4.3 | Build full-page preview with step navigation | P0 | 2.4.1 |

**Deliverables:**
- [ ] Template can be previewed before publishing
- [ ] Preview matches final permit form appearance

---

### 2.5 Default Templates

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 2.5.1 | Create Hot Work (HW) permit template schema | P0 | 2.4.3 |
| 2.5.2 | Create Confined Space Entry (CSE) template schema | P0 | 2.4.3 |
| 2.5.3 | Create Working at Height (WAH) template schema | P0 | 2.4.3 |
| 2.5.4 | Create Electrical Work (EW) template schema | P0 | 2.4.3 |
| 2.5.5 | Add templates to seed script | P0 | 2.5.1-2.5.4 |

**Hot Work Template Structure (Example):**
```
Step 1: General Information
  - Work Location (text, required)
  - Description of Work (textarea, required)
  - Start Date (date, required)
  - End Date (date, required)
  - Number of Workers (number, required)

Step 2: Hazard Assessment
  - Identified Hazards (checkbox-group: fire, fumes, hot_surfaces, sparks)
  - Additional Hazards (textarea, optional)

Step 3: Safety Compliance
  - Fire extinguisher present (checkbox, required)
  - Fire watch assigned (checkbox, required)
  - Ventilation adequate (checkbox, required)
  - Combustibles removed (checkbox, required)
  - PPE requirements met (checkbox, required)
```

**Deliverables:**
- [ ] 4 default templates created with realistic fields
- [ ] Templates seeded on fresh database setup
- [ ] Templates follow industry-standard permit structures

---

### Phase 2 Quality Gate

Before proceeding to Phase 3, verify:

- [ ] **Template Builder:** Can create multi-step templates with all field types
- [ ] **Template CRUD:** Create, update, publish, archive all work
- [ ] **Preview:** Template preview matches form rendering
- [ ] **Permissions:** Only Coordinators can create/edit templates
- [ ] **Seed Data:** 4 default templates load correctly

---

## Phase 3: Core Permit Workflow (Week 4)

### 3.1 Multi-Step Form Renderer

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 3.1.1 | Create DynamicForm component that renders from schema | P0 | Phase 2 |
| 3.1.2 | Implement all 10 field type renderers | P0 | 3.1.1 |
| 3.1.3 | Add step navigation (progress indicator, prev/next) | P0 | 3.1.1 |
| 3.1.4 | Implement per-step validation with error display | P0 | 3.1.2 |
| 3.1.5 | Handle conditional field visibility | P1 | 3.1.2 |

**Deliverables:**
- [ ] Form renders correctly from any valid schema
- [ ] All field types work with validation
- [ ] Step navigation is intuitive

---

### 3.2 Draft Persistence

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 3.2.1 | Create POST /api/permits (create draft) | P0 | 3.1.1 |
| 3.2.2 | Create PATCH /api/permits/[id] (update draft) | P0 | 3.2.1 |
| 3.2.3 | Implement auto-save (debounced, every 30 seconds) | P0 | 3.2.2 |
| 3.2.4 | Create draft list view for Receivers | P0 | 3.2.1 |
| 3.2.5 | Add resume draft functionality | P0 | 3.2.4 |
| 3.2.6 | Create DELETE /api/permits/[id] (delete draft only) | P1 | 3.2.1 |

**Deliverables:**
- [ ] Drafts auto-save as user fills form
- [ ] Receivers can resume drafts later
- [ ] Draft deletion works (before submission only)

---

### 3.3 Permit Submission

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 3.3.1 | Create permit preview page (PDF-like view) | P0 | 3.2.3 |
| 3.3.2 | Create POST /api/permits/[id]/submit | P0 | 3.3.1 |
| 3.3.3 | Implement permit number generation (PROJECT-TYPE-YEAR-SEQ) | P0 | 3.3.2 |
| 3.3.4 | Add submission confirmation dialog | P0 | 3.3.2 |
| 3.3.5 | Create audit log entry on submit | P0 | 3.3.2 |
| 3.3.6 | Trigger notification to Issuer(s) on submit | P0 | 3.3.2 |

**Permit Number Format:**
```
DOWNTOWN-HW-2026-001
└─ Project ─┴Type┴Year┴─Sequence (per project/type/year)
```

**Deliverables:**
- [ ] Permit can be previewed before submission
- [ ] Submission generates unique permit number
- [ ] Status changes to SUBMITTED
- [ ] Issuer notified

---

### 3.4 Issuer Approval

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 3.4.1 | Create pending approvals dashboard for Issuers | P0 | 3.3.6 |
| 3.4.2 | Create permit detail view (read-only form data) | P0 | 3.4.1 |
| 3.4.3 | Create POST /api/permits/[id]/approve (Issuer) | P0 | 3.4.2 |
| 3.4.4 | Create POST /api/permits/[id]/reject | P0 | 3.4.2 |
| 3.4.5 | Add signature capture component | P0 | 3.4.3 |
| 3.4.6 | Require signature for approval | P0 | 3.4.5 |
| 3.4.7 | Add rejection reason textarea (required) | P0 | 3.4.4 |
| 3.4.8 | Create audit log entries for approve/reject | P0 | 3.4.3, 3.4.4 |
| 3.4.9 | Notify Coordinator on Issuer approval | P0 | 3.4.3 |
| 3.4.10 | Notify Receiver on rejection | P0 | 3.4.4 |

**Deliverables:**
- [ ] Issuer sees pending permits
- [ ] Issuer can approve with digital signature
- [ ] Issuer can reject with reason
- [ ] Status changes appropriately
- [ ] Notifications sent

---

### 3.5 Coordinator Final Approval

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 3.5.1 | Create Coordinator approval queue (ISSUER_APPROVED permits) | P0 | 3.4.9 |
| 3.5.2 | Show Issuer signature on permit detail view | P0 | 3.5.1 |
| 3.5.3 | Create POST /api/permits/[id]/approve (Coordinator) | P0 | 3.5.2 |
| 3.5.4 | Create POST /api/permits/[id]/reject (Coordinator) | P0 | 3.5.2 |
| 3.5.5 | Require Coordinator signature for final approval | P0 | 3.5.3 |
| 3.5.6 | On final approval: status → APPROVED, permit → Active | P0 | 3.5.3 |
| 3.5.7 | Notify all parties (Receiver, Issuer) on final approval | P0 | 3.5.6 |
| 3.5.8 | On rejection: return to Receiver (can revise) | P0 | 3.5.4 |

**Deliverables:**
- [ ] Coordinator sees Issuer-approved permits
- [ ] Coordinator can give final approval with signature
- [ ] Coordinator can reject (restarts workflow)
- [ ] All parties notified on final decision

---

### 3.6 Signature Capture

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 3.6.1 | Install react-signature-canvas | P0 | - |
| 3.6.2 | Create SignatureCapture component | P0 | 3.6.1 |
| 3.6.3 | Add clear/redo functionality | P0 | 3.6.2 |
| 3.6.4 | Save signature as base64 PNG | P0 | 3.6.2 |
| 3.6.5 | Display captured signatures (read-only mode) | P0 | 3.6.4 |
| 3.6.6 | Ensure mobile touch support | P0 | 3.6.2 |

**Deliverables:**
- [ ] Signature capture works on desktop and mobile
- [ ] Signatures saved and displayed correctly
- [ ] Canvas sized appropriately (not too small on mobile)

---

### 3.7 Permit State Machine

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 3.7.1 | Create PermitStateMachine service | P0 | Phase 1 |
| 3.7.2 | Define all state transitions (from PRD section 6.1) | P0 | 3.7.1 |
| 3.7.3 | Implement transition validation (allowed next states) | P0 | 3.7.2 |
| 3.7.4 | Add transition side effects (notifications, logs) | P0 | 3.7.3 |
| 3.7.5 | Create getPermitActions(permit, user) utility | P0 | 3.7.3 |
| 3.7.6 | Write unit tests for state machine | P0 | 3.7.5 |

**State Transitions:**
```typescript
const TRANSITIONS: Record<PermitStatus, PermitStatus[]> = {
  DRAFT: ['SUBMITTED', 'CANCELLED'],
  SUBMITTED: ['ISSUER_APPROVED', 'REJECTED_ISSUER'],
  ISSUER_APPROVED: ['APPROVED', 'REJECTED_COORD'],
  REJECTED_ISSUER: ['DRAFT'],
  REJECTED_COORD: ['DRAFT'],
  APPROVED: ['PENDING_DAILY', 'SUSPENDED', 'PENDING_CLOSEOUT'],
  PENDING_DAILY: ['ACTIVE', 'SUSPENDED'],
  ACTIVE: ['PENDING_DAILY', 'PENDING_CLOSEOUT'],
  SUSPENDED: ['ACTIVE'],
  PENDING_CLOSEOUT: ['CLOSED'],
  CLOSED: [],
  CANCELLED: [],
};
```

**Deliverables:**
- [ ] State machine enforces valid transitions
- [ ] Invalid transitions throw errors
- [ ] Side effects trigger correctly
- [ ] Unit tests cover all transitions

---

### Phase 3 Quality Gate

Before proceeding to Phase 4, verify:

- [ ] **Full Workflow:** Permit flows from DRAFT → SUBMITTED → ISSUER_APPROVED → APPROVED
- [ ] **Rejections:** Both Issuer and Coordinator rejection flows work
- [ ] **Signatures:** Digital signatures captured and displayed
- [ ] **Notifications:** All parties notified at each step
- [ ] **Audit Log:** All status changes logged
- [ ] **Mobile:** Signature capture works on touch devices

---

## Phase 4: Daily Operations & Notifications (Week 5)

### 4.1 Daily Approval Records

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 4.1.1 | Create daily approval record on permit activation | P0 | Phase 3 |
| 4.1.2 | Create GET /api/permits/[id]/daily-approvals | P0 | 4.1.1 |
| 4.1.3 | Create POST /api/permits/[id]/daily-approve | P0 | 4.1.1 |
| 4.1.4 | Implement approval order enforcement (Receiver → Issuer → Coord) | P0 | 4.1.3 |
| 4.1.5 | Track timestamp for each party's approval | P0 | 4.1.3 |

**Deliverables:**
- [ ] Daily approval records created for each active permit day
- [ ] Approval order enforced
- [ ] Timestamps recorded

---

### 4.2 Daily Re-Approval UI

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 4.2.1 | Create "Daily Tasks" dashboard section (all roles) | P0 | 4.1.3 |
| 4.2.2 | Show pending daily approvals prominently | P0 | 4.2.1 |
| 4.2.3 | Implement one-tap approve button | P0 | 4.2.2 |
| 4.2.4 | Implement bulk approve (select multiple, approve all) | P1 | 4.2.3 |
| 4.2.5 | Show approval status indicators (who has approved) | P0 | 4.2.2 |
| 4.2.6 | Add countdown timer to 8:00 AM deadline | P1 | 4.2.2 |

**Deliverables:**
- [ ] Users see pending daily approvals
- [ ] One-tap approval works
- [ ] Bulk approval available
- [ ] Clear status indicators

---

### 4.3 Scheduled Jobs

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 4.3.1 | Set up Vercel Cron for scheduled jobs | P0 | - |
| 4.3.2 | Create 7:00 AM daily reminder job | P0 | 4.3.1 |
| 4.3.3 | Create 8:05 AM suspension check job | P0 | 4.3.1 |
| 4.3.4 | Create 12:00 AM expiry warning job (24h before) | P0 | 4.3.1 |
| 4.3.5 | Create 5:00 PM end-of-day processing job | P0 | 4.3.1 |
| 4.3.6 | Add job execution logging | P0 | 4.3.2-4.3.5 |

**Cron Schedule (vercel.json):**
```json
{
  "crons": [
    { "path": "/api/cron/daily-reminder", "schedule": "0 7 * * *" },
    { "path": "/api/cron/check-suspensions", "schedule": "5 8 * * *" },
    { "path": "/api/cron/expiry-warning", "schedule": "0 0 * * *" },
    { "path": "/api/cron/end-of-day", "schedule": "0 17 * * *" }
  ]
}
```

**Deliverables:**
- [ ] All 4 cron jobs configured
- [ ] Jobs execute reliably
- [ ] Job results logged

---

### 4.4 Suspension Logic

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 4.4.1 | Implement suspension check logic | P0 | 4.3.3 |
| 4.4.2 | Update permit status to SUSPENDED if any approval missed | P0 | 4.4.1 |
| 4.4.3 | Notify all parties on suspension | P0 | 4.4.2 |
| 4.4.4 | Create reactivation flow (complete pending approvals) | P0 | 4.4.2 |
| 4.4.5 | Show suspended permits prominently in dashboard | P0 | 4.4.2 |

**Deliverables:**
- [ ] Permits suspend at 8:00 AM if approvals missing
- [ ] All parties notified of suspension
- [ ] Suspended permits can be reactivated

---

### 4.5 Permit Close-Out

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 4.5.1 | Create PENDING_CLOSEOUT status transition on expiry | P0 | 4.3.5 |
| 4.5.2 | Create close-out queue for Coordinators | P0 | 4.5.1 |
| 4.5.3 | Create POST /api/permits/[id]/close | P0 | 4.5.2 |
| 4.5.4 | Add close-out notes field (required) | P0 | 4.5.3 |
| 4.5.5 | Require Coordinator signature for close-out | P0 | 4.5.3 |
| 4.5.6 | Create early close-out request flow (Receiver initiated) | P1 | 4.5.3 |
| 4.5.7 | Notify all parties on close-out | P0 | 4.5.3 |

**Deliverables:**
- [ ] Expired permits move to PENDING_CLOSEOUT
- [ ] Coordinator can close with notes and signature
- [ ] Early close-out available
- [ ] Final notifications sent

---

### 4.6 In-App Notifications

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 4.6.1 | Create notification creation service | P0 | Phase 1 |
| 4.6.2 | Build notification bell component with unread count | P0 | 4.6.1 |
| 4.6.3 | Create notification dropdown/panel | P0 | 4.6.2 |
| 4.6.4 | Implement mark as read (single, all) | P0 | 4.6.3 |
| 4.6.5 | Add click-to-navigate (to relevant permit) | P0 | 4.6.3 |
| 4.6.6 | Create GET /api/notifications (paginated) | P0 | 4.6.1 |
| 4.6.7 | Create PATCH /api/notifications/[id]/read | P0 | 4.6.4 |
| 4.6.8 | Create POST /api/notifications/read-all | P0 | 4.6.4 |

**Notification Types:**
```typescript
type NotificationType =
  | 'permit_submitted'
  | 'permit_approved_issuer'
  | 'permit_approved_final'
  | 'permit_rejected_issuer'
  | 'permit_rejected_coord'
  | 'daily_reminder'
  | 'permit_suspended'
  | 'permit_expiry_warning'
  | 'permit_closed';
```

**Deliverables:**
- [ ] In-app notifications work
- [ ] Unread count visible
- [ ] Click navigates to permit
- [ ] Mark as read works

---

### 4.7 Email Notifications

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 4.7.1 | Set up Resend (or SendGrid) integration | P0 | - |
| 4.7.2 | Create email templates for all notification types | P0 | 4.7.1 |
| 4.7.3 | Create sendEmail utility function | P0 | 4.7.1, 4.7.2 |
| 4.7.4 | Integrate email sending into notification service | P0 | 4.7.3 |
| 4.7.5 | Add user email preference (can disable) | P1 | 4.7.4 |
| 4.7.6 | Create daily digest email (morning re-approvals) | P0 | 4.7.4 |

**Email Templates to Create:**
1. Permit Submitted (→ Issuer)
2. Permit Approved by Issuer (→ Receiver, Coordinator)
3. Permit Rejected by Issuer (→ Receiver)
4. Permit Final Approved (→ All)
5. Permit Rejected by Coordinator (→ Receiver, Issuer)
6. Daily Re-Approval Reminder (→ All with pending)
7. Permit Expiry Warning (→ All)
8. Permit Suspended (→ All)
9. Permit Closed (→ All)

**Deliverables:**
- [ ] Email integration works
- [ ] All template types implemented
- [ ] Emails send at correct trigger points
- [ ] User preferences respected

---

### Phase 4 Quality Gate

Before proceeding to Phase 5, verify:

- [ ] **Daily Cycle:** Re-approval flow works end-to-end
- [ ] **Suspension:** Permits suspend correctly at deadline
- [ ] **Close-Out:** Coordinators can close permits
- [ ] **Notifications:** In-app notifications appear correctly
- [ ] **Emails:** All email types send with correct content
- [ ] **Cron Jobs:** Scheduled jobs execute reliably

---

## Phase 5: PDF, Polish & Deployment (Week 6)

### 5.1 PDF Generation

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 5.1.1 | Install @react-pdf/renderer | P0 | - |
| 5.1.2 | Create PDF layout matching PRD Appendix D | P0 | 5.1.1 |
| 5.1.3 | Render all form data dynamically | P0 | 5.1.2 |
| 5.1.4 | Include signatures as images | P0 | 5.1.2 |
| 5.1.5 | Generate QR code linking to digital permit | P0 | 5.1.2 |
| 5.1.6 | Add company header/branding | P0 | 5.1.2 |
| 5.1.7 | Create GET /api/permits/[id]/pdf endpoint | P0 | 5.1.5 |
| 5.1.8 | Add download button to permit detail view | P0 | 5.1.7 |
| 5.1.9 | Test PDF on various permit types | P0 | 5.1.8 |

**PDF Sections:**
1. Header (Company logo, permit type, number)
2. Validity (dates, daily hours)
3. Permit Details (all form fields)
4. Compliance Checklist
5. Approvals (3 signature boxes with names/timestamps)
6. QR Code + generation timestamp

**Deliverables:**
- [ ] PDF generates correctly for all permit types
- [ ] Signatures render properly
- [ ] QR code links to digital permit
- [ ] Print-optimized (A4)

---

### 5.2 Push Notifications

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 5.2.1 | Set up Web Push API with VAPID keys | P0 | - |
| 5.2.2 | Create push subscription flow on first login | P0 | 5.2.1 |
| 5.2.3 | Store push subscriptions in database | P0 | 5.2.2 |
| 5.2.4 | Create sendPush utility function | P0 | 5.2.3 |
| 5.2.5 | Integrate push into notification service | P0 | 5.2.4 |
| 5.2.6 | Add user push preference (can disable) | P1 | 5.2.5 |
| 5.2.7 | Test push on mobile browsers | P0 | 5.2.5 |

**Deliverables:**
- [ ] Push subscription opt-in works
- [ ] Push notifications send on key events
- [ ] Works on mobile browsers
- [ ] User can disable in preferences

---

### 5.3 Mobile Refinements

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 5.3.1 | Audit all pages for mobile responsiveness (320px) | P0 | Phase 4 |
| 5.3.2 | Fix any overflow/layout issues | P0 | 5.3.1 |
| 5.3.3 | Optimize touch targets (48px minimum) | P0 | 5.3.1 |
| 5.3.4 | Test signature capture on various mobile devices | P0 | 5.3.1 |
| 5.3.5 | Optimize form field spacing for mobile | P0 | 5.3.1 |
| 5.3.6 | Test bottom navigation usability | P0 | 5.3.1 |

**Deliverables:**
- [ ] All pages work on 320px screens
- [ ] Touch targets are thumb-friendly
- [ ] Signature capture works on mobile
- [ ] No horizontal scroll issues

---

### 5.4 Audit Trail & Timeline

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 5.4.1 | Create GET /api/permits/[id]/timeline | P0 | Phase 3 |
| 5.4.2 | Build timeline component (vertical timeline) | P0 | 5.4.1 |
| 5.4.3 | Show all status changes with timestamps | P0 | 5.4.2 |
| 5.4.4 | Display rejection reasons in timeline | P0 | 5.4.2 |
| 5.4.5 | Add daily approval entries to timeline | P0 | 5.4.2 |
| 5.4.6 | Include who made each change | P0 | 5.4.2 |

**Deliverables:**
- [ ] Timeline shows complete permit history
- [ ] All status changes visible
- [ ] User attribution on each entry

---

### 5.5 Error Handling & Edge Cases

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 5.5.1 | Add global error boundary | P0 | Phase 4 |
| 5.5.2 | Create user-friendly error pages (404, 500) | P0 | 5.5.1 |
| 5.5.3 | Add API error handling with proper HTTP codes | P0 | 5.5.1 |
| 5.5.4 | Handle network failures gracefully | P0 | 5.5.1 |
| 5.5.5 | Add loading states to all data-fetching pages | P0 | 5.5.1 |
| 5.5.6 | Test edge cases (concurrent approvals, etc.) | P0 | 5.5.1 |
| 5.5.7 | Set up Sentry for error monitoring | P1 | 5.5.1 |

**Deliverables:**
- [ ] Errors handled gracefully
- [ ] User-friendly error messages
- [ ] Loading states on all pages
- [ ] Sentry captures errors

---

### 5.6 Testing

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 5.6.1 | Write unit tests for state machine | P0 | 3.7.6 |
| 5.6.2 | Write unit tests for permission utilities | P0 | 1.4.6 |
| 5.6.3 | Write integration tests for permit API | P0 | Phase 3 |
| 5.6.4 | Write E2E tests for critical flows (Playwright) | P0 | Phase 4 |
| 5.6.5 | Manual QA checklist execution | P0 | 5.6.4 |
| 5.6.6 | Cross-browser testing (Chrome, Safari, Firefox) | P0 | 5.6.5 |
| 5.6.7 | Mobile device testing | P0 | 5.6.5 |

**E2E Test Scenarios:**
1. Receiver submits permit → Issuer approves → Coordinator approves
2. Issuer rejects → Receiver revises → Resubmit
3. Daily re-approval flow (all 3 parties)
4. Permit expiry and close-out
5. Login/logout flow
6. User creation by different roles

**Deliverables:**
- [ ] Unit tests pass (>80% coverage on critical paths)
- [ ] E2E tests pass for critical flows
- [ ] Manual QA completed
- [ ] No critical bugs remaining

---

### 5.7 Deployment & Documentation

| Task ID | Task | Priority | Dependencies |
|---------|------|----------|--------------|
| 5.7.1 | Set up Vercel production deployment | P0 | 5.6.7 |
| 5.7.2 | Set up Railway production database | P0 | 5.7.1 |
| 5.7.3 | Configure environment variables (production) | P0 | 5.7.2 |
| 5.7.4 | Set up custom domain (if applicable) | P1 | 5.7.3 |
| 5.7.5 | Create deployment documentation | P0 | 5.7.3 |
| 5.7.6 | Create user guide document | P1 | 5.7.5 |
| 5.7.7 | Create admin setup guide | P1 | 5.7.5 |
| 5.7.8 | Final production smoke test | P0 | 5.7.5 |

**Deliverables:**
- [ ] Production deployment live
- [ ] All environment variables configured
- [ ] Documentation completed
- [ ] Smoke test passed

---

### Phase 5 Quality Gate (Final MVP)

Before declaring MVP complete, verify:

- [ ] **PDF:** Generates correctly with all data and signatures
- [ ] **Push:** Notifications work on mobile browsers
- [ ] **Mobile:** All features work on 320px+ screens
- [ ] **Timeline:** Complete audit trail visible
- [ ] **Errors:** Handled gracefully with monitoring
- [ ] **Tests:** All critical tests pass
- [ ] **Production:** Deployed and accessible
- [ ] **Documentation:** User guides available

---

## Dependency Graph

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                            DEPENDENCY FLOW                                   │
└─────────────────────────────────────────────────────────────────────────────┘

Phase 1 (Foundation)
├─ 1.1 Project Setup ────────────────────────────────────────────────────────┐
├─ 1.2 Database Setup ──────────────┐                                        │
├─ 1.3 Authentication ──────────────┼── Depends on 1.1, 1.2                  │
├─ 1.4 RBAC ────────────────────────┼── Depends on 1.3                       │
├─ 1.5 Layout ──────────────────────┼── Depends on 1.1, 1.4                  │
├─ 1.6 User Management ─────────────┼── Depends on 1.4                       │
└─ 1.7 Project Management ──────────┴── Depends on 1.4, 1.6                  │
                                        │
Phase 2 (Templates)                     │
├─ 2.1 Schema Types ────────────────────┴── Depends on Phase 1               │
├─ 2.2 Template API ─────────────────────── Depends on 2.1                   │
├─ 2.3 Template Builder ─────────────────── Depends on 2.2                   │
├─ 2.4 Template Preview ─────────────────── Depends on 2.3                   │
└─ 2.5 Default Templates ────────────────── Depends on 2.4                   │
                                            │
Phase 3 (Core Workflow)                     │
├─ 3.1 Form Renderer ───────────────────────┴── Depends on Phase 2           │
├─ 3.2 Draft Persistence ────────────────────── Depends on 3.1               │
├─ 3.3 Permit Submission ────────────────────── Depends on 3.2               │
├─ 3.4 Issuer Approval ──────────────────────── Depends on 3.3               │
├─ 3.5 Coordinator Approval ─────────────────── Depends on 3.4               │
├─ 3.6 Signature Capture ────────────────────── Parallel with 3.3-3.5        │
└─ 3.7 State Machine ────────────────────────── Foundation for 3.3-3.5       │
                                                │
Phase 4 (Daily & Notifications)                 │
├─ 4.1 Daily Records ───────────────────────────┴── Depends on Phase 3       │
├─ 4.2 Daily Re-Approval UI ─────────────────────── Depends on 4.1           │
├─ 4.3 Scheduled Jobs ───────────────────────────── Depends on 4.1           │
├─ 4.4 Suspension Logic ─────────────────────────── Depends on 4.3           │
├─ 4.5 Close-Out ────────────────────────────────── Depends on 4.3           │
├─ 4.6 In-App Notifications ─────────────────────── Parallel with 4.1-4.5    │
└─ 4.7 Email Notifications ──────────────────────── Parallel with 4.1-4.5    │
                                                    │
Phase 5 (Polish)                                    │
├─ 5.1 PDF Generation ──────────────────────────────┴── Depends on Phase 4   │
├─ 5.2 Push Notifications ───────────────────────────── Parallel             │
├─ 5.3 Mobile Refinements ───────────────────────────── Parallel             │
├─ 5.4 Audit Trail UI ───────────────────────────────── Parallel             │
├─ 5.5 Error Handling ───────────────────────────────── Parallel             │
├─ 5.6 Testing ──────────────────────────────────────── Depends on 5.1-5.5   │
└─ 5.7 Deployment ───────────────────────────────────── Depends on 5.6       │
```

---

## Risk Mitigation Checkpoints

### Checkpoint 1: End of Week 2
**Risk:** Foundation issues block everything
**Check:**
- [ ] Auth works end-to-end
- [ ] RBAC enforced correctly
- [ ] Database performant
- [ ] Mobile layout stable

### Checkpoint 2: End of Week 3
**Risk:** Template builder too complex
**Check:**
- [ ] Basic templates can be created
- [ ] Form preview works
- [ ] Schema storage correct

### Checkpoint 3: End of Week 4
**Risk:** Workflow complexity causes bugs
**Check:**
- [ ] Full approval chain works
- [ ] Signatures capture correctly
- [ ] State transitions valid

### Checkpoint 4: End of Week 5
**Risk:** Daily operations timing issues
**Check:**
- [ ] Cron jobs reliable
- [ ] Suspension logic correct
- [ ] Notifications send

### Checkpoint 5: End of Week 6
**Risk:** Polish items incomplete
**Check:**
- [ ] PDF quality acceptable
- [ ] Mobile ready
- [ ] Tests pass

---

## Task Count Summary

| Phase | Tasks | Priority P0 | Priority P1 |
|-------|-------|-------------|-------------|
| 1. Foundation | 40 | 35 | 5 |
| 2. Templates | 20 | 17 | 3 |
| 3. Core Workflow | 35 | 33 | 2 |
| 4. Daily & Notifications | 38 | 34 | 4 |
| 5. Polish | 42 | 35 | 7 |
| **Total** | **175** | **154** | **21** |

---

## Next Steps

1. **Initialize Project:** Run `npx create-next-app@latest permitto --typescript --tailwind --app`
2. **Begin Phase 1.1:** Set up tooling and shadcn/ui
3. **Create GitHub Repository:** Initialize with this workflow as tracking reference
4. **Set Up Project Board:** Import tasks into GitHub Projects or similar

---

*This workflow was generated from PRD v1.0 using systematic analysis with deep task decomposition.*
