# Permitto - Product Requirements Document (PRD) v2

**Version:** 2.0
**Date:** January 15, 2026
**Author:** Product Team
**Status:** Draft for Review
**Previous Version:** [PRD v1](./PRD_permitto_v1.md)

---

## Document Change Summary (v1 → v2)

| Section | Change |
|---------|--------|
| Architecture | Separate Node.js backend (Express.js) instead of Next.js API Routes |
| Deployment | Docker-based deployment on Railway instead of Vercel serverless |
| File Storage | Cloudflare R2 instead of Vercel Blob |
| Infrastructure | Added Redis for caching/queues, explicit CI/CD requirements |
| Scale Design | MVP for 50-100 users with documented path to 500+ |

---

## Table of Contents

1. [Executive Summary](#1-executive-summary)
2. [Problem Statement](#2-problem-statement)
3. [Product Vision](#3-product-vision)
4. [User Roles & Personas](#4-user-roles--personas)
5. [Functional Requirements](#5-functional-requirements)
6. [Permit Workflow Specification](#6-permit-workflow-specification)
7. [Data Model](#7-data-model)
8. [Technical Architecture](#8-technical-architecture)
9. [Infrastructure & Deployment](#9-infrastructure--deployment)
10. [MVP Scope & Phasing](#10-mvp-scope--phasing)
11. [Non-Functional Requirements](#11-non-functional-requirements)
12. [Security Requirements](#12-security-requirements)
13. [Risks & Mitigations](#13-risks--mitigations)
14. [Success Metrics](#14-success-metrics)
15. [Future Roadmap](#15-future-roadmap)
16. [Appendices](#16-appendices)

---

## 1. Executive Summary

### 1.1 Product Overview

**Permitto** is an in-house Permit To Work (PTW) web application designed to digitize and streamline the permit approval process for construction and manufacturing operations. The system replaces paper-based permit management with a modern, mobile-friendly digital workflow.

### 1.2 Business Context

| Attribute | Value |
|-----------|-------|
| **Type** | Internal tool (not SaaS) |
| **Industry** | Construction & Manufacturing |
| **Target Users** | Internal employees (team leads, supervisors, coordinators) |
| **Deployment** | Single-tenant, company-owned infrastructure |
| **MVP Scale** | 50-100 concurrent users |
| **Growth Target** | 500+ users (documented migration path) |

### 1.3 Key Objectives

| Objective | Current State | Target State |
|-----------|---------------|--------------|
| Approval Time | Days (physical transport) | Hours (digital workflow) |
| Permit Tracking | Manual, error-prone | Real-time visibility |
| Compliance | Paper records | Digital audit trail |
| Accessibility | Office-bound | Mobile-first |

### 1.4 Success Criteria

- Reduce permit approval cycle from days to < 4 hours
- 100% digital permit issuance within 3 months of launch
- Zero lost/misplaced permits
- Complete audit trail for all permit activities
- System uptime > 99.5%

---

## 2. Problem Statement

### 2.1 Current Pain Points

1. **Approval Delays:** Workers physically transport permits between Issuer and Coordinator locations, causing significant delays in the approval queue.

2. **Lost Documentation:** Hard copy permits get lost, damaged, or become illegible, creating compliance risks.

3. **No Visibility:** No way to track permit status without physically locating the document.

4. **Bulk Processing Bottlenecks:** High-volume periods (50+ permits/week) create processing backlogs.

5. **Audit Challenges:** Difficulty proving proper permit issuance during audits or incidents.

### 2.2 Root Cause Analysis

```
Physical Paper Process
├── Single copy must travel sequentially
├── No parallel processing possible
├── Dependent on physical proximity
├── No automatic notifications
└── Manual status tracking only
```

### 2.3 Impact

| Impact Area | Current Cost |
|-------------|--------------|
| Worker downtime | Waiting for approvals |
| Safety risk | Work starting without proper permits |
| Compliance risk | Missing/incomplete documentation |
| Administrative overhead | Manual tracking and filing |

---

## 3. Product Vision

### 3.1 Vision Statement

> "Enable any authorized worker to request, track, and receive permit approval from anywhere, with complete visibility and compliance assurance."

### 3.2 Design Principles

1. **Mobile-First:** Field workers access via smartphones
2. **Minimal Friction:** Fewest possible taps to complete actions
3. **Real-Time:** Instant notifications, live status updates
4. **Compliance Built-In:** Audit trail automatic, not afterthought
5. **Print-Ready:** Physical permits still needed on-site
6. **Scalable:** Architecture supports growth to 500+ users

### 3.3 Key Differentiators vs. Paper Process

| Aspect | Paper | Permitto |
|--------|-------|----------|
| Approval notification | None | Push + Email + In-App |
| Status visibility | Unknown | Real-time tracking |
| Daily re-approval | Physical signatures | One-tap digital |
| Storage | Filing cabinets | Searchable database |
| Audit trail | Manual logs | Automatic timestamps |
| File attachments | Paper copies | Cloud storage (R2) |

---

## 4. User Roles & Personas

### 4.1 Role Hierarchy

```
Super Admin
    │
    ├── Creates Projects
    ├── Creates Coordinators, Issuers, Receivers
    └── System-wide visibility
         │
         ▼
    Coordinator
    │
    ├── Creates Permit Templates
    ├── Assigns Issuers to Projects
    ├── Final Approval Authority
    ├── Closes Out Permits
    └── Assigns Receivers (optional)
         │
         ▼
      Issuer
      │
      ├── First-Level Approval
      ├── Assigns Receivers to Projects
      ├── Daily Re-Approval
      └── Signs Permits
           │
           ▼
        Receiver (Team Lead/Supervisor)
        │
        ├── Requests Permits
        ├── Fills Multi-Step Forms
        ├── Uploads Supporting Documents
        ├── Daily Re-Confirmation
        └── Tracks Permit Status
```

### 4.2 User Personas

#### Persona 1: Super Admin (Sarah)
- **Role:** System Administrator
- **Goals:** Maintain user accounts, monitor system health
- **Frequency:** Weekly access
- **Device:** Desktop primarily

#### Persona 2: Coordinator (Carlos)
- **Role:** Safety Coordinator
- **Goals:** Ensure compliance, approve permits quickly
- **Frequency:** Multiple times daily
- **Device:** Desktop + Mobile
- **Pain Point:** Too many permits to review, needs prioritization

#### Persona 3: Issuer (Ibrahim)
- **Role:** Site Supervisor
- **Goals:** Verify work conditions, approve efficiently
- **Frequency:** Continuously throughout day
- **Device:** Mobile primarily
- **Pain Point:** Interruptions for signatures, hard to track pending

#### Persona 4: Receiver (Rashid)
- **Role:** Team Lead
- **Goals:** Get permits approved fast, start work on time
- **Frequency:** Start of shift + as needed
- **Device:** Mobile only
- **Pain Point:** Waiting for approvals, form complexity

### 4.3 Permission Matrix

| Action | Super Admin | Coordinator | Issuer | Receiver |
|--------|-------------|-------------|--------|----------|
| Create Project | ✅ | ❌ | ❌ | ❌ |
| Create User (any role) | ✅ | ❌ | ❌ | ❌ |
| Create Coordinator | ✅ | ❌ | ❌ | ❌ |
| Create Issuer | ✅ | ✅ | ❌ | ❌ |
| Create Receiver | ✅ | ✅ | ✅ | ❌ |
| Create Template | ❌ | ✅ | ❌ | ❌ |
| Edit Template | ❌ | ✅ | ❌ | ❌ |
| Assign to Project | ✅ | ✅ | ✅* | ❌ |
| Request Permit | ❌ | ❌ | ❌ | ✅ |
| Upload Files | ❌ | ✅ | ✅ | ✅ |
| Approve (Level 1) | ❌ | ❌ | ✅ | ❌ |
| Approve (Final) | ❌ | ✅ | ❌ | ❌ |
| Daily Re-Approve | ❌ | ✅ | ✅ | ✅ |
| Close Permit | ❌ | ✅ | ❌ | ❌ |
| View All Permits | ✅ | ✅ | Project Only | Own Only |
| View Analytics | ✅ | ✅ | ❌ | ❌ |
| Download Files | ✅ | ✅ | ✅ | ✅ |

*Issuer can only assign Receivers

---

## 5. Functional Requirements

### 5.1 Authentication & Authorization

#### FR-AUTH-001: User Authentication
- **Description:** Users authenticate via email/password
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Users can register (admin-invited only)
  - Users can login with email/password
  - Sessions persist across browser refresh
  - Password reset via email
  - JWT tokens with refresh token rotation

#### FR-AUTH-002: Role-Based Access Control
- **Description:** System enforces permissions based on user role
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Each user has exactly one role
  - Permissions enforced at API level (backend)
  - UI hides inaccessible features (frontend)
  - Role cannot be changed by user (admin only)

### 5.2 User Management

#### FR-USER-001: Create Users
- **Description:** Super Admin creates user accounts
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Admin can create users with: email, name, role, phone (optional)
  - Email invitation sent to new users
  - Duplicate email prevented
  - Bulk import via CSV (Post-MVP)

#### FR-USER-002: Assign Users to Projects
- **Description:** Users are assigned to projects with their role
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - User can be assigned to multiple projects
  - Assignment respects role permissions
  - Users only see their assigned projects
  - Coordinators see all projects

### 5.3 Project Management

#### FR-PROJ-001: Create Projects
- **Description:** Super Admin creates projects
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Project has: name, code (for permit ID prefix), status
  - Project code is unique (e.g., "DOWNTOWN")
  - Projects can be active/inactive
  - Inactive projects don't accept new permits

### 5.4 Permit Template Management

#### FR-TMPL-001: Template Builder
- **Description:** Coordinators create permit templates with custom fields
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Drag-and-drop field placement
  - Multi-step form support (sections)
  - Field types: text, textarea, number, date, time, select, multiselect, checkbox, radio, file-upload
  - Field properties: label, placeholder, required, validation rules
  - Compliance checklists as checkbox groups
  - Preview template before publishing

#### FR-TMPL-002: Template Types
- **Description:** System supports 4 permit types initially
- **Priority:** P0 (MVP)
- **Permit Types:**
  1. Hot Work Permit (HW)
  2. Confined Space Entry (CSE)
  3. Working at Height (WAH)
  4. Electrical Work Permit (EW)

### 5.5 File Upload & Storage

#### FR-FILE-001: File Upload
- **Description:** Users can upload supporting documents to permits
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Supported formats: PDF, JPG, PNG, HEIC
  - Maximum file size: 25MB per file
  - Maximum files per permit: 10
  - Files stored in Cloudflare R2
  - Pre-signed URL upload (direct browser-to-R2)
  - File metadata stored in database

#### FR-FILE-002: File Access
- **Description:** Users can view and download permit files
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Files accessible via time-limited signed URLs
  - Access respects permit visibility permissions
  - Thumbnail generation for images
  - PDF preview in browser

#### FR-FILE-003: Signature Storage
- **Description:** Digital signatures stored as images
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Signatures captured as PNG (base64)
  - Stored in R2 with permit reference
  - Immutable once permit approved

### 5.6 Permit Request (Receiver)

#### FR-PERM-001: Browse Templates
- **Description:** Receivers browse available permit templates
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Filter by permit type
  - Show only templates for assigned projects
  - Clear indication of required time to complete

#### FR-PERM-002: Multi-Step Form
- **Description:** Receivers fill multi-step permit request form
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Progress indicator shows current step
  - Navigate forward/backward between steps
  - Validation on each step before proceeding
  - Save draft functionality
  - Resume draft later
  - File upload fields integrated

#### FR-PERM-003: Submit Permit
- **Description:** Receivers submit permit for approval
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Submit triggers workflow
  - Notification sent to Issuer
  - Permit status changes to "Submitted"
  - Receiver cannot edit after submit

### 5.7 Permit Approval (Issuer)

#### FR-APPR-001: View Pending Permits
- **Description:** Issuers see permits awaiting their approval
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Dashboard shows pending permits
  - Sort by submission time, priority, project
  - Filter by permit type, project
  - Count of pending items visible

#### FR-APPR-002: Approve Permit
- **Description:** Issuers approve permits
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Approve button requires signature
  - Digital signature captured (drawn)
  - Status changes to "Issuer Approved"
  - Auto-forward to Coordinator
  - Notification sent to Coordinator
  - Notification sent to Receiver

#### FR-APPR-003: Reject Permit
- **Description:** Issuers reject permits with feedback
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Reject requires reason (mandatory text)
  - Status changes to "Rejected by Issuer"
  - Notification sent to Receiver with feedback
  - Receiver can revise and resubmit

### 5.8 Final Approval (Coordinator)

#### FR-FINAL-001: Final Approve
- **Description:** Coordinators give final approval
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Approve requires Coordinator signature
  - Status changes to "Approved"
  - PDF generated with both signatures
  - Notification sent to all parties
  - Permit becomes "Active"

### 5.9 Daily Re-Approval Cycle

#### FR-DAILY-001: Daily Approval Requirement
- **Description:** Active permits require daily re-approval
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Each day within permit validity, all parties must re-confirm
  - Order: Receiver → Issuer → Coordinator
  - Daily deadline: 8:00 AM
  - Re-approval = signature/confirmation only (no form re-entry)

#### FR-DAILY-002: One-Tap Daily Approve
- **Description:** Streamlined daily re-approval
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Approve from notification
  - Bulk approve multiple permits
  - Simple confirmation dialog
  - Timestamp recorded

#### FR-DAILY-003: Suspended Status
- **Description:** Permits suspend if daily approval missed
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - If any party misses 8:00 AM deadline, permit status = "Suspended"
  - Work cannot proceed until resolved
  - Notification to all parties
  - Can be reactivated by completing pending approvals

### 5.10 Permit Close-Out

#### FR-CLOSE-001: Coordinator Close-Out
- **Description:** Coordinators formally close permits
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Only Coordinator can close permit
  - Close-out requires: signature, completion notes
  - Status changes to "Closed"
  - Final PDF generated with close-out info
  - All files archived

### 5.11 PDF Generation

#### FR-PDF-001: Permit PDF Layout
- **Description:** Generate printable PDF of approved permit
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - All form data in structured layout
  - Permit ID prominently displayed
  - Validity period (dates, daily hours)
  - All signatures with timestamps
  - QR code linking to digital permit
  - Company header/branding
  - Attached files listed (with links)

### 5.12 Notifications

#### FR-NOTIF-001: Email Notifications
- **Description:** Send transactional emails for key events
- **Priority:** P0 (MVP)
- **Events:**
  - New permit submitted (→ Issuer)
  - Permit approved by Issuer (→ Receiver, Coordinator)
  - Permit rejected (→ Receiver)
  - Permit approved by Coordinator (→ All)
  - Daily re-approval reminder (→ All with pending)
  - Permit expiry warning (→ All)
  - Permit suspended (→ All)
  - Permit closed (→ All)

#### FR-NOTIF-002: In-App Notifications
- **Description:** Real-time in-app notification center
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Notification bell with unread count
  - Real-time updates (WebSocket/SSE)
  - Click to navigate to relevant permit
  - Mark as read/unread

#### FR-NOTIF-003: Push Notifications
- **Description:** Browser push notifications
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Opt-in during first login
  - Immediate push for approvals, rejections
  - Can disable in preferences

### 5.13 Permit ID Generation

#### FR-ID-001: Permit ID Format
- **Description:** Generate structured permit IDs
- **Priority:** P0 (MVP)
- **Format:** `{PROJECT_CODE}-{PERMIT_TYPE}-{YEAR}-{SEQUENCE}`
- **Example:** `DOWNTOWN-HW-2026-001`

---

## 6. Permit Workflow Specification

### 6.1 State Machine

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         PERMIT STATE MACHINE                            │
└─────────────────────────────────────────────────────────────────────────┘

                              ┌─────────┐
                              │  DRAFT  │
                              └────┬────┘
                                   │ Submit
                                   ▼
                            ┌───────────┐
                            │ SUBMITTED │
                            └─────┬─────┘
                           ┌──────┴──────┐
                           │             │
                    Approve│             │Reject
                           ▼             ▼
                  ┌────────────────┐  ┌─────────────────┐
                  │ISSUER_APPROVED │  │REJECTED_ISSUER  │──┐
                  └───────┬────────┘  └─────────────────┘  │
                   ┌──────┴──────┐                         │
                   │             │                         │
            Approve│             │Reject                   │
                   ▼             ▼                         │
          ┌──────────────┐  ┌─────────────────┐           │
          │   APPROVED   │  │REJECTED_COORD   │───────────┤
          │   (Active)   │  └─────────────────┘           │
          └──────┬───────┘                                │
                 │                                        │
    ┌────────────┼────────────────────────────────────────┘
    │            │                         Revise & Resubmit
    │            ▼
    │  ┌─────────────────────────┐
    │  │   DAILY APPROVAL CYCLE  │
    │  │  ┌──────────────────┐   │
    │  │  │PENDING_DAILY_ALL │   │
    │  │  └────────┬─────────┘   │
    │  │           │             │
    │  │      All approve        │Any miss
    │  │           │             │
    │  │           ▼             ▼
    │  │  ┌─────────────┐  ┌───────────┐
    │  │  │   ACTIVE    │  │ SUSPENDED │
    │  │  │ (work day)  │  │           │
    │  │  └──────┬──────┘  └─────┬─────┘
    │  │         │               │
    │  │    End of day      Resolve
    │  │         │               │
    │  │         └───────┬───────┘
    │  │                 │
    │  │                 ▼
    │  │         (Next Day Start)
    │  └─────────────────────────┘
    │            │
    │      Validity Ends OR Early Close Request
    │            │
    │            ▼
    │  ┌────────────────────┐
    │  │  PENDING_CLOSEOUT  │
    │  └─────────┬──────────┘
    │            │
    │       Coordinator Close-Out
    │            │
    │            ▼
    │  ┌────────────────────┐
    │  │      CLOSED        │
    │  └────────────────────┘
    │
    │ (Optional: Manual Cancellation)
    │            │
    │            ▼
    │  ┌────────────────────┐
    └─▶│     CANCELLED      │
       └────────────────────┘
```

### 6.2 Status Definitions

| Status | Description | Next Possible States |
|--------|-------------|---------------------|
| `DRAFT` | Receiver working on form | SUBMITTED, CANCELLED |
| `SUBMITTED` | Sent to Issuer for review | ISSUER_APPROVED, REJECTED_ISSUER |
| `ISSUER_APPROVED` | Issuer approved, awaiting Coordinator | APPROVED, REJECTED_COORD |
| `REJECTED_ISSUER` | Issuer rejected with feedback | DRAFT (after revision) |
| `REJECTED_COORD` | Coordinator rejected | DRAFT (after revision) |
| `APPROVED` | Fully approved, permit active | PENDING_DAILY, SUSPENDED, PENDING_CLOSEOUT |
| `PENDING_DAILY` | Awaiting daily re-approvals | ACTIVE, SUSPENDED |
| `ACTIVE` | Permit valid for current work day | PENDING_DAILY (next day), PENDING_CLOSEOUT |
| `SUSPENDED` | Daily approval missed | ACTIVE (after resolution) |
| `PENDING_CLOSEOUT` | Expired, awaiting Coordinator close | CLOSED |
| `CLOSED` | Formally closed by Coordinator | (terminal) |
| `CANCELLED` | Manually cancelled | (terminal) |

### 6.3 Timing Rules

| Event | Timing | Trigger |
|-------|--------|---------|
| Daily reminder | 7:00 AM | All active permits |
| Daily deadline | 8:00 AM | Suspend if not approved |
| Expiry warning | 24h before | Notification to all |
| Expiry | End of validity | Status → PENDING_CLOSEOUT |
| Work hours end | 5:00 PM | Daily status → PENDING_DAILY |

---

## 7. Data Model

### 7.1 Entity Relationship Diagram

```
┌─────────────────┐     ┌─────────────────┐
│      User       │     │     Project     │
├─────────────────┤     ├─────────────────┤
│ id              │     │ id              │
│ email           │     │ name            │
│ name            │     │ code            │
│ passwordHash    │     │ status          │
│ role            │     │ createdAt       │
│ phone           │     │ updatedAt       │
│ createdAt       │     └────────┬────────┘
│ updatedAt       │              │
└────────┬────────┘              │
         │                       │
         └───────┬───────────────┘
                 │
         ┌───────▼───────┐
         │ ProjectUser   │
         ├───────────────┤
         │ projectId     │
         │ userId        │
         │ assignedBy    │
         │ assignedAt    │
         └───────────────┘

┌─────────────────┐     ┌─────────────────┐
│ PermitTemplate  │     │     Permit      │
├─────────────────┤     ├─────────────────┤
│ id              │     │ id              │
│ name            │◄────┤ templateId      │
│ type            │     │ permitNumber    │
│ schema (JSON)   │     │ projectId       │
│ version         │     │ receiverId      │
│ isActive        │     │ issuerId        │
│ createdBy       │     │ coordinatorId   │
│ createdAt       │     │ status          │
└─────────────────┘     │ formData (JSON) │
                        │ validFrom       │
                        │ validTo         │
                        │ signatures      │
                        │ createdAt       │
                        │ updatedAt       │
                        └────────┬────────┘
                                 │
               ┌─────────────────┼─────────────────┐
               │                 │                 │
      ┌────────▼────────┐  ┌─────▼─────┐  ┌───────▼───────┐
      │  DailyApproval  │  │PermitFile │  │PermitAuditLog │
      ├─────────────────┤  ├───────────┤  ├───────────────┤
      │ id              │  │ id        │  │ id            │
      │ permitId        │  │ permitId  │  │ permitId      │
      │ date            │  │ fileKey   │  │ userId        │
      │ receiverApproved│  │ fileName  │  │ action        │
      │ issuerApproved  │  │ fileType  │  │ previousStatus│
      │ coordApproved   │  │ fileSize  │  │ newStatus     │
      │ status          │  │ uploadedBy│  │ comments      │
      └─────────────────┘  │ createdAt │  │ timestamp     │
                           └───────────┘  └───────────────┘

┌─────────────────┐
│  Notification   │
├─────────────────┤
│ id              │
│ userId          │
│ type            │
│ title           │
│ message         │
│ data (JSON)     │
│ read            │
│ createdAt       │
└─────────────────┘
```

### 7.2 Prisma Schema

```prisma
// schema.prisma

generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

enum Role {
  SUPER_ADMIN
  COORDINATOR
  ISSUER
  RECEIVER
}

enum PermitType {
  HOT_WORK        // HW
  CONFINED_SPACE  // CSE
  WORKING_HEIGHT  // WAH
  ELECTRICAL      // EW
}

enum PermitStatus {
  DRAFT
  SUBMITTED
  ISSUER_APPROVED
  REJECTED_ISSUER
  REJECTED_COORD
  APPROVED
  PENDING_DAILY
  ACTIVE
  SUSPENDED
  PENDING_CLOSEOUT
  CLOSED
  CANCELLED
}

enum DailyStatus {
  PENDING
  APPROVED
  MISSED
}

model User {
  id                 String         @id @default(cuid())
  email              String         @unique
  name               String
  passwordHash       String
  role               Role
  phone              String?
  emailNotifications Boolean        @default(true)
  pushNotifications  Boolean        @default(true)
  createdAt          DateTime       @default(now())
  updatedAt          DateTime       @updatedAt

  // Relations
  projectAssignments ProjectUser[]
  templatesCreated   PermitTemplate[]
  permitsAsReceiver  Permit[]        @relation("ReceiverPermits")
  permitsAsIssuer    Permit[]        @relation("IssuerPermits")
  permitsAsCoord     Permit[]        @relation("CoordPermits")
  auditLogs          PermitAuditLog[]
  notifications      Notification[]
  assignedBy         ProjectUser[]   @relation("AssignedByUser")
  uploadedFiles      PermitFile[]
}

model Project {
  id        String   @id @default(cuid())
  name      String
  code      String   @unique
  isActive  Boolean  @default(true)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  users     ProjectUser[]
  permits   Permit[]
}

model ProjectUser {
  id         String   @id @default(cuid())
  projectId  String
  userId     String
  assignedBy String
  assignedAt DateTime @default(now())

  project    Project  @relation(fields: [projectId], references: [id])
  user       User     @relation(fields: [userId], references: [id])
  assigner   User     @relation("AssignedByUser", fields: [assignedBy], references: [id])

  @@unique([projectId, userId])
}

model PermitTemplate {
  id          String     @id @default(cuid())
  name        String
  description String?
  type        PermitType
  schema      Json
  version     Int        @default(1)
  isActive    Boolean    @default(true)
  createdBy   String
  createdAt   DateTime   @default(now())
  updatedAt   DateTime   @updatedAt

  creator     User       @relation(fields: [createdBy], references: [id])
  permits     Permit[]
}

model Permit {
  id              String       @id @default(cuid())
  permitNumber    String       @unique
  templateId      String
  projectId       String
  receiverId      String
  issuerId        String?
  coordinatorId   String?

  status          PermitStatus @default(DRAFT)
  formData        Json

  validFrom       DateTime
  validTo         DateTime
  dailyStartTime  String       @default("08:00")
  dailyEndTime    String       @default("17:00")
  isExtendedHours Boolean      @default(false)

  // Signatures stored as R2 keys
  receiverSignature  String?
  issuerSignature    String?
  issuerSignedAt     DateTime?
  coordSignature     String?
  coordSignedAt      DateTime?

  closeOutNotes   String?
  closedAt        DateTime?

  createdAt       DateTime     @default(now())
  updatedAt       DateTime     @updatedAt

  template        PermitTemplate @relation(fields: [templateId], references: [id])
  project         Project        @relation(fields: [projectId], references: [id])
  receiver        User           @relation("ReceiverPermits", fields: [receiverId], references: [id])
  issuer          User?          @relation("IssuerPermits", fields: [issuerId], references: [id])
  coordinator     User?          @relation("CoordPermits", fields: [coordinatorId], references: [id])
  dailyApprovals  DailyApproval[]
  auditLogs       PermitAuditLog[]
  files           PermitFile[]
}

model PermitFile {
  id         String   @id @default(cuid())
  permitId   String
  fileKey    String   @unique  // R2 object key
  fileName   String
  fileType   String
  fileSize   Int
  uploadedBy String
  createdAt  DateTime @default(now())

  permit     Permit   @relation(fields: [permitId], references: [id])
  uploader   User     @relation(fields: [uploadedBy], references: [id])
}

model DailyApproval {
  id              String      @id @default(cuid())
  permitId        String
  date            DateTime    @db.Date

  receiverApproved Boolean    @default(false)
  receiverAt       DateTime?
  issuerApproved   Boolean    @default(false)
  issuerAt         DateTime?
  coordApproved    Boolean    @default(false)
  coordAt          DateTime?

  status          DailyStatus @default(PENDING)

  permit          Permit      @relation(fields: [permitId], references: [id])

  @@unique([permitId, date])
}

model PermitAuditLog {
  id             String       @id @default(cuid())
  permitId       String
  userId         String
  action         String
  previousStatus PermitStatus?
  newStatus      PermitStatus?
  comments       String?
  metadata       Json?
  timestamp      DateTime     @default(now())

  permit         Permit       @relation(fields: [permitId], references: [id])
  user           User         @relation(fields: [userId], references: [id])
}

model Notification {
  id        String   @id @default(cuid())
  userId    String
  type      String
  title     String
  message   String
  data      Json?
  read      Boolean  @default(false)
  createdAt DateTime @default(now())

  user      User     @relation(fields: [userId], references: [id])
}

model PermitSequence {
  id          String     @id @default(cuid())
  projectId   String
  permitType  PermitType
  year        Int
  lastNumber  Int        @default(0)

  @@unique([projectId, permitType, year])
}
```

---

## 8. Technical Architecture

### 8.1 System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              CLIENT LAYER                                    │
│                                                                             │
│    ┌─────────────┐    ┌─────────────┐    ┌─────────────────────────────┐   │
│    │   Mobile    │    │   Tablet    │    │          Desktop            │   │
│    │  (Browser)  │    │  (Browser)  │    │         (Browser)           │   │
│    └──────┬──────┘    └──────┬──────┘    └──────────────┬──────────────┘   │
│           └──────────────────┴────────────────────────┬─┘                   │
│                                                       │                     │
│                         Next.js 14+ Frontend                                │
│                    ┌──────────────────────────────────┐                     │
│                    │  • React 18 + TypeScript         │                     │
│                    │  • shadcn/ui + Tailwind CSS      │                     │
│                    │  • React Hook Form + Zod         │                     │
│                    │  • Zustand (Client State)        │                     │
│                    │  • TanStack Query (Server State) │                     │
│                    └────────────────┬─────────────────┘                     │
│                                     │                                       │
└─────────────────────────────────────┼───────────────────────────────────────┘
                                      │ HTTPS (REST API)
                                      │
┌─────────────────────────────────────┼───────────────────────────────────────┐
│                                     │          BACKEND LAYER                │
│                                     ▼                                       │
│                    ┌──────────────────────────────────┐                     │
│                    │      Node.js + Express.js        │                     │
│                    │    ┌─────────────────────────┐   │                     │
│                    │    │    API Server           │   │                     │
│                    │    │  • REST Endpoints       │   │                     │
│                    │    │  • JWT Authentication   │   │                     │
│                    │    │  • RBAC Middleware      │   │                     │
│                    │    │  • Zod Validation       │   │                     │
│                    │    │  • Prisma ORM           │   │                     │
│                    │    └─────────────────────────┘   │                     │
│                    │    ┌─────────────────────────┐   │                     │
│                    │    │    Background Workers   │   │                     │
│                    │    │  • BullMQ (Redis Queue) │   │                     │
│                    │    │  • PDF Generation       │   │                     │
│                    │    │  • Email Sending        │   │                     │
│                    │    │  • Scheduled Jobs       │   │                     │
│                    │    └─────────────────────────┘   │                     │
│                    └──────────────────────────────────┘                     │
│                                     │                                       │
└─────────────────────────────────────┼───────────────────────────────────────┘
                                      │
┌─────────────────────────────────────┼───────────────────────────────────────┐
│                                     │           DATA LAYER                  │
│                                     │                                       │
│    ┌────────────────────────────────┼────────────────────────────────────┐  │
│    │                                │                                    │  │
│    ▼                                ▼                                    │  │
│ ┌────────────────┐           ┌────────────────┐        ┌──────────────┐ │  │
│ │  PostgreSQL    │           │     Redis      │        │ Cloudflare   │ │  │
│ │  (Railway)     │           │   (Railway)    │        │     R2       │ │  │
│ │                │           │                │        │              │ │  │
│ │ • Users        │           │ • Sessions     │        │ • Documents  │ │  │
│ │ • Permits      │           │ • Job Queue    │        │ • Signatures │ │  │
│ │ • Templates    │           │ • Cache        │        │ • PDFs       │ │  │
│ │ • Audit Logs   │           │ • Rate Limits  │        │ • Images     │ │  │
│ └────────────────┘           └────────────────┘        └──────────────┘ │  │
│                                                                          │  │
└──────────────────────────────────────────────────────────────────────────┘  │
                                                                              │
┌─────────────────────────────────────────────────────────────────────────────┘
│                           EXTERNAL SERVICES
│
│  ┌────────────────┐   ┌────────────────┐   ┌────────────────┐
│  │    Resend      │   │    Sentry      │   │   Cloudflare   │
│  │   (Email)      │   │   (Errors)     │   │    (CDN/DNS)   │
│  └────────────────┘   └────────────────┘   └────────────────┘
│
└─────────────────────────────────────────────────────────────────────────────┘
```

### 8.2 Tech Stack Summary

| Layer | Technology | Purpose |
|-------|------------|---------|
| **Frontend** | Next.js 14+ (App Router) | SSR, routing, static generation |
| **UI Components** | shadcn/ui + Tailwind CSS | Design system |
| **Forms** | React Hook Form + Zod | Form handling, validation |
| **Client State** | Zustand | Local UI state |
| **Server State** | TanStack Query | API caching, sync |
| **Backend** | Node.js + Express.js | REST API server |
| **Validation** | Zod | Request/response validation |
| **Auth** | JWT (access + refresh tokens) | Authentication |
| **ORM** | Prisma | Database access |
| **Database** | PostgreSQL | Primary data store |
| **Cache/Queue** | Redis + BullMQ | Sessions, job queue |
| **File Storage** | Cloudflare R2 | Documents, signatures, PDFs |
| **PDF Generation** | Puppeteer / @react-pdf/renderer | PDF creation |
| **Email** | Resend | Transactional email |
| **Containerization** | Docker + Docker Compose | Development & deployment |
| **Hosting (Backend)** | Railway | Backend + DB + Redis |
| **Hosting (Frontend)** | Vercel or Railway | Frontend deployment |
| **Monitoring** | Sentry | Error tracking |
| **CDN/DNS** | Cloudflare | CDN, DDoS protection |

### 8.3 Repository Structure

```
permitto/
├── apps/
│   ├── web/                    # Next.js Frontend
│   │   ├── src/
│   │   │   ├── app/            # App Router pages
│   │   │   ├── components/     # React components
│   │   │   ├── hooks/          # Custom hooks
│   │   │   ├── lib/            # Utilities
│   │   │   ├── stores/         # Zustand stores
│   │   │   └── types/          # TypeScript types
│   │   ├── public/
│   │   ├── Dockerfile
│   │   └── package.json
│   │
│   └── api/                    # Express.js Backend
│       ├── src/
│       │   ├── config/         # Configuration
│       │   ├── controllers/    # Route handlers
│       │   ├── middleware/     # Express middleware
│       │   ├── routes/         # API routes
│       │   ├── services/       # Business logic
│       │   ├── jobs/           # Background jobs
│       │   ├── utils/          # Utilities
│       │   └── index.ts        # Entry point
│       ├── prisma/
│       │   ├── schema.prisma
│       │   ├── migrations/
│       │   └── seed.ts
│       ├── Dockerfile
│       └── package.json
│
├── packages/
│   ├── shared/                 # Shared types, constants
│   │   ├── src/
│   │   │   ├── types/
│   │   │   ├── constants/
│   │   │   └── validators/     # Shared Zod schemas
│   │   └── package.json
│   │
│   └── ui/                     # Shared UI components (optional)
│       └── package.json
│
├── docker-compose.yml          # Local development
├── docker-compose.prod.yml     # Production reference
├── .env.example
├── turbo.json                  # Turborepo config
├── package.json                # Root package.json
└── README.md
```

### 8.4 API Design

#### Base URL
- Development: `http://localhost:3001/api/v1`
- Production: `https://api.permitto.example.com/api/v1`

#### Authentication Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/auth/login` | User login, returns tokens |
| POST | `/auth/logout` | Invalidate refresh token |
| POST | `/auth/refresh` | Refresh access token |
| POST | `/auth/forgot-password` | Request password reset |
| POST | `/auth/reset-password` | Reset password with token |
| GET | `/auth/me` | Get current user profile |

#### User Management Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/users` | List users (paginated) |
| POST | `/users` | Create user |
| GET | `/users/:id` | Get user details |
| PATCH | `/users/:id` | Update user |
| DELETE | `/users/:id` | Deactivate user |

#### Project Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/projects` | List projects |
| POST | `/projects` | Create project |
| GET | `/projects/:id` | Get project details |
| PATCH | `/projects/:id` | Update project |
| POST | `/projects/:id/users` | Assign user |
| DELETE | `/projects/:id/users/:userId` | Remove user |

#### Template Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/templates` | List templates |
| POST | `/templates` | Create template |
| GET | `/templates/:id` | Get template details |
| PATCH | `/templates/:id` | Update template |
| POST | `/templates/:id/publish` | Publish template |

#### Permit Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/permits` | List permits (filtered) |
| POST | `/permits` | Create permit draft |
| GET | `/permits/:id` | Get permit details |
| PATCH | `/permits/:id` | Update permit draft |
| POST | `/permits/:id/submit` | Submit permit |
| POST | `/permits/:id/approve` | Approve permit |
| POST | `/permits/:id/reject` | Reject permit |
| POST | `/permits/:id/daily-approve` | Daily re-approval |
| POST | `/permits/:id/close` | Close permit |
| GET | `/permits/:id/pdf` | Download PDF |
| GET | `/permits/:id/timeline` | Get audit timeline |

#### File Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/files/upload-url` | Get pre-signed upload URL |
| GET | `/files/:id` | Get file download URL |
| DELETE | `/files/:id` | Delete file |

#### Notification Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/notifications` | List notifications |
| PATCH | `/notifications/:id/read` | Mark as read |
| POST | `/notifications/read-all` | Mark all as read |

### 8.5 Background Jobs

| Job | Schedule/Trigger | Description |
|-----|-----------------|-------------|
| `daily-reminder` | Cron: 7:00 AM | Send re-approval reminders |
| `check-suspensions` | Cron: 8:05 AM | Suspend overdue permits |
| `expiry-warning` | Cron: 12:00 AM | Send 24h expiry warnings |
| `end-of-day` | Cron: 5:00 PM | Process day-end status |
| `generate-pdf` | Queue trigger | Generate permit PDF |
| `send-email` | Queue trigger | Send transactional email |
| `cleanup-expired-files` | Cron: 3:00 AM | Remove orphaned files |

---

## 9. Infrastructure & Deployment

### 9.1 Environment Overview

| Environment | Purpose | URL Pattern |
|-------------|---------|-------------|
| **Local** | Development | `localhost:3000` (web), `localhost:3001` (api) |
| **Staging** | Testing, QA | `staging.permitto.example.com` |
| **Production** | Live system | `permitto.example.com` |

### 9.2 Docker Configuration

#### Development Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  # PostgreSQL Database
  db:
    image: postgres:15-alpine
    container_name: permitto-db
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

  # Redis
  redis:
    image: redis:7-alpine
    container_name: permitto-redis
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 5s
      retries: 5

  # Backend API
  api:
    build:
      context: ./apps/api
      dockerfile: Dockerfile
      target: development
    container_name: permitto-api
    environment:
      NODE_ENV: development
      DATABASE_URL: postgresql://permitto:permitto_dev@db:5432/permitto
      REDIS_URL: redis://redis:6379
      JWT_SECRET: dev-secret-change-in-production
      R2_ACCOUNT_ID: ${R2_ACCOUNT_ID}
      R2_ACCESS_KEY_ID: ${R2_ACCESS_KEY_ID}
      R2_SECRET_ACCESS_KEY: ${R2_SECRET_ACCESS_KEY}
      R2_BUCKET_NAME: ${R2_BUCKET_NAME}
      RESEND_API_KEY: ${RESEND_API_KEY}
    ports:
      - "3001:3001"
    volumes:
      - ./apps/api:/app
      - /app/node_modules
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_healthy
    command: npm run dev

  # Frontend Web
  web:
    build:
      context: ./apps/web
      dockerfile: Dockerfile
      target: development
    container_name: permitto-web
    environment:
      NEXT_PUBLIC_API_URL: http://localhost:3001/api/v1
    ports:
      - "3000:3000"
    volumes:
      - ./apps/web:/app
      - /app/node_modules
      - /app/.next
    depends_on:
      - api
    command: npm run dev

volumes:
  postgres_data:
  redis_data:
```

#### Production Dockerfile (Backend)

```dockerfile
# apps/api/Dockerfile
FROM node:20-alpine AS base

# Install dependencies only when needed
FROM base AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --only=production

# Development
FROM base AS development
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
EXPOSE 3001
CMD ["npm", "run", "dev"]

# Build
FROM base AS builder
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
COPY prisma ./prisma
RUN npx prisma generate
RUN npm run build

# Production
FROM base AS production
WORKDIR /app

ENV NODE_ENV=production

# Create non-root user
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 expressjs

COPY --from=deps /app/node_modules ./node_modules
COPY --from=builder /app/dist ./dist
COPY --from=builder /app/prisma ./prisma
COPY --from=builder /app/package.json ./package.json

USER expressjs

EXPOSE 3001

CMD ["npm", "run", "start:prod"]
```

### 9.3 Railway Deployment

#### Services to Deploy

| Service | Type | Resources |
|---------|------|-----------|
| `permitto-api` | Docker | 512MB RAM, 0.5 vCPU |
| `permitto-web` | Docker or Nixpacks | 512MB RAM, 0.5 vCPU |
| `permitto-db` | PostgreSQL | Starter (1GB) |
| `permitto-redis` | Redis | Starter (25MB) |

#### Environment Variables (Production)

```bash
# Database
DATABASE_URL=postgresql://...@railway/permitto

# Redis
REDIS_URL=redis://...@railway

# JWT
JWT_SECRET=<strong-random-secret>
JWT_REFRESH_SECRET=<strong-random-secret>
JWT_ACCESS_EXPIRY=15m
JWT_REFRESH_EXPIRY=7d

# Cloudflare R2
R2_ACCOUNT_ID=<account-id>
R2_ACCESS_KEY_ID=<access-key>
R2_SECRET_ACCESS_KEY=<secret-key>
R2_BUCKET_NAME=permitto-files
R2_PUBLIC_URL=https://files.permitto.example.com

# Email
RESEND_API_KEY=<resend-api-key>
EMAIL_FROM=noreply@permitto.example.com

# Frontend
NEXT_PUBLIC_API_URL=https://api.permitto.example.com/api/v1

# Monitoring
SENTRY_DSN=<sentry-dsn>

# App
APP_URL=https://permitto.example.com
NODE_ENV=production
```

### 9.4 Cloudflare R2 Setup

#### Bucket Configuration

| Setting | Value |
|---------|-------|
| Bucket Name | `permitto-files` |
| Location | Auto (nearest) |
| Default Encryption | Enabled |

#### CORS Configuration

```json
[
  {
    "AllowedOrigins": [
      "https://permitto.example.com",
      "https://staging.permitto.example.com",
      "http://localhost:3000"
    ],
    "AllowedMethods": ["GET", "PUT", "POST", "DELETE", "HEAD"],
    "AllowedHeaders": ["*"],
    "MaxAgeSeconds": 3600
  }
]
```

#### Folder Structure

```
permitto-files/
├── signatures/
│   └── {permitId}/
│       ├── receiver-{timestamp}.png
│       ├── issuer-{timestamp}.png
│       └── coordinator-{timestamp}.png
├── documents/
│   └── {permitId}/
│       └── {fileId}-{filename}
├── pdfs/
│   └── {permitId}/
│       └── permit-{permitNumber}.pdf
└── temp/
    └── {uploadId}/
```

### 9.5 CI/CD Requirements

> **Note:** Detailed CI/CD implementation is documented in [WORKFLOW.md](./WORKFLOW.md). This section defines the requirements.

#### CI/CD Pipeline Requirements

| Requirement | Description |
|-------------|-------------|
| **NFR-CICD-001** | Automated testing on every pull request |
| **NFR-CICD-002** | Linting and type checking enforced |
| **NFR-CICD-003** | Automated deployment to staging on merge to `develop` |
| **NFR-CICD-004** | Manual approval required for production deployment |
| **NFR-CICD-005** | Zero-downtime deployments in production |
| **NFR-CICD-006** | Rollback capability within 5 minutes |
| **NFR-CICD-007** | Database migrations run automatically |
| **NFR-CICD-008** | Environment secrets managed securely |

#### Pipeline Stages

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│    Lint     │───▶│    Test     │───▶│    Build    │───▶│   Deploy    │
│  TypeCheck  │    │  Unit/E2E   │    │   Docker    │    │  Railway    │
└─────────────┘    └─────────────┘    └─────────────┘    └─────────────┘
```

#### Branch Strategy

| Branch | Purpose | Deployment |
|--------|---------|------------|
| `main` | Production-ready code | Production |
| `develop` | Integration branch | Staging |
| `feature/*` | New features | Preview (optional) |
| `fix/*` | Bug fixes | Preview (optional) |

---

## 10. MVP Scope & Phasing

### 10.1 MVP Feature Checklist

#### Must Have (MVP)

- [x] User authentication (JWT)
- [x] Role-based access (4 roles)
- [x] Project management (Super Admin)
- [x] User assignment to projects
- [x] Template builder (Coordinator)
- [x] 4 permit types (HW, CSE, WAH, EW)
- [x] Multi-step permit form (Receiver)
- [x] File upload to R2
- [x] Draft saving
- [x] Issuer approval workflow
- [x] Coordinator final approval
- [x] Digital signature capture
- [x] Daily re-approval cycle
- [x] Permit suspension on missed approval
- [x] Permit close-out (Coordinator)
- [x] Email notifications
- [x] In-app notifications
- [x] Push notifications
- [x] PDF generation
- [x] Permit status tracking
- [x] Audit trail logging
- [x] Mobile responsive design
- [x] Docker containerization

#### Nice to Have (Phase 2)

- [ ] PWA offline mode
- [ ] Bulk daily approval
- [ ] Analytics dashboard
- [ ] Advanced reporting
- [ ] Data import/migration
- [ ] Template versioning UI
- [ ] Notification preferences

#### Future (Phase 3+)

- [ ] Native mobile app
- [ ] WebSocket real-time updates
- [ ] Extended hours permit type
- [ ] Risk assessment integration
- [ ] API for external systems

### 10.2 Scaling Path (50 → 500+ Users)

| Trigger | Action |
|---------|--------|
| 100+ users | Add Redis caching for frequent queries |
| 200+ users | Enable read replica for PostgreSQL |
| 300+ users | Horizontal scaling (multiple API instances) |
| 500+ users | Evaluate Kubernetes or managed services |

---

## 11. Non-Functional Requirements

### 11.1 Performance

| Metric | Requirement |
|--------|-------------|
| Page Load Time | < 3 seconds (3G network) |
| API Response Time | < 500ms (95th percentile) |
| PDF Generation | < 15 seconds |
| File Upload | < 30 seconds for 25MB |
| Concurrent Users | Support 100 simultaneous (MVP) |
| Daily Permit Volume | Handle 100 permits/day |

### 11.2 Availability

| Metric | Requirement |
|--------|-------------|
| Uptime | 99.5% (excluding planned maintenance) |
| Planned Maintenance Window | Weekends 2-4 AM |
| Recovery Time Objective (RTO) | < 4 hours |
| Recovery Point Objective (RPO) | < 1 hour |

### 11.3 Scalability

| Growth Phase | Architecture |
|--------------|--------------|
| MVP (1-100 users) | Single instance per service |
| Growth (100-300 users) | Add Redis caching, optimize queries |
| Scale (300-500 users) | Horizontal scaling, read replicas |
| Enterprise (500+ users) | Kubernetes, managed services |

### 11.4 Compatibility

| Platform | Requirement |
|----------|-------------|
| Desktop Browsers | Chrome, Firefox, Safari, Edge (latest 2 versions) |
| Mobile Browsers | Chrome Mobile, Safari iOS (latest 2 versions) |
| Minimum Screen | 320px width |
| Offline | PWA support (Phase 2) |

---

## 12. Security Requirements

### 12.1 Authentication & Authorization

| Requirement | Implementation |
|-------------|----------------|
| Password hashing | bcrypt (cost factor 12) |
| JWT tokens | Access (15min) + Refresh (7 days) |
| Token storage | httpOnly cookies |
| RBAC enforcement | Middleware on all routes |
| Session management | Redis-backed sessions |

### 12.2 Data Protection

| Requirement | Implementation |
|-------------|----------------|
| Data Encryption (transit) | TLS 1.3 |
| Data Encryption (rest) | AES-256 (database, R2) |
| PII handling | Minimal collection, encrypted storage |
| Audit logging | All state changes logged |
| Input validation | Server-side Zod validation |

### 12.3 Infrastructure Security

| Requirement | Implementation |
|-------------|----------------|
| Network isolation | Railway private networking |
| DDoS protection | Cloudflare |
| Rate limiting | Express rate-limiter + Redis |
| Secrets management | Railway environment variables |
| Dependency scanning | npm audit, Snyk (CI/CD) |

### 12.4 Compliance

| Standard | Implementation |
|----------|----------------|
| Data Retention | Permits kept 7 years minimum |
| Audit Trail | Immutable logs for all actions |
| Signature Validity | Timestamp + user ID recorded |
| File Archive | All PDFs stored in R2 |
| GDPR readiness | Data export/deletion capability |

---

## 13. Risks & Mitigations

### 13.1 Technical Risks

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Railway downtime | Low | High | Monitor status, have failover plan |
| R2 upload failures | Low | Medium | Retry logic, fallback to direct upload |
| PDF generation timeout | Medium | Medium | Queue-based generation, progress tracking |
| Database connection limits | Medium | High | Connection pooling, PgBouncer |

### 13.2 User Adoption Risks

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Mixed tech comfort levels | High | High | Simple UI, minimal steps |
| Daily re-approval friction | Medium | High | One-tap approve, push notifications |
| Form abandonment | Medium | Medium | Auto-save, mobile-optimized |

### 13.3 Operational Risks

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Small team maintenance | Medium | Medium | Comprehensive documentation |
| Cost overruns | Low | Medium | Monitor Railway billing, set alerts |
| Data loss | Very Low | Critical | Daily backups, point-in-time recovery |

---

## 14. Success Metrics

### 14.1 Primary KPIs

| Metric | Current | Target (3 months) | Measurement |
|--------|---------|-------------------|-------------|
| Average approval time | 2-3 days | < 4 hours | System timestamps |
| Lost permits | 5-10/month | 0 | Audit reports |
| Daily re-approval compliance | N/A | > 95% | System tracking |
| Digital permit adoption | 0% | 100% new permits | Usage statistics |

### 14.2 Technical KPIs

| Metric | Target | Measurement |
|--------|--------|-------------|
| System uptime | > 99.5% | Railway monitoring |
| API error rate | < 1% | Sentry tracking |
| Page load time | < 3s | Analytics |
| User-reported bugs | < 5/week (launch), < 1/week (stable) | Issue tracking |

---

## 15. Future Roadmap

### Phase 2 (Post-MVP): Enhancement

| Feature | Priority | Effort |
|---------|----------|--------|
| PWA offline mode | High | 2-3 weeks |
| Bulk daily approval | High | 1 week |
| Analytics dashboard | Medium | 2 weeks |
| WebSocket notifications | Medium | 1 week |
| Notification preferences | Medium | 1 week |

### Phase 3: Advanced Features

| Feature | Priority | Effort |
|---------|----------|--------|
| Native mobile app | Medium | 6-8 weeks |
| Advanced reporting | Medium | 2-3 weeks |
| Risk assessment integration | Low | 3-4 weeks |
| API for external systems | Low | 2 weeks |

### Scaling Milestones

| Users | Infrastructure Change |
|-------|----------------------|
| 100+ | Add caching layer |
| 200+ | Database read replica |
| 300+ | Multiple API instances |
| 500+ | Kubernetes evaluation |

---

## 16. Appendices

### Appendix A: Glossary

| Term | Definition |
|------|------------|
| PTW | Permit To Work - authorization system for hazardous activities |
| Receiver | Team lead/supervisor who requests permits |
| Issuer | First-level approver (site supervisor) |
| Coordinator | Final approver (safety coordinator) |
| Daily Re-Approval | Daily confirmation that permit conditions still apply |
| Close-Out | Formal ending of a permit by Coordinator |
| R2 | Cloudflare R2 - S3-compatible object storage |
| BullMQ | Redis-based job queue for Node.js |

### Appendix B: API Response Format

```typescript
// Success Response
{
  "success": true,
  "data": { ... },
  "meta": {
    "page": 1,
    "limit": 20,
    "total": 100
  }
}

// Error Response
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      { "field": "email", "message": "Invalid email format" }
    ]
  }
}
```

### Appendix C: File Upload Flow

```
1. Client requests upload URL
   POST /api/v1/files/upload-url
   Body: { fileName, fileType, permitId }

2. Server generates pre-signed URL
   Response: { uploadUrl, fileKey, expiresAt }

3. Client uploads directly to R2
   PUT {uploadUrl}
   Body: <file-binary>

4. Client confirms upload
   POST /api/v1/files/confirm
   Body: { fileKey, permitId }

5. Server creates PermitFile record
   Response: { fileId, downloadUrl }
```

### Appendix D: Environment Variable Reference

| Variable | Required | Description |
|----------|----------|-------------|
| `NODE_ENV` | Yes | Environment (development/production) |
| `DATABASE_URL` | Yes | PostgreSQL connection string |
| `REDIS_URL` | Yes | Redis connection string |
| `JWT_SECRET` | Yes | JWT signing secret |
| `JWT_REFRESH_SECRET` | Yes | Refresh token secret |
| `R2_ACCOUNT_ID` | Yes | Cloudflare account ID |
| `R2_ACCESS_KEY_ID` | Yes | R2 access key |
| `R2_SECRET_ACCESS_KEY` | Yes | R2 secret key |
| `R2_BUCKET_NAME` | Yes | R2 bucket name |
| `RESEND_API_KEY` | Yes | Resend API key |
| `SENTRY_DSN` | No | Sentry error tracking |
| `APP_URL` | Yes | Frontend URL |

---

## Document History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | January 14, 2026 | Product Team | Initial PRD |
| 2.0 | January 15, 2026 | Product Team | Separate backend, Docker, R2, CI/CD requirements |

---

*This PRD was updated based on architectural research and technical analysis for scalability and maintainability.*
