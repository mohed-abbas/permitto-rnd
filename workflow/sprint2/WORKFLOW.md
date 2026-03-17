# Sprint 2 — SIMOPs, Condition Change & Extensions (Weeks 4-5)

**Goal:** Full SIMOPs implementation with countersign and checklist, condition change workflow, and permit extension flow — all operational end-to-end.

**Payment Milestone:** $1,400 on delivery of SIMOPs, condition change, and extension workflows.

**Prerequisites:** Sprint 1 must be complete (all 5 roles, 4-level approval, basic SIMOPs awareness).

---

## 2.1 SIMOPs Full Implementation

**Ref:** Proposal Section 2.8

SIMOPs (Simultaneous Operations) management is a critical safety feature. When multiple permits are active or pending in the same zone during overlapping time periods, the system must enforce an interface management gate.

### Sub-Tasks

#### Backend

- [ ] **SIMOPs detection service** — query active/pending permits in the same zone with overlapping time periods
- [ ] **SIMOPs gate in approval workflow** — blocking step after WPR submission. If SIMOPs detected, permit cannot proceed until interfaces are managed
- [ ] **Countersign requirement** — other SIMOP's WPI must review and countersign the permit, confirming interface management
- [ ] **Withhold mechanism** — permits withheld if interface management issues are unresolved. WPI notified to resolve before proceeding
- [ ] **SIMOPs status tracking** — track SIMOPs state per permit (detected, pending_countersign, resolved, withheld)
- [ ] **SIMOPs API endpoints:**
  - `GET /permits/:id/simops` — get SIMOPs conflicts for a permit
  - `POST /permits/:id/simops/countersign` — WPI countersigns
  - `POST /permits/:id/simops/withhold` — WPI withholds permit
  - `POST /permits/:id/simops/resolve` — resolve SIMOPs issue

#### Frontend

- [ ] **SIMOPs conflict alerts** — visual warning when SIMOPs detected during permit submission
- [ ] **Countersign screens** — UI for other SIMOP's WPI to review and countersign
- [ ] **Cross-permit visibility** — all parties can see which other permits are active in the same zone
- [ ] **SIMOPs status indicators** — show SIMOPs state on permit detail and list pages
- [ ] **Withhold notification UI** — show withheld status with reason and resolution path

### State Machine Update

```
SUBMITTED → [SIMOPs Check] → SIMOPS_PENDING (if conflicts) → SIMOPS_RESOLVED → WPI_APPROVED
                            → WPI_APPROVED (if no conflicts)

SIMOPS_PENDING → WITHHELD (if issues unresolved)
WITHHELD → SIMOPS_RESOLVED (when WPI resolves)
```

### Acceptance Criteria

- System automatically detects overlapping permits in the same zone
- Permit blocked from proceeding until SIMOPs interfaces are managed
- Other SIMOP's WPI can countersign
- Withhold mechanism prevents unsafe concurrent operations
- All SIMOPs actions logged in audit trail

---

## 2.2 SIMOPs Checklist

**Ref:** Proposal Section 2.8 (SIMOPs Checklist)

### Sub-Tasks

- [ ] **Digital SIMOPs checklist model** — Prisma schema for SIMOPs checklist records
- [ ] **Checklist template** — based on client-provided format for documenting interface management
- [ ] **Checklist API:**
  - `POST /permits/:id/simops/checklist` — create/update checklist
  - `GET /permits/:id/simops/checklist` — get checklist for a permit
- [ ] **Checklist UI** — form for documenting how interfaces between concurrent operations are managed
- [ ] **Checklist validation** — ensure all required fields completed before SIMOPs resolution
- [ ] **Checklist in PDF** — include SIMOPs checklist data in permit PDF

### Acceptance Criteria

- WPI can fill out SIMOPs checklist documenting interface management
- Checklist must be completed before SIMOPs can be resolved
- Checklist data included in permit PDF
- Based on client-provided checklist format

---

## 2.3 Condition Change Workflow

**Ref:** Proposal Section 2.10

When site conditions change during active work, the system must support a re-assessment process.

### Sub-Tasks

#### Backend

- [ ] **Condition change model** — Prisma schema for condition change records (flaggedBy, description, assessment, outcome)
- [ ] **Flag condition change endpoint** — `POST /permits/:id/condition-change` (WPR or WPI can flag)
- [ ] **Automatic WPI notification** — notify WPI when conditions are flagged
- [ ] **Re-assessment endpoint** — `POST /permits/:id/condition-change/:changeId/assess`
- [ ] **Three outcomes:**
  - **Continue** — conditions manageable, work continues under existing permit
  - **Re-permit** — original permit cancelled, new permit initiated
  - **Cancel** — conditions unsafe, permit cancelled immediately
- [ ] **State machine update:**
  ```
  ACTIVE → CONDITION_CHANGED → RE_ASSESSMENT → CONTINUE (back to ACTIVE)
                                              → RE_PERMIT (cancel + new draft)
                                              → CANCEL (to CANCELLED)
  ```
- [ ] **Audit logging** for all condition change actions

#### Frontend

- [ ] **Flag condition change button** on active permit detail (for WPR and WPI)
- [ ] **Condition change form** — description of changed conditions with optional file upload
- [ ] **Re-assessment UI** for WPI — review conditions and select outcome
- [ ] **Re-permit flow** — auto-create new permit draft from cancelled permit data
- [ ] **Condition change history** — show all condition changes on permit detail

### Acceptance Criteria

- WPR or WPI can flag changed conditions on active permits
- WPI receives notification and can perform re-assessment
- Three clear outcomes: continue, re-permit, or cancel
- All actions logged in audit trail
- Re-permit creates new draft pre-filled from original permit

---

## 2.4 Permit Extension

**Ref:** Proposal Section 2.11

### Sub-Tasks

#### Backend

- [ ] **Extension request model** — Prisma schema (requestedBy, justification, requestedEndDate, status, reviewedBy)
- [ ] **Extension request endpoint** — `POST /permits/:id/extension` (WPR requests before expiry)
- [ ] **Extension review endpoint** — `POST /permits/:id/extension/:extId/review` (WPI grants or denies)
- [ ] **Extension grant flow:**
  - WPI approves with stored signature
  - Permit validity dates updated
  - PTW Register auto-updated with extension details
- [ ] **Extension deny flow:**
  - Denied extensions proceed to permit close-out
  - Notification to WPR with reason
- [ ] **Extension deadline alerts** — background job to notify when extension deadlines approach
- [ ] **Audit logging** for extension requests, grants, and denials

#### Frontend

- [ ] **Extension request button** on active permit detail (for WPR, before expiry)
- [ ] **Extension request form** — justification text, requested new end date
- [ ] **Extension review UI** for WPI — review request, grant or deny with comments
- [ ] **Extension status indicator** on permit detail and list
- [ ] **Extension history** — show all extensions on permit detail

### State Machine Update

```
ACTIVE → EXPIRED → EXTENSION_REQUESTED → GRANTED (back to ACTIVE with new dates)
                                        → DENIED → PENDING_CLOSEOUT
```

### Acceptance Criteria

- WPR can request extension before permit expiry with justification
- WPI can grant or deny extension
- Granted extensions update permit validity and PTW Register
- Denied extensions proceed to close-out
- Extension sign-off includes WPI's stored signature

---

## Sprint 2 Dependencies

| Feature | Depends On |
|---------|-----------|
| SIMOPs Full Implementation | Sprint 1: Zone Management, Permit CRUD, 4-Level Approval |
| SIMOPs Checklist | SIMOPs Full Implementation |
| Condition Change Workflow | Sprint 1: Permit Workflow, Notifications |
| Permit Extension | Sprint 1: Permit Workflow, Background Jobs |

## Sprint 2 Technical Notes

### Database Migrations Needed

1. Add SIMOPs-related fields/tables (SIMOPsConflict, SIMOPsChecklist, SIMOPsCountersign)
2. Add ConditionChange table (permitId, flaggedBy, description, assessment, outcome, timestamps)
3. Add PermitExtension table (permitId, requestedBy, justification, requestedEndDate, status, reviewedBy, timestamps)
4. Update Permit status enum with new states (SIMOPS_PENDING, WITHHELD, CONDITION_CHANGED, EXTENSION_REQUESTED)

### API Route Structure

```
/api/permits/:id/simops/*          — SIMOPs management
/api/permits/:id/condition-change/* — Condition change workflow
/api/permits/:id/extension/*       — Permit extension
```
