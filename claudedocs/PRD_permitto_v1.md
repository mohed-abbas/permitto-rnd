# Permitto - Product Requirements Document (PRD)

**Version:** 1.0
**Date:** January 14, 2026
**Author:** Product Team
**Status:** Draft for Review

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
9. [MVP Scope & Phasing](#9-mvp-scope--phasing)
10. [Non-Functional Requirements](#10-non-functional-requirements)
11. [Risks & Mitigations](#11-risks--mitigations)
12. [Success Metrics](#12-success-metrics)
13. [Future Roadmap](#13-future-roadmap)
14. [Appendices](#14-appendices)

---

## 1. Executive Summary

### 1.1 Product Overview

**Permitto** is an in-house Permit To Work (PTW) web application designed to digitize and streamline the permit approval process for construction and manufacturing operations. The system replaces paper-based permit management with a modern, mobile-friendly digital workflow.

### 1.2 Business Context

- **Type:** Internal tool (not SaaS)
- **Industry:** Construction & Manufacturing
- **Target Users:** Internal employees (team leads, supervisors, coordinators)
- **Deployment:** Single-tenant, company-owned

### 1.3 Key Objectives

| Objective | Current State | Target State |
|-----------|--------------|--------------|
| Approval Time | Days (physical transport) | Hours (digital workflow) |
| Permit Tracking | Manual, error-prone | Real-time visibility |
| Compliance | Paper records | Digital audit trail |
| Accessibility | Office-bound | Mobile-first |

### 1.4 Success Criteria

- Reduce permit approval cycle from days to < 4 hours
- 100% digital permit issuance within 3 months of launch
- Zero lost/misplaced permits
- Complete audit trail for all permit activities

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

### 3.3 Key Differentiators vs. Paper Process

| Aspect | Paper | Permitto |
|--------|-------|----------|
| Approval notification | None | Push + Email + Dashboard |
| Status visibility | Unknown | Real-time tracking |
| Daily re-approval | Physical signatures | One-tap digital |
| Storage | Filing cabinets | Searchable database |
| Audit trail | Manual logs | Automatic timestamps |

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
| Approve (Level 1) | ❌ | ❌ | ✅ | ❌ |
| Approve (Final) | ❌ | ✅ | ❌ | ❌ |
| Daily Re-Approve | ❌ | ✅ | ✅ | ✅ |
| Close Permit | ❌ | ✅ | ❌ | ❌ |
| View All Permits | ✅ | ✅ | Project Only | Own Only |
| View Analytics | ✅ | ✅ | ❌ | ❌ |

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

#### FR-AUTH-002: Role-Based Access Control
- **Description:** System enforces permissions based on user role
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Each user has exactly one role
  - Permissions enforced at API level
  - UI hides inaccessible features
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

#### FR-PROJ-002: View Projects
- **Description:** Users see their assigned projects
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Dashboard shows project list
  - Each project shows permit statistics
  - Filter by active/all projects

### 5.4 Permit Template Management

#### FR-TMPL-001: Template Builder
- **Description:** Coordinators create permit templates with custom fields
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Drag-and-drop field placement
  - Multi-step form support (sections)
  - Field types: text, textarea, number, date, time, select, multiselect, checkbox, radio
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
- **Acceptance Criteria:**
  - Each type has a unique code for permit IDs
  - Templates assigned to types
  - Types can have multiple templates (active/archived)

#### FR-TMPL-003: Template Versioning
- **Description:** Track template changes
- **Priority:** P1 (MVP)
- **Acceptance Criteria:**
  - Editing published template creates new version
  - Existing permits remain linked to their original version
  - Version history viewable

### 5.5 Permit Request (Receiver)

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

#### FR-PERM-003: Preview Before Submit
- **Description:** Receivers preview permit as PDF before submitting
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - PDF preview shows all entered data
  - Can return to edit
  - Clear submit button
  - Confirmation dialog before final submit

#### FR-PERM-004: Submit Permit
- **Description:** Receivers submit permit for approval
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Submit triggers workflow
  - Notification sent to Issuer
  - Permit status changes to "Submitted"
  - Receiver cannot edit after submit

### 5.6 Permit Approval (Issuer)

#### FR-APPR-001: View Pending Permits
- **Description:** Issuers see permits awaiting their approval
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Dashboard shows pending permits
  - Sort by submission time, priority, project
  - Filter by permit type, project
  - Count of pending items visible

#### FR-APPR-002: Review Permit Details
- **Description:** Issuers review full permit details
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - All form data visible
  - Receiver info displayed
  - Project context shown
  - Can download PDF preview

#### FR-APPR-003: Approve Permit
- **Description:** Issuers approve permits
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Approve button requires signature
  - Digital signature captured (drawn)
  - Status changes to "Issuer Approved"
  - Auto-forward to Coordinator
  - Notification sent to Coordinator
  - Notification sent to Receiver (approved by Issuer)

#### FR-APPR-004: Reject Permit
- **Description:** Issuers reject permits with feedback
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Reject requires reason (mandatory text)
  - Status changes to "Rejected by Issuer"
  - Notification sent to Receiver with feedback
  - Receiver can revise and resubmit

### 5.7 Final Approval (Coordinator)

#### FR-FINAL-001: Coordinator Approval Queue
- **Description:** Coordinators see permits awaiting final approval
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Shows permits in "Issuer Approved" status
  - Displays Issuer signature
  - Shows project and permit details

#### FR-FINAL-002: Final Approve
- **Description:** Coordinators give final approval
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Approve requires Coordinator signature
  - Status changes to "Approved"
  - PDF generated with both signatures
  - Notification sent to all parties
  - Permit becomes "Active"

#### FR-FINAL-003: Final Reject
- **Description:** Coordinators reject permits
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Reject requires reason
  - Status changes to "Rejected by Coordinator"
  - Notification sent to Receiver and Issuer
  - Receiver can revise and resubmit (restarts workflow)

### 5.8 Daily Re-Approval Cycle

#### FR-DAILY-001: Daily Approval Requirement
- **Description:** Active permits require daily re-approval
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Each day within permit validity, all parties must re-confirm
  - Order: Receiver → Issuer → Coordinator
  - Daily deadline: 8:00 AM
  - Re-approval = signature/confirmation only (no form re-entry)

#### FR-DAILY-002: Morning Notification
- **Description:** System sends daily re-approval reminders
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Push notification at 7:00 AM
  - Email digest of all pending re-approvals
  - Dashboard shows pending daily approvals prominently

#### FR-DAILY-003: One-Tap Daily Approve
- **Description:** Streamlined daily re-approval
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Approve from notification (no login required if authenticated)
  - Bulk approve multiple permits
  - Simple confirmation dialog
  - Timestamp recorded

#### FR-DAILY-004: Suspended Status
- **Description:** Permits suspend if daily approval missed
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - If any party misses 8:00 AM deadline, permit status = "Suspended"
  - Work cannot proceed until resolved
  - Notification to all parties
  - Can be reactivated by completing pending approvals

### 5.9 Permit Close-Out

#### FR-CLOSE-001: Expiry Notification
- **Description:** Notify all parties when permit reaches expiry
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Notification sent 24 hours before expiry
  - Notification sent at expiry time
  - All parties notified: Receiver, Issuer, Coordinator
  - Permit status changes to "Pending Close-Out"

#### FR-CLOSE-002: Coordinator Close-Out
- **Description:** Coordinators formally close permits
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Only Coordinator can close permit
  - Close-out requires: signature, completion notes
  - Status changes to "Closed"
  - Final PDF generated with close-out info
  - Notification sent to all parties

#### FR-CLOSE-003: Early Close-Out
- **Description:** Close permit before expiry if work completed
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Receiver can request early close-out
  - Coordinator confirms and closes
  - Same close-out process as expiry

### 5.10 PDF Generation

#### FR-PDF-001: Permit PDF Layout
- **Description:** Generate printable PDF of approved permit
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - All form data in structured layout
  - Permit ID prominently displayed
  - Project name and permit type
  - Validity period (dates, daily hours 8AM-5PM)
  - All signatures with timestamps
  - QR code linking to digital permit
  - Company header/branding

#### FR-PDF-002: PDF Download
- **Description:** Users can download permit PDF
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Download available after final approval
  - Receiver, Issuer, Coordinator can all download
  - Print-optimized format (A4)

### 5.11 Notifications

#### FR-NOTIF-001: Email Notifications
- **Description:** Send transactional emails for key events
- **Priority:** P0 (MVP)
- **Events:**
  - New permit submitted (→ Issuer)
  - Permit approved by Issuer (→ Receiver, Coordinator)
  - Permit rejected by Issuer (→ Receiver)
  - Permit approved by Coordinator (→ All)
  - Permit rejected by Coordinator (→ Receiver, Issuer)
  - Daily re-approval reminder (→ All with pending)
  - Permit expiry warning (→ All)
  - Permit suspended (→ All)
  - Permit closed (→ All)

#### FR-NOTIF-002: In-App Notifications
- **Description:** Real-time in-app notification center
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Notification bell with unread count
  - Notification list with timestamps
  - Click to navigate to relevant permit
  - Mark as read/unread
  - Bulk mark all as read

#### FR-NOTIF-003: Push Notifications
- **Description:** Browser push notifications
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Opt-in during first login
  - Immediate push for approvals, rejections
  - Daily digest for re-approvals
  - Can disable in preferences

### 5.12 Status Tracking

#### FR-TRACK-001: Permit Status Dashboard
- **Description:** View status of all permits user has access to
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Filter by status, project, type, date
  - Status badge with clear color coding
  - Click to view details
  - Search by permit ID or content

#### FR-TRACK-002: Status Timeline
- **Description:** View permit's approval history
- **Priority:** P0 (MVP)
- **Acceptance Criteria:**
  - Chronological list of all status changes
  - Who made each change
  - Timestamp for each
  - Comments/reasons where applicable

### 5.13 Permit ID Generation

#### FR-ID-001: Permit ID Format
- **Description:** Generate structured permit IDs
- **Priority:** P0 (MVP)
- **Format:** `{PROJECT_CODE}-{PERMIT_TYPE}-{YEAR}-{SEQUENCE}`
- **Example:** `DOWNTOWN-HW-2026-001`
- **Acceptance Criteria:**
  - Auto-generated on submit
  - Sequence is per-project, per-type, per-year
  - Always unique
  - Cannot be edited

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

### 6.3 Validity Rules

#### Standard Permit
- **Duration:** Up to 1 week
- **Daily Hours:** 8:00 AM - 5:00 PM
- **Daily Re-Approval:** Required from all parties before 8:00 AM
- **Close-Out:** Required from Coordinator

#### Extended Hours Permit
- **Duration:** 1 day only
- **Hours:** Outside 8:00 AM - 5:00 PM window
- **Same approval chain**
- **Auto-triggers close-out at day end**

### 6.4 Timing Rules

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
│ password        │     │ status          │
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
│ name            │     │ permitNumber    │
│ type            │◄────┤ templateId      │
│ schema (JSON)   │     │ projectId       │
│ version         │     │ receiverId      │
│ isActive        │     │ issuerId        │
│ createdBy       │     │ coordinatorId   │
│ createdAt       │     │ status          │
│ updatedAt       │     │ formData (JSON) │
└─────────────────┘     │ validFrom       │
                        │ validTo         │
                        │ dailyStartTime  │
                        │ dailyEndTime    │
                        │ issuerSignature │
                        │ issuerSignedAt  │
                        │ coordSignature  │
                        │ coordSignedAt   │
                        │ closeOutNotes   │
                        │ closedAt        │
                        │ createdAt       │
                        │ updatedAt       │
                        └────────┬────────┘
                                 │
                    ┌────────────┴────────────┐
                    │                         │
           ┌────────▼────────┐     ┌──────────▼──────────┐
           │  DailyApproval  │     │  PermitAuditLog    │
           ├─────────────────┤     ├────────────────────┤
           │ id              │     │ id                 │
           │ permitId        │     │ permitId           │
           │ date            │     │ userId             │
           │ receiverApproved│     │ action             │
           │ receiverAt      │     │ previousStatus     │
           │ issuerApproved  │     │ newStatus          │
           │ issuerAt        │     │ comments           │
           │ coordApproved   │     │ timestamp          │
           │ coordAt         │     └────────────────────┘
           │ status          │
           └─────────────────┘

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
  id                String         @id @default(cuid())
  email             String         @unique
  name              String
  passwordHash      String
  role              Role
  phone             String?
  emailNotifications Boolean       @default(true)
  pushNotifications  Boolean       @default(true)
  createdAt         DateTime       @default(now())
  updatedAt         DateTime       @updatedAt

  // Relations
  projectAssignments ProjectUser[]
  templatesCreated   PermitTemplate[]
  permitsAsReceiver  Permit[]       @relation("ReceiverPermits")
  permitsAsIssuer    Permit[]       @relation("IssuerPermits")
  permitsAsCoord     Permit[]       @relation("CoordPermits")
  auditLogs          PermitAuditLog[]
  notifications      Notification[]
  assignedBy         ProjectUser[]  @relation("AssignedByUser")
}

model Project {
  id        String   @id @default(cuid())
  name      String
  code      String   @unique  // For permit ID prefix
  isActive  Boolean  @default(true)
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt

  // Relations
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
  schema      Json       // FormSchema structure
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
  permitNumber    String       @unique  // e.g., DOWNTOWN-HW-2026-001
  templateId      String
  projectId       String
  receiverId      String
  issuerId        String?
  coordinatorId   String?

  status          PermitStatus @default(DRAFT)
  formData        Json         // Submitted form data

  // Validity
  validFrom       DateTime
  validTo         DateTime
  dailyStartTime  String       @default("08:00")  // HH:mm
  dailyEndTime    String       @default("17:00")  // HH:mm
  isExtendedHours Boolean      @default(false)

  // Signatures
  issuerSignature    String?   // Base64 image
  issuerSignedAt     DateTime?
  coordSignature     String?   // Base64 image
  coordSignedAt      DateTime?

  // Close-out
  closeOutNotes   String?
  closedAt        DateTime?

  createdAt       DateTime     @default(now())
  updatedAt       DateTime     @updatedAt

  // Relations
  template        PermitTemplate @relation(fields: [templateId], references: [id])
  project         Project        @relation(fields: [projectId], references: [id])
  receiver        User           @relation("ReceiverPermits", fields: [receiverId], references: [id])
  issuer          User?          @relation("IssuerPermits", fields: [issuerId], references: [id])
  coordinator     User?          @relation("CoordPermits", fields: [coordinatorId], references: [id])
  dailyApprovals  DailyApproval[]
  auditLogs       PermitAuditLog[]
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
  action         String       // Status change or action name
  previousStatus PermitStatus?
  newStatus      PermitStatus?
  comments       String?
  metadata       Json?        // Additional context
  timestamp      DateTime     @default(now())

  permit         Permit       @relation(fields: [permitId], references: [id])
  user           User         @relation(fields: [userId], references: [id])
}

model Notification {
  id        String   @id @default(cuid())
  userId    String
  type      String   // e.g., "permit_submitted", "daily_reminder"
  title     String
  message   String
  data      Json?    // Reference IDs, etc.
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

### 7.3 Form Schema TypeScript Interfaces

```typescript
// types/form-schema.ts

export interface FormSchema {
  steps: FormStep[];
  metadata: {
    estimatedMinutes: number;
    requiredSignatures: ('issuer' | 'coordinator')[];
    validityOptions: {
      maxDays: number;
      defaultDays: number;
      allowExtendedHours: boolean;
    };
  };
}

export interface FormStep {
  id: string;
  title: string;
  description?: string;
  fields: FormField[];
}

export type FieldType =
  | 'text'
  | 'textarea'
  | 'number'
  | 'date'
  | 'time'
  | 'datetime'
  | 'select'
  | 'multiselect'
  | 'checkbox'
  | 'checkbox-group'  // For compliance checklists
  | 'radio'
  | 'signature';

export interface FormField {
  id: string;
  type: FieldType;
  label: string;
  placeholder?: string;
  helpText?: string;
  required: boolean;
  defaultValue?: any;

  validation?: {
    min?: number;
    max?: number;
    minLength?: number;
    maxLength?: number;
    pattern?: string;
    patternMessage?: string;
  };

  // For select/radio/checkbox-group
  options?: {
    label: string;
    value: string;
  }[];

  // Conditional display
  conditionalOn?: {
    fieldId: string;
    operator: 'equals' | 'not_equals' | 'contains' | 'is_checked';
    value: any;
  };

  // Layout
  width?: 'full' | 'half' | 'third';
}

// Example: Hot Work Permit Template Schema
export const hotWorkTemplateExample: FormSchema = {
  steps: [
    {
      id: 'general',
      title: 'General Information',
      fields: [
        {
          id: 'work_location',
          type: 'text',
          label: 'Work Location',
          placeholder: 'Building/Floor/Area',
          required: true,
        },
        {
          id: 'work_description',
          type: 'textarea',
          label: 'Description of Work',
          required: true,
        },
        {
          id: 'start_date',
          type: 'date',
          label: 'Start Date',
          required: true,
        },
        {
          id: 'end_date',
          type: 'date',
          label: 'End Date',
          required: true,
        },
      ],
    },
    {
      id: 'hazards',
      title: 'Hazard Assessment',
      fields: [
        {
          id: 'hazard_types',
          type: 'checkbox-group',
          label: 'Identified Hazards',
          required: true,
          options: [
            { label: 'Fire/Explosion', value: 'fire' },
            { label: 'Toxic Fumes', value: 'fumes' },
            { label: 'Hot Surfaces', value: 'hot_surfaces' },
            { label: 'Sparks/Splatter', value: 'sparks' },
          ],
        },
      ],
    },
    {
      id: 'compliance',
      title: 'Safety Compliance',
      description: 'Confirm all safety requirements are met',
      fields: [
        {
          id: 'fire_extinguisher',
          type: 'checkbox',
          label: 'Fire extinguisher present and accessible',
          required: true,
        },
        {
          id: 'fire_watch',
          type: 'checkbox',
          label: 'Fire watch assigned for duration of work',
          required: true,
        },
        {
          id: 'ventilation',
          type: 'checkbox',
          label: 'Adequate ventilation ensured',
          required: true,
        },
        {
          id: 'combustibles_removed',
          type: 'checkbox',
          label: 'Combustible materials removed or protected',
          required: true,
        },
      ],
    },
  ],
  metadata: {
    estimatedMinutes: 10,
    requiredSignatures: ['issuer', 'coordinator'],
    validityOptions: {
      maxDays: 7,
      defaultDays: 1,
      allowExtendedHours: true,
    },
  },
};
```

---

## 8. Technical Architecture

### 8.1 System Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                            │
│                                                                 │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────────┐ │
│  │   Mobile    │  │   Tablet    │  │        Desktop          │ │
│  │  (PWA)      │  │   (PWA)     │  │       (Web App)         │ │
│  └──────┬──────┘  └──────┬──────┘  └────────────┬────────────┘ │
│         └────────────────┴─────────────────────┬┘              │
│                          │                                      │
│                    Next.js Frontend                             │
│         ┌────────────────┴────────────────┐                    │
│         │  shadcn/ui + Tailwind CSS       │                    │
│         │  React Hook Form + Zod          │                    │
│         │  Zustand (State Management)     │                    │
│         │  Framer Motion (Animations)     │                    │
│         └────────────────┬────────────────┘                    │
└──────────────────────────┼──────────────────────────────────────┘
                           │ HTTPS
┌──────────────────────────┼──────────────────────────────────────┐
│                          │                                      │
│                    VERCEL EDGE                                  │
│                          │                                      │
│         ┌────────────────┴────────────────┐                    │
│         │      Next.js API Routes         │                    │
│         │  ┌──────────────────────────┐   │                    │
│         │  │   NextAuth.js (Auth)     │   │                    │
│         │  │   Prisma ORM             │   │                    │
│         │  │   Business Logic         │   │                    │
│         │  │   Validation (Zod)       │   │                    │
│         │  └──────────────────────────┘   │                    │
│         │                                  │                    │
│         │  ┌──────────────────────────┐   │                    │
│         │  │   Serverless Functions   │   │                    │
│         │  │   - PDF Generation       │   │                    │
│         │  │   - Email Sending        │   │                    │
│         │  │   - Scheduled Jobs       │   │                    │
│         │  └──────────────────────────┘   │                    │
│         └────────────────┬────────────────┘                    │
└──────────────────────────┼──────────────────────────────────────┘
                           │
┌──────────────────────────┼──────────────────────────────────────┐
│                    RAILWAY                                      │
│                          │                                      │
│    ┌─────────────────────┼─────────────────────┐               │
│    │                     │                     │                │
│    ▼                     ▼                     ▼                │
│  ┌────────────┐   ┌────────────┐    ┌────────────────┐         │
│  │ PostgreSQL │   │   Redis    │    │  (Future:      │         │
│  │            │   │            │    │   Background   │         │
│  │ - Users    │   │ - Sessions │    │   Workers)     │         │
│  │ - Permits  │   │ - Cache    │    └────────────────┘         │
│  │ - Templates│   │ - Pub/Sub  │                                │
│  │ - Logs     │   │            │                                │
│  └────────────┘   └────────────┘                                │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                    EXTERNAL SERVICES                            │
│                                                                 │
│  ┌────────────┐   ┌────────────┐   ┌────────────────┐          │
│  │  SendGrid  │   │   Sentry   │   │  (Future:      │          │
│  │  (Email)   │   │  (Errors)  │   │   Cloudinary)  │          │
│  └────────────┘   └────────────┘   └────────────────┘          │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### 8.2 Tech Stack Summary

| Layer | Technology | Purpose |
|-------|------------|---------|
| **Frontend** | Next.js 14+ (App Router) | SSR, routing, API routes |
| **UI Components** | shadcn/ui + Tailwind CSS | Design system |
| **Forms** | React Hook Form + Zod | Form handling, validation |
| **State** | Zustand | Client-side state |
| **Animations** | Framer Motion | UI transitions |
| **Auth** | NextAuth.js (Auth.js) | Authentication + RBAC |
| **ORM** | Prisma | Database access |
| **Database** | PostgreSQL | Primary data store |
| **Cache** | Redis | Sessions, real-time |
| **PDF** | @react-pdf/renderer or Puppeteer | PDF generation |
| **Signatures** | react-signature-canvas | Signature capture |
| **Email** | SendGrid/Resend | Transactional email |
| **Hosting** | Vercel + Railway | App + DB hosting |
| **Monitoring** | Sentry | Error tracking |

### 8.3 API Design

#### Authentication Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/login` | User login |
| POST | `/api/auth/logout` | User logout |
| POST | `/api/auth/forgot-password` | Request password reset |
| POST | `/api/auth/reset-password` | Reset password |
| GET | `/api/auth/me` | Get current user |

#### User Management Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/users` | List users (admin) |
| POST | `/api/users` | Create user |
| GET | `/api/users/:id` | Get user details |
| PATCH | `/api/users/:id` | Update user |
| DELETE | `/api/users/:id` | Deactivate user |

#### Project Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/projects` | List projects |
| POST | `/api/projects` | Create project (admin) |
| GET | `/api/projects/:id` | Get project details |
| PATCH | `/api/projects/:id` | Update project |
| POST | `/api/projects/:id/users` | Assign user to project |
| DELETE | `/api/projects/:id/users/:userId` | Remove user from project |

#### Template Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/templates` | List templates |
| POST | `/api/templates` | Create template |
| GET | `/api/templates/:id` | Get template details |
| PATCH | `/api/templates/:id` | Update template |
| POST | `/api/templates/:id/publish` | Publish template |
| POST | `/api/templates/:id/archive` | Archive template |

#### Permit Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/permits` | List permits |
| POST | `/api/permits` | Create permit (draft) |
| GET | `/api/permits/:id` | Get permit details |
| PATCH | `/api/permits/:id` | Update permit draft |
| POST | `/api/permits/:id/submit` | Submit permit |
| POST | `/api/permits/:id/approve` | Approve permit (Issuer/Coord) |
| POST | `/api/permits/:id/reject` | Reject permit |
| POST | `/api/permits/:id/daily-approve` | Daily re-approval |
| POST | `/api/permits/:id/close` | Close permit (Coord) |
| GET | `/api/permits/:id/pdf` | Generate/download PDF |
| GET | `/api/permits/:id/timeline` | Get audit timeline |

#### Notification Endpoints

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/notifications` | List user notifications |
| PATCH | `/api/notifications/:id/read` | Mark as read |
| POST | `/api/notifications/read-all` | Mark all as read |

### 8.4 Scheduled Jobs

| Job | Schedule | Description |
|-----|----------|-------------|
| Daily Reminder | 7:00 AM | Send re-approval reminders |
| Check Suspensions | 8:05 AM | Suspend permits with missed approvals |
| Expiry Warning | 12:00 AM | Send 24h expiry warnings |
| Process Expirations | 5:00 PM | Move expired permits to PENDING_CLOSEOUT |

---

## 9. MVP Scope & Phasing

### 9.1 MVP Timeline: 6 Weeks

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         MVP DEVELOPMENT TIMELINE                        │
├─────────────────────────────────────────────────────────────────────────┤
│ Week 1-2: Foundation                                                    │
│ ├── Project setup (Next.js, Prisma, PostgreSQL)                        │
│ ├── Authentication + RBAC                                               │
│ ├── User management CRUD                                                │
│ ├── Project management                                                  │
│ └── Basic dashboard layout                                              │
├─────────────────────────────────────────────────────────────────────────┤
│ Week 3: Templates                                                       │
│ ├── Template builder UI                                                 │
│ ├── Form schema storage                                                 │
│ ├── Template CRUD                                                       │
│ └── 4 default templates (HW, CSE, WAH, EW)                             │
├─────────────────────────────────────────────────────────────────────────┤
│ Week 4: Core Workflow                                                   │
│ ├── Multi-step permit form                                              │
│ ├── Draft saving                                                        │
│ ├── Submit to Issuer                                                    │
│ ├── Issuer approval/rejection                                           │
│ ├── Coordinator final approval                                          │
│ └── Signature capture                                                   │
├─────────────────────────────────────────────────────────────────────────┤
│ Week 5: Daily & Notifications                                           │
│ ├── Daily re-approval system                                            │
│ ├── Email notifications                                                 │
│ ├── In-app notifications                                                │
│ ├── Push notifications                                                  │
│ └── Status tracking dashboard                                           │
├─────────────────────────────────────────────────────────────────────────┤
│ Week 6: PDF & Polish                                                    │
│ ├── PDF generation with signatures                                      │
│ ├── Permit close-out                                                    │
│ ├── Mobile responsive refinements                                       │
│ ├── Testing & bug fixes                                                 │
│ └── User acceptance testing                                             │
└─────────────────────────────────────────────────────────────────────────┘
```

### 9.2 MVP Feature Checklist

#### Must Have (MVP)

- [x] User authentication (email/password)
- [x] Role-based access (4 roles)
- [x] Project management (Super Admin)
- [x] User assignment to projects
- [x] Template builder (Coordinator)
- [x] 4 permit types (HW, CSE, WAH, EW)
- [x] Multi-step permit form (Receiver)
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

#### Nice to Have (Phase 2)

- [ ] PWA offline mode
- [ ] Bulk daily approval
- [ ] Analytics dashboard
- [ ] Advanced reporting
- [ ] Data import/migration
- [ ] Template versioning UI
- [ ] User profile settings
- [ ] Notification preferences

#### Future (Phase 3+)

- [ ] Native mobile app
- [ ] Extended hours permit type
- [ ] Risk assessment integration
- [ ] API for external systems
- [ ] SIMOPS management
- [ ] AI-assisted form filling

### 9.3 Parallel Running Strategy

```
Week 1-2: Internal testing
    │
    ▼
Week 3-4: Pilot with 1 project (paper backup)
    │
    ▼
Week 5-6: Expand to 2-3 projects
    │
    ▼
Week 7-8: All projects (paper for active permits only)
    │
    ▼
Week 9+: Full digital (paper archived)
```

---

## 10. Non-Functional Requirements

### 10.1 Performance

| Metric | Requirement |
|--------|-------------|
| Page Load Time | < 3 seconds (3G network) |
| API Response Time | < 500ms (95th percentile) |
| PDF Generation | < 10 seconds |
| Concurrent Users | Support 50 simultaneous |
| Daily Permit Volume | Handle 100 permits/day |

### 10.2 Availability

| Metric | Requirement |
|--------|-------------|
| Uptime | 99.5% (excluding planned maintenance) |
| Planned Maintenance Window | Weekends 2-4 AM |
| Recovery Time Objective (RTO) | < 4 hours |
| Recovery Point Objective (RPO) | < 1 hour |

### 10.3 Security

| Requirement | Implementation |
|-------------|----------------|
| Data Encryption (transit) | TLS 1.3 |
| Data Encryption (rest) | AES-256 (database) |
| Authentication | Password hashing (bcrypt) |
| Session Management | JWT with refresh tokens |
| RBAC Enforcement | API middleware + DB policies |
| Audit Logging | All state changes logged |
| Input Validation | Server-side Zod validation |

### 10.4 Scalability

| Growth Phase | Architecture |
|--------------|--------------|
| MVP (1-50 users) | Single Vercel instance + Railway |
| Growth (50-200 users) | Add Redis caching, optimize queries |
| Scale (200+ users) | Evaluate Railway upgrade or migration |

### 10.5 Compatibility

| Platform | Requirement |
|----------|-------------|
| Desktop Browsers | Chrome, Firefox, Safari, Edge (latest 2 versions) |
| Mobile Browsers | Chrome Mobile, Safari iOS (latest 2 versions) |
| Minimum Screen | 320px width |
| Offline | PWA support (Phase 2) |

### 10.6 Compliance

| Standard | Implementation |
|----------|----------------|
| Data Retention | Permits kept 7 years minimum |
| Audit Trail | Immutable logs for all actions |
| Signature Validity | Timestamp + user ID recorded |
| PDF Archive | All final PDFs stored |

---

## 11. Risks & Mitigations

### 11.1 Technical Risks

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| PDF generation performance | Medium | High | Use serverless functions with 1GB+ memory; cache generated PDFs |
| Daily job reliability | Medium | High | Use Vercel Cron or Inngest for reliable scheduling |
| Real-time notification delays | Low | Medium | Start with polling; add WebSocket later if needed |
| Signature storage size | Low | Low | Compress signature images; limit canvas size |

### 11.2 User Adoption Risks

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Mixed tech comfort levels | High | High | Simple UI, minimal steps, clear labels |
| Daily re-approval friction | Medium | High | One-tap approve, push notifications, bulk operations |
| Form abandonment | Medium | Medium | Progress indicator, auto-save, mobile-optimized |
| Notification fatigue | Low | Medium | Configurable preferences (Phase 2) |

### 11.3 Business Risks

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Parallel running confusion | Medium | Medium | Clear process documentation; status badges |
| Compliance gaps | Low | High | Build audit trail from day 1; coordinator reviews |
| Approval bottlenecks persist | Low | Medium | Mobile notifications; escalation rules (Phase 2) |

### 11.4 Operational Risks

| Risk | Likelihood | Impact | Mitigation |
|------|------------|--------|------------|
| Small team maintenance burden | Medium | Medium | Choose managed services; comprehensive documentation |
| Vercel/Railway downtime | Low | High | Monitor status pages; have manual backup process |
| Database corruption | Very Low | Critical | Daily backups; point-in-time recovery enabled |

---

## 12. Success Metrics

### 12.1 Primary KPIs

| Metric | Current | Target (3 months) | Measurement |
|--------|---------|-------------------|-------------|
| Average approval time | 2-3 days | < 4 hours | System timestamps |
| Lost permits | 5-10/month | 0 | Audit reports |
| Daily re-approval compliance | N/A | > 95% | System tracking |
| Digital permit adoption | 0% | 100% new permits | Usage statistics |

### 12.2 Secondary KPIs

| Metric | Target | Measurement |
|--------|--------|-------------|
| User satisfaction | > 4/5 stars | Post-launch survey |
| Mobile usage | > 60% of approvals | Analytics |
| Form completion rate | > 90% | Draft vs submitted |
| PDF downloads | Track usage | Download counts |

### 12.3 Technical KPIs

| Metric | Target | Measurement |
|--------|--------|-------------|
| System uptime | > 99.5% | Monitoring |
| API error rate | < 1% | Sentry tracking |
| Page load time | < 3s | Vercel Analytics |
| User-reported bugs | < 5/week (launch), < 1/week (stable) | Issue tracking |

---

## 13. Future Roadmap

### Phase 2 (Post-MVP): Enhancement

| Feature | Priority | Effort |
|---------|----------|--------|
| PWA offline mode | High | 2-3 weeks |
| Bulk daily approval | High | 1 week |
| Analytics dashboard | Medium | 2 weeks |
| Notification preferences | Medium | 1 week |
| Extended hours permit workflow | Medium | 1 week |
| Data import tool | Low | 2 weeks |

### Phase 3: Advanced Features

| Feature | Priority | Effort |
|---------|----------|--------|
| Native mobile app (React Native) | Medium | 6-8 weeks |
| Advanced reporting | Medium | 2-3 weeks |
| Risk assessment integration | Low | 3-4 weeks |
| API for external systems | Low | 2 weeks |

### Phase 4: Enterprise Features (if needed)

| Feature | Priority | Effort |
|---------|----------|--------|
| Multi-organization support | Low | 4-6 weeks |
| SSO integration | Low | 2 weeks |
| Custom branding | Low | 1 week |
| SIMOPS management | Low | 4+ weeks |

---

## 14. Appendices

### Appendix A: Glossary

| Term | Definition |
|------|------------|
| PTW | Permit To Work - authorization system for hazardous activities |
| Receiver | Team lead/supervisor who requests permits |
| Issuer | First-level approver (site supervisor) |
| Coordinator | Final approver (safety coordinator) |
| Daily Re-Approval | Daily confirmation that permit conditions still apply |
| Close-Out | Formal ending of a permit by Coordinator |
| SIMOPS | Simultaneous Operations management |
| HW | Hot Work (welding, cutting, etc.) |
| CSE | Confined Space Entry |
| WAH | Working at Height |
| EW | Electrical Work |

### Appendix B: User Journey Maps

#### Receiver Journey: Request Permit

```
1. Login to Permitto (mobile)
2. Dashboard shows assigned projects
3. Select project
4. Browse available permit templates
5. Select permit type (e.g., Hot Work)
6. Fill multi-step form:
   - Step 1: General info
   - Step 2: Hazard assessment
   - Step 3: Compliance checklist
7. Preview PDF
8. Submit permit
9. Receive notification when approved/rejected
10. Daily: Confirm permit still valid
11. Work completes: Request early close-out OR wait for expiry
```

#### Issuer Journey: Approve Permit

```
1. Receive push notification: "New permit submitted"
2. Open Permitto (mobile)
3. View pending permits dashboard
4. Select permit to review
5. Review all form data
6. Decision:
   - APPROVE: Sign digitally → forwarded to Coordinator
   - REJECT: Add reason → returned to Receiver
7. Daily: One-tap re-approve active permits (bulk)
```

#### Coordinator Journey: Final Approval

```
1. Receive notification: "Permit awaiting final approval"
2. Open Permitto (desktop/mobile)
3. View Issuer-approved permits
4. Review permit + Issuer signature
5. Decision:
   - APPROVE: Sign digitally → Permit active
   - REJECT: Add reason → returned to workflow
6. Daily: Re-approve active permits
7. Close-out: Review expired permits, sign off
```

### Appendix C: Notification Templates

#### Email: Permit Submitted

```
Subject: [Permitto] New Permit Awaiting Your Approval - {PermitNumber}

Hi {IssuerName},

A new permit has been submitted and requires your review.

Permit Details:
- Number: {PermitNumber}
- Type: {PermitType}
- Project: {ProjectName}
- Requested by: {ReceiverName}
- Validity: {ValidFrom} to {ValidTo}

[Review Permit]

---
You received this email because you are assigned as an Issuer on {ProjectName}.
```

#### Push: Daily Reminder

```
Title: Daily Approvals Pending
Body: You have {Count} permits requiring daily re-approval. Tap to approve.
```

### Appendix D: PDF Layout Specification

```
┌─────────────────────────────────────────────────────────────┐
│                     COMPANY LOGO + NAME                     │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  PERMIT TO WORK                                             │
│  ─────────────────                                          │
│  Type: Hot Work Permit                                      │
│  Number: DOWNTOWN-HW-2026-001                               │
│  Project: Downtown Construction                              │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  VALIDITY                                                   │
│  From: January 15, 2026                                     │
│  To: January 21, 2026                                       │
│  Daily Hours: 08:00 - 17:00                                 │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  PERMIT DETAILS                                             │
│  ─────────────────                                          │
│  Work Location: Building A, Floor 3, Area B2                │
│  Description: Welding of structural supports                │
│  ...                                                        │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  COMPLIANCE CHECKLIST                                       │
│  ─────────────────                                          │
│  [✓] Fire extinguisher present                              │
│  [✓] Fire watch assigned                                    │
│  [✓] Ventilation adequate                                   │
│  [✓] Combustibles removed                                   │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  APPROVALS                                                  │
│                                                             │
│  Requested by:          Issued by:          Approved by:    │
│  ┌──────────────┐      ┌──────────────┐    ┌──────────────┐│
│  │  [Signature] │      │  [Signature] │    │  [Signature] ││
│  └──────────────┘      └──────────────┘    └──────────────┘│
│  {ReceiverName}        {IssuerName}        {CoordName}     │
│  {Date/Time}           {Date/Time}         {Date/Time}     │
│                                                             │
├─────────────────────────────────────────────────────────────┤
│  QR Code: [QR linking to digital permit]                    │
│  Generated: {Timestamp}                                     │
└─────────────────────────────────────────────────────────────┘
```

---

## Document History

| Version | Date | Author | Changes |
|---------|------|--------|---------|
| 1.0 | January 14, 2026 | Product Team | Initial PRD |

---

*This PRD was created through systematic brainstorming sessions analyzing market research, technical requirements, and organizational workflows.*
