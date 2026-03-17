# Sprint 1 — Working Prototype (Weeks 1-3)

**Goal:** Deliver a live staging URL with the full core PTW workflow operational — authentication, 5-role RBAC, permit lifecycle from draft to closed, daily re-approval, PDF generation, notifications, and audit trail.

**Payment Milestone:** $1,600 on delivery of working prototype on staging URL.

---

## 1.1 Project Infrastructure

**Ref:** Proposal Section 1.1 #1, Section 2.19

### Sub-Tasks

- [x] Initialize Turborepo monorepo with pnpm workspaces
- [x] Create `apps/web` (Next.js 14+ with App Router)
- [x] Create `apps/api` (Express.js + TypeScript)
- [x] Create `packages/shared` (types, constants, validators)
- [x] Configure TypeScript paths and references
- [x] Set up ESLint + Prettier (shared config)
- [x] Configure Husky pre-commit hooks
- [x] Create Dockerfile for `apps/api` (multi-stage: dev + prod)
- [x] Create Dockerfile for `apps/web` (multi-stage: dev + prod)
- [x] Create `docker-compose.yml` for development (PostgreSQL, Redis, API, Web)
- [x] Create `.env.example` with all required variables
- [x] Create GitHub Actions CI workflow (lint, typecheck, build, test)
- [x] Create staging deployment workflow
- [x] Create production deployment workflow

### Acceptance Criteria

- `pnpm dev` starts all services locally
- `docker-compose up` starts full stack
- CI pipeline passes on every PR

---

## 1.2 Database Design & Setup

**Ref:** Proposal Section 2.19 (Database)

### Sub-Tasks

- [x] Install Prisma ORM and configure
- [x] Design full relational schema: Users, Projects, Zones, Templates, Permits, DailyApprovals, AuditLogs, Notifications, Files
- [x] Run initial migration
- [x] Create Prisma client singleton
- [x] Create seed script with sample data

### Acceptance Criteria

- Prisma schema covers all entities from PRD
- Migrations apply cleanly
- Seed script creates usable test data

---

## 1.3 Authentication & Sessions

**Ref:** Proposal Section 2.1

### Sub-Tasks

- [x] Email/password registration (admin-invited)
- [x] Login endpoint returning JWT access + refresh tokens
- [x] JWT access tokens (15 min) + refresh tokens (7 days)
- [x] Refresh token rotation stored in Redis
- [x] Auth middleware (JWT validation on protected routes)
- [x] Password reset flow via email link
- [x] Logout endpoint (invalidate refresh token in Redis)

### Acceptance Criteria

- Login returns access + refresh tokens
- Refresh endpoint rotates tokens
- Protected routes reject invalid/expired tokens
- Password reset sends email and allows reset

---

## 1.4 RBAC — 5 Roles

**Ref:** Proposal Section 2.2

### Sub-Tasks

- [x] Define permissions matrix in `packages/shared`
- [x] Create RBAC middleware for API routes
- [x] Add role to JWT payload
- [x] Implement Super Admin role
- [x] Implement PTW-PC (Coordinator) role
- [x] Implement WPI (Issuer) role
- [x] Implement WPR (Receiver) role
- [ ] **Add HSSE Officer role** — 5th role with independent risk assessment verification permissions
- [ ] **Update permissions matrix** for HSSE Officer capabilities
- [ ] **Update Prisma schema** to include HSSE_OFFICER in Role enum
- [ ] **Update frontend** role-based UI visibility for HSSE Officer

### Dependencies

- Auth module must be complete

### Acceptance Criteria

- All 5 roles enforced at API level (middleware blocks unauthorized access)
- UI hides features based on user role
- HSSE Officer can perform 4th-level approval

---

## 1.5 User Management

**Ref:** Proposal Section 2.1

### Sub-Tasks

- [x] User CRUD API (create, list, get, update)
- [x] Role assignment on user creation
- [x] User deactivation (soft-delete — preserves historical records)
- [x] User reactivation
- [x] User list page (frontend)
- [x] User create/edit form modal (frontend)
- [ ] **User profile page** with editable personal details
- [ ] **Notification preferences** settings per user

### Acceptance Criteria

- Admins can create users with any role
- Deactivated users cannot login but records preserved
- Users can edit their own profile and notification preferences

---

## 1.6 Project & Zone Management

**Ref:** Proposal Section 2.3

### Sub-Tasks

- [x] Project CRUD API (create, list, get, update, activate/deactivate)
- [x] Unique project code generation (e.g., "AFIF2")
- [x] Zone management within projects (create, list, update)
- [x] User-project assignment API
- [x] Project list page (frontend)
- [x] Project detail page (frontend)
- [x] User assignment UI (frontend)
- [ ] **Zone-based scoping** — verify permits, users, workflows are properly scoped to zones

### Acceptance Criteria

- Projects have unique codes
- Zones divide projects into areas
- Users only see projects they're assigned to
- Permits are scoped to specific zones

---

## 1.7 One-Time Profile Signature

**Ref:** Proposal Section 2.1 (Digital Signature Setup), Section 2.7

### Sub-Tasks

- [x] Signature capture canvas component (frontend)
- [ ] **Profile signature setup flow** — user draws signature once during profile setup
- [ ] **Signature storage** — store signature securely in database (PostgreSQL), not as separate file
- [ ] **Signature reuse on approvals** — on "Approve" click, stored signature is auto-applied with fresh timestamp
- [ ] **Signature required gate** — block approvals if user hasn't set up their signature

### Dependencies

- User Management module must be complete

### Acceptance Criteria

- Users draw and save signature once during profile setup
- Stored signature is reused on all subsequent approvals (no redraw)
- Users without signatures are prompted to set up before first approval

---

## 1.8 Permit Template Builder

**Ref:** Proposal Section 2.5

### Sub-Tasks

- [x] Template CRUD API with versioning
- [x] Template builder page (frontend)
- [x] 14+ field types: text, textarea, number, date, time, select, multi-select, checkbox, radio, file upload, signature, checkbox_grid, yes_no_na
- [x] Multi-step sections with progress indication
- [x] Risk assessment checklist sections
- [x] Field properties: label, placeholder, required/optional, validation rules, help text
- [x] Template versioning (new versions don't break existing permits)
- [x] Template preview
- [x] Background color configuration per template
- [x] Activate/deactivate templates

### Acceptance Criteria

- Coordinators can build custom templates for 7 permit types
- Templates support all 14+ field types
- Versioning preserves existing permits when templates are updated
- Preview shows accurate form layout

---

## 1.9 Permit Request & Submission

**Ref:** Proposal Section 2.6

### Sub-Tasks

- [x] Dynamic form renderer for template fields
- [x] Multi-step form with progress indicator and navigation
- [x] File upload component (PDF, JPG, PNG, HEIC up to 25MB, max 10 per permit)
- [x] Draft management (save and resume drafts)
- [x] Risk assessment verification step before submission
- [x] Submit to WPI endpoint
- [ ] **Auto-save** during form filling (save on field blur/interval)
- [ ] **Permit ID generation** — format: `{PROJECT_CODE}-{TYPE}-{YEAR}-{SEQUENCE}`
- [ ] **Template selection** filtered by project and permit type

### Dependencies

- Template Builder must be complete
- File Storage must be complete

### Acceptance Criteria

- WPR can fill multi-step forms with all field types
- Drafts are auto-saved (no data loss on navigation)
- Files upload correctly with size/type validation
- Submission triggers notification to assigned WPI

---

## 1.10 4-Level Approval Workflow

**Ref:** Proposal Section 2.7

### Sub-Tasks

- [x] Permit state machine (Draft → Submitted → WPI_Approved → PC_Approved → Active)
- [x] Submit endpoint (WPR → WPI)
- [x] WPI (Issuer) approve/reject with stored signature
- [x] PTW-PC (Coordinator) approve/reject
- [x] Pending approvals dashboard per role
- [x] Approve/reject modals (frontend)
- [ ] **Add HSSE approval level** — 4th level after PTW-PC, before ACTIVE
- [ ] **Update state machine**: Draft → Submitted → WPI_Approved → PC_Approved → HSSE_Approved → ACTIVE
- [ ] **Request Additional Measures** — approvers can request specific measures (not just reject), WPR implements and resubmits
- [ ] **Additional Measures resubmit loop** — WPR revises and resubmits, goes back to the level that requested
- [ ] **Rejection with mandatory feedback** — comments required on rejection
- [ ] **One-click approval with stored signature** — stored profile signature auto-applied on approve click

### Dependencies

- RBAC with all 5 roles
- Profile Signature setup
- Notification system

### Acceptance Criteria

- Full 4-level chain: WPR → WPI → PTW-PC → HSSE → APPROVED
- Each level can approve, reject (with feedback), or request additional measures
- Stored signature auto-applied on approval
- All transitions logged in audit trail

---

## 1.11 Daily Re-Approval Cycle

**Ref:** Proposal Section 2.9

### Sub-Tasks

- [x] Daily approval records created on permit activation
- [x] Daily approve endpoint (single permit)
- [x] Daily approval UI
- [x] Signature/remarks fields on daily approval
- [ ] **Bulk approval UI** — approve multiple permits at once
- [ ] **Deadline enforcement** — configurable deadline (default 8:00 AM)
- [ ] **Reactivation flow** — suspended permits reactivated when all daily approvals complete

### Dependencies

- Approval workflow must be complete
- Background jobs for reminders/suspension

### Acceptance Criteria

- All parties with active permits receive daily reminders at 7:00 AM
- Permits not re-approved by 8:00 AM are automatically suspended
- Bulk approval works for efficiency
- Suspended permits can be reactivated

---

## 1.12 Permit Close-Out & Cancellation

**Ref:** Proposal Section 2.12

### Sub-Tasks

- [x] Close-out endpoint (PTW-PC closes with completion notes + signature)
- [x] Cancellation endpoint (separate path for terminated permits)
- [x] PTW Register auto-update on close-out/cancellation
- [ ] **Final PDF generation** on close-out reflecting completion details

### Acceptance Criteria

- PTW-PC can formally close permits with notes and signature
- Cancellation path for permits terminated due to issues
- PTW Register updated automatically
- PDF reflects close-out details

---

## 1.13 PTW Register

**Ref:** Proposal Section 2.4

### Sub-Tasks

- [x] Auto-registration of permits upon issuance
- [x] Search by permit number, project, zone, type, status, date range
- [x] Filter by status, type, project
- [x] Real-time status tracking
- [x] Automatic updates on state changes
- [ ] **Export** — export register data (CSV/Excel) for reporting and compliance

### Acceptance Criteria

- All permits auto-logged in register on issuance
- Search/filter by all key fields works
- Status updates in real-time on state changes
- Export functionality for compliance reporting

---

## 1.14 Basic SIMOPs Awareness

**Ref:** Proposal Section 2.8 (basic awareness only — full SIMOPs in Sprint 2)

### Sub-Tasks

- [ ] **Zone overlap detection** — detect when multiple permits are active/pending in the same zone during overlapping time periods
- [ ] **Warning display** — show SIMOPs warning on permit detail page when overlaps detected
- [ ] **Cross-permit visibility** — show which other permits are active in the same zone

### Dependencies

- Project & Zone Management
- Permit CRUD

### Acceptance Criteria

- System detects overlapping permits in the same zone
- Warning displayed on permit detail page
- Users can see other active permits in the zone

---

## 1.15 Background Automation

**Ref:** Proposal Section 2.16

### Sub-Tasks

- [x] BullMQ queue setup with Redis
- [x] Daily reminder job (7:00 AM)
- [x] Suspension check job (8:05 AM)
- [x] Expiry warning job (24h before)
- [x] End-of-day processing job (5:00 PM)
- [x] Job monitoring (Bull Board)
- [ ] **Extension deadline alerts** — notify when extension deadlines approach

### Acceptance Criteria

- All cron jobs run reliably on schedule
- Reminders sent at 7:00 AM
- Suspensions triggered at 8:05 AM
- Expiry warnings sent 24h before

---

## 1.16 Email Notifications

**Ref:** Proposal Section 2.14

### Sub-Tasks

- [x] Email service with Resend SDK
- [x] Email templates (9 types: submission, approval, rejection, etc.)
- [x] Send-email job processor (queue-based)
- [ ] **Integrate email triggers into workflow events** — automatic emails on: submission, approval, rejection, daily reminders, suspension, expiry warning, close-out
- [ ] **Email notification preferences** — users can opt out of specific email types

### Dependencies

- Email service configured with valid API key

### Acceptance Criteria

- Emails sent automatically on all core workflow events
- Email templates professional and informative
- Users can manage email preferences

---

## 1.17 In-App Notifications

**Ref:** Proposal Section 2.14

### Sub-Tasks

- [x] Notification bell component with unread count
- [x] Notification dropdown
- [x] Mark as read (individual and bulk)
- [x] Notifications page (full list)
- [ ] **Click-to-navigate** — clicking notification goes to relevant permit
- [ ] **Real-time updates** — new notifications appear without page refresh

### Acceptance Criteria

- Bell icon shows unread count
- Click any notification navigates to the relevant permit
- Notifications update in real-time

---

## 1.18 On-Demand PDF Generation

**Ref:** Proposal Section 2.13

### Sub-Tasks

- [x] Puppeteer-based PDF generation
- [x] Two-page layout (front: form data/signatures, back: precautions/re-validation)
- [x] Company branding (header, logo)
- [x] QR code linking to digital permit
- [x] PDF download endpoint
- [ ] **On-demand generation** — generate when user clicks "View PDF" or "Download PDF" (no permanent storage)
- [ ] **All approval signatures with timestamps** in PDF

### Acceptance Criteria

- PDFs generated on-the-fly when requested
- Two-page layout matching physical permit format
- Includes form data, signatures, QR code, branding
- Download/print from permit detail view

---

## 1.19 File Storage

**Ref:** Proposal Section 2.15

### Sub-Tasks

- [x] File upload API with auth/authz checks
- [x] Pre-signed URL generation
- [x] File size (25MB) and type (PDF, JPG, PNG, HEIC) validation
- [ ] **Migrate to VPS filesystem storage** — proposal specifies server-side storage, not R2/S3
- [ ] **Access control** — file access respects permit visibility permissions

### Technical Note

Current implementation uses Cloudflare R2. Proposal specifies VPS filesystem storage. Need to either:
- Migrate file service to use local filesystem, or
- Keep R2 for development and document VPS migration for production deployment

### Acceptance Criteria

- Files stored securely on server
- Only authorized users can download permit files
- Supported formats: PDF, JPG, PNG, HEIC (up to 25MB, max 10 per permit)

---

## 1.20 Audit Trail

**Ref:** Proposal Section 2.17

### Sub-Tasks

- [x] Comprehensive logging for all permit actions
- [x] Timestamped entries with user attribution
- [x] Status transition logging (previous → new status)
- [x] Comments captured from approvers/rejectors
- [ ] **Timeline view** — visual timeline of all permit events on permit detail page

### Acceptance Criteria

- Every permit action logged with timestamp and user
- Audit trail viewable on permit detail page as timeline
- All status transitions tracked

---

## 1.21 Responsive Frontend

**Ref:** Proposal Section 1.1 #19

### Sub-Tasks

- [x] Mobile-first design
- [x] App shell layout with sidebar
- [x] Role-based dashboard pages
- [x] Mobile navigation
- [x] Header with user menu

### Acceptance Criteria

- Works on mobile, tablet, and desktop
- Role-based dashboards show relevant content

---

## 1.22 Staging Deployment

**Ref:** Proposal Section 3 (Sprint 1 Review)

### Sub-Tasks

- [ ] **Deploy to staging environment** on development VPS
- [ ] **Configure staging database** (PostgreSQL)
- [ ] **Configure staging Redis**
- [ ] **Set up staging domain/URL**
- [ ] **Seed staging with demo data**
- [ ] **Deliver staging URL to client**

### Dependencies

- All Sprint 1 features must be functional

### Acceptance Criteria

- Client receives a live staging URL
- Full core PTW workflow operational on staging
- All 5 roles can log in and perform their functions
