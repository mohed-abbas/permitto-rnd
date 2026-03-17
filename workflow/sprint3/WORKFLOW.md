# Sprint 3 — PDF Templates & Notifications (Weeks 6-7)

**Goal:** All 7 permit type PDF formats matching client-provided physical permits, complete notification system covering all workflow events including HSSE, SIMOPs, condition change, and extensions.

**Payment Milestone:** $1,200 on delivery of 7 PDF formats and complete notification system.

**Prerequisites:** Sprint 2 must be complete (SIMOPs, condition change, extensions all functional).

---

## 3.1 Seven Permit PDF Formats

**Ref:** Proposal Section 2.13

Each of the 7 permit types must have a PDF layout that matches the client-provided physical permit format. Phase 8 of the existing codebase created a generic two-page table layout — this sprint creates type-specific variations.

### The 7 Permit Types

1. **General PTW** — Standard permit to work
2. **Hot Work** — Welding, cutting, grinding, etc.
3. **Excavation** — Digging, trenching, etc.
4. **Work at Height** — Scaffolding, ladders, elevated platforms
5. **Lifting** — Crane operations, heavy lifting
6. **Night Work** — After-hours operations
7. **Road Closure** — Traffic management, road blocking

### Sub-Tasks

#### Backend

- [ ] **PDF template registry** — map each permit type to its specific PDF layout template
- [ ] **Type-specific HTML templates** — create 7 distinct PDF HTML templates matching physical formats:
  - [ ] General PTW PDF template
  - [ ] Hot Work PDF template
  - [ ] Excavation PDF template
  - [ ] Work at Height PDF template
  - [ ] Lifting PDF template
  - [ ] Night Work PDF template
  - [ ] Road Closure PDF template
- [ ] **Template variables** — each type may have different sections, field arrangements, and safety checklists
- [ ] **Two-page layout per type:**
  - Front page: permit details, form data, approval signatures with timestamps, QR code, company header/branding
  - Back page: additional precautions, re-validation table (daily approvals), validity note
- [ ] **SIMOPs data in PDF** — include SIMOPs checklist and countersign data if applicable
- [ ] **Condition change data in PDF** — include condition change history
- [ ] **Extension data in PDF** — include extension history and updated validity dates
- [ ] **Close-out data in PDF** — updated PDF reflecting close-out details on demand

#### Frontend

- [ ] **PDF preview per type** — show correct layout when previewing PDF for each permit type
- [ ] **Download/print buttons** — one-click PDF download or in-browser print

#### Validation

- [ ] **Side-by-side comparison** — compare generated PDFs with physical permit formats (requires client-provided physical permits)
- [ ] **1:1 visual parity check** for each of the 7 types

### Technical Notes

- Existing PDF service (`apps/api/src/services/pdf.service.ts`) uses Puppeteer with HTML templates
- Phase 8 redesigned the HTML template to a two-page table layout — this is the base
- Each type needs its own HTML template file or a parameterized template with type-specific sections
- Consider a template inheritance pattern: base layout + type-specific sections

### Acceptance Criteria

- All 7 permit types generate distinct, type-appropriate PDFs
- PDFs match client-provided physical permit formats (1:1 parity)
- All approval signatures with timestamps included
- QR code links to digital permit
- Company branding on all PDFs
- SIMOPs, condition change, and extension data included where applicable

---

## 3.2 Updated Email Notifications

**Ref:** Proposal Section 2.14

Sprint 1 built the email infrastructure and 9 base templates. This sprint adds notifications for all new workflow events from Sprint 2 and ensures complete coverage.

### New Email Notification Types

| Event | Recipients | Template |
|-------|-----------|----------|
| HSSE Approval | WPR, WPI, PTW-PC | HSSE has approved the permit |
| HSSE Rejection | WPR, WPI, PTW-PC | HSSE has rejected the permit, with feedback |
| HSSE Additional Measures Request | WPR | HSSE requests additional safety measures |
| SIMOPs Detected | WPI (both permits) | SIMOPs conflict detected, countersign needed |
| SIMOPs Countersign Request | Other SIMOP's WPI | Request to countersign for interface management |
| SIMOPs Resolved | All parties | SIMOPs interfaces resolved, permit proceeding |
| SIMOPs Withheld | All parties | Permit withheld due to unresolved SIMOPs issues |
| Condition Change Flagged | WPI | Site conditions changed, re-assessment needed |
| Condition Change — Continue | WPR | WPI assessed, work continues |
| Condition Change — Re-Permit | WPR | Original permit cancelled, new permit needed |
| Condition Change — Cancel | WPR, PTW-PC | Permit cancelled due to unsafe conditions |
| Extension Requested | WPI | WPR has requested a permit extension |
| Extension Granted | WPR, PTW-PC | Extension approved, new validity dates |
| Extension Denied | WPR | Extension denied, proceed to close-out |

### Sub-Tasks

- [ ] **Create 14 new email templates** (HTML + text versions)
- [ ] **Register email triggers** for each new workflow event
- [ ] **Recipient resolution** — determine correct recipients for each event based on permit assignments
- [ ] **Email queue integration** — all new events trigger queued email jobs
- [ ] **Test email delivery** for all new notification types

### Acceptance Criteria

- All workflow events from Sprints 1-2 trigger appropriate emails
- Emails include relevant details (permit number, action taken, next steps)
- Correct recipients receive each notification
- Email templates are professional and actionable

---

## 3.3 Additional Measures Notification Flow

**Ref:** Proposal Section 2.7

This is a specific notification sub-flow where an approver (WPI, PTW-PC, or HSSE) requests additional safety measures instead of a flat reject.

### Sub-Tasks

- [ ] **Additional measures email template** — clearly communicates what specific measures are needed
- [ ] **Actionable notification** — WPR receives notification with link to the permit and details of requested measures
- [ ] **In-app notification** with specific measures listed
- [ ] **Resubmit notification** — when WPR resubmits after implementing measures, original approver is notified
- [ ] **Measures tracking** — track which measures were requested, by whom, and whether implemented

### Acceptance Criteria

- Approver can specify what additional measures are needed (not just "rejected")
- WPR receives clear notification with specific measures required
- WPR can implement measures and resubmit
- Original requesting approver is notified of resubmission
- Full tracking of measures requested and implemented

---

## 3.4 PDF Validation

**Ref:** Proposal Section 3 (Sprint 3 Review)

### Sub-Tasks

- [ ] **Obtain client physical permit formats** — all 7 types
- [ ] **Side-by-side comparison** — screenshot generated PDF next to physical format
- [ ] **Document discrepancies** and fix
- [ ] **Client review** — send generated PDFs to client for validation
- [ ] **Iterate until 1:1 parity** achieved

### Acceptance Criteria

- All 7 PDF types reviewed against physical permits
- Client confirms visual parity
- All discrepancies documented and resolved

---

## Sprint 3 Technical Notes

### Files to Modify/Create

| File | Action |
|------|--------|
| `apps/api/src/services/pdf.service.ts` | Add type-specific template selection |
| `apps/api/src/templates/pdf/` | Create 7 type-specific HTML templates |
| `apps/api/src/services/email.service.ts` | Add 14 new email templates |
| `apps/api/src/services/notification.service.ts` | Register new event triggers |
| `packages/shared/src/constants/` | Add email event types |

### Testing Plan

- Generate PDF for each of the 7 permit types and verify layout
- Send test emails for all 14 new notification types
- Verify recipient resolution for each event
- Compare PDFs with physical permit formats
