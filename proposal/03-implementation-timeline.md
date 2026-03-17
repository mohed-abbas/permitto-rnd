# Section 3: Implementation Timeline

---

## Methodology

| Aspect | Detail |
|--------|--------|
| Framework | Agile Scrum |
| Sprint 1 | 3 weeks (working prototype) |
| Subsequent Sprints | 2 weeks each |
| Sprint Review/Demo | End of each sprint — live demo on staging URL |
| Staging | Hosted on our infrastructure throughout development. Client receives a live staging URL from Sprint 1 |
| Production | Deployed to dedicated VPS only after UAT sign-off |

---

## Sprint 1 — Working Prototype (Weeks 1-3)

| Deliverable | Details |
|-------------|---------|
| Project Infrastructure | Monorepo setup, Docker containerization, CI/CD pipeline, staging environment on our VPS |
| Database Design & Setup | Full relational schema — users, projects, zones, templates, permits, daily approvals, audit logs, notifications |
| Authentication & Sessions | Email/password login, JWT sessions with refresh tokens, password reset, session management |
| RBAC (5 Roles) | Super Admin, PTW-PC (Coordinator), WPI (Issuer), HSSE Officer, WPR (Receiver) — permissions enforced at API and UI level |
| User Management | Create, view, edit, deactivate/reactivate users with role assignment |
| Project & Zone Management | Create projects with unique codes, divide into zones, assign users to projects |
| One-Time Profile Signature | Users draw and save signature once during profile setup, stored securely in database |
| Permit Template Builder | Form builder with 14 field types, multi-step sections, risk assessment checklists, versioning, preview, background color |
| Permit Request & Submission | Multi-step form filling with auto-save, file upload, draft management, risk assessment verification |
| 4-Level Approval Workflow | Full sequential approval: WPR → WPI → PTW-PC → HSSE. One-click approve with stored signature, request additional measures, rejection with feedback |
| Daily Re-Approval Cycle | Daily re-confirmation with deadline enforcement, automatic suspension, bulk approval |
| Permit Close-Out & Cancellation | Formal close-out by PTW-PC, separate cancellation path |
| PTW Register | Centralized permit registry — searchable, filterable, with status tracking and auto-updates |
| Basic SIMOPs Awareness | Zone overlap detection with warnings displayed on permit detail |
| Background Automation | Scheduled jobs — daily reminders, suspension checks, expiry warnings, end-of-day processing |
| Email Notifications | Transactional emails for all core workflow events |
| In-App Notifications | Notification center with bell icon, unread counts, click-to-navigate |
| On-Demand PDF Generation | Print-ready permit PDFs with form data, signatures, QR code, company branding |
| File Storage | Supporting document upload stored securely on server |
| Audit Trail | Complete timestamped log of all permit actions |
| Responsive Frontend | Mobile-first web application with role-based dashboards, navigation, and full workflow UI |
| Staging Deployment | Live staging URL delivered to client |

**Sprint 1 Review: WORKING PROTOTYPE** — Client receives staging URL with full core PTW workflow operational.

---

## Sprint 2 — SIMOPs, Condition Change & Extensions (Weeks 4-5)

| Deliverable | Details |
|-------------|---------|
| SIMOPs Full Implementation | Countersign flow (other SIMOP's WPI must approve), withhold mechanism, blocking gate in approval workflow |
| SIMOPs Checklist | Digital checklist based on client-provided format for documenting interface management |
| SIMOPs UI | Conflict alerts, countersign screens, cross-permit visibility per zone |
| Condition Change Workflow | Flag changed conditions, notify WPI, re-assessment with options: continue, re-permit, or cancel |
| Permit Extension | Extension request by WPR, WPI review and grant/deny, sign extension, PTW Register auto-update |
| Condition Change & Extension UI | Request forms, approval screens, status indicators |

**Sprint 2 Review:** Full SIMOPs demo with countersign, condition change and extension workflows end-to-end.

---

## Sprint 3 — PDF Templates & Notifications (Weeks 6-7)

| Deliverable | Details |
|-------------|---------|
| 7 Permit PDF Formats | PDF layouts matching all client-provided physical permit formats (General PTW, Hot Work, Excavation, Work at Height, Lifting, Night Work, Road Closure) |
| Updated Email Notifications | New notifications for HSSE approval, SIMOPs countersign, additional measures request, condition change, extension decisions |
| Additional Measures Notification Flow | Specific measures communicated to WPR with actionable notifications |
| PDF Validation | Side-by-side comparison with physical permit formats for 1:1 parity |

**Sprint 3 Review:** PDF comparison against physical permits, complete notification flow demo.

---

## Sprint 4 — PWA & Offline Mode (Weeks 8-9)

| Deliverable | Details |
|-------------|---------|
| PWA Setup | Service worker, app manifest — installable on mobile/desktop home screen |
| Offline Permit Viewing | Active permits cached and viewable without connectivity |
| Offline PDF Caching | PDFs pre-cached for offline viewing/printing |
| Offline Form Filling | Fill permit forms offline, data stored in encrypted local storage |
| Auto-Sync | Automatic sync of queued actions on connectivity restore |
| Sync Status Indicator | Visual online/offline indicator with pending action count |
| Encryption at Rest | All cached data encrypted using Web Crypto API |
| Session-Bound Cache | Encryption key destroyed on logout/session expiry |
| Wipe on Logout | All local data cleared on logout |
| Session Timeout | Auto-lock after idle period, re-authentication required even offline |
| Data Minimization | Only user's own active permits cached, auto-purge of expired data |
| Auto-Expiry | Cached data expires after 24-48 hours |
| Offline Action Audit | All offline actions logged with timestamps, validated on sync |

**Sprint 4 Review:** PWA install demo, offline form filling, security measures walkthrough.

---

## Sprint 5 — Mobile Optimization & Testing (Weeks 10-11)

| Deliverable | Details |
|-------------|---------|
| Mobile Responsiveness | All pages audited and optimized for mobile and tablet use |
| Touch Target Optimization | Buttons and interactions sized for field use on mobile |
| Cross-Browser Testing | Chrome, Safari, Edge, Firefox verification |
| End-to-End Workflow Testing | All permit types, all approval paths, all edge cases |
| SIMOPs Edge Cases | Multiple overlapping permits, zone conflict scenarios |
| Offline Sync Testing | Conflict resolution, queue integrity verification |
| Performance Optimization | Page load times, API response times |
| Bug Fixes | All issues from previous sprint reviews |

**Sprint 5 Review:** Mobile device demo, test results report, remaining issues list.

---

## Sprint 6 — UAT & Production Deployment (Weeks 12-13)

| Deliverable | Details |
|-------------|---------|
| Client UAT | Client team tests all workflows on staging environment |
| UAT Feedback Fixes | Issues reported during UAT resolved |
| Final Adjustments | UI tweaks, wording changes, workflow adjustments per client feedback |
| VPS Setup | Docker, PostgreSQL, Redis, domain configuration on dedicated VPS |
| Production Deployment | Application deployed to dedicated VPS |
| Initial Data Setup | Users, projects, zones, templates configured for production |
| Production Smoke Test | Full workflow verification on production environment |
| Handover | Admin credentials, documentation, system access |

**Sprint 6 Review: PROJECT DELIVERED** — Production live on dedicated VPS.

---

## Timeline Summary

| Sprint | Duration | Weeks | Deliverable |
|--------|----------|-------|-------------|
| Sprint 1: Working Prototype | 3 weeks | 1-3 | Live staging URL — full core PTW workflow |
| Sprint 2: SIMOPs + Extensions | 2 weeks | 4-5 | SIMOPs, condition change, permit extension |
| Sprint 3: PDF Templates + Notifications | 2 weeks | 6-7 | 7 PDF formats, complete notification system |
| Sprint 4: PWA & Offline Mode | 2 weeks | 8-9 | Installable PWA with secure offline mode |
| Sprint 5: Mobile + Testing | 2 weeks | 10-11 | Mobile optimization, full testing |
| Sprint 6: UAT + Production | 2 weeks | 12-13 | Client UAT, production deployment |
| **Total** | **13 weeks** | | |

## Key Milestones

| Milestone | When | Deliverable |
|-----------|------|-------------|
| Working Prototype | End of Week 3 | Live staging URL — core PTW workflow demo |
| SIMOPs Complete | End of Week 5 | Full SIMOPs with countersign and checklist |
| All Features Complete | End of Week 9 | PWA, offline, all workflows operational |
| UAT Start | Week 12 | Client begins formal acceptance testing |
| Go-Live | End of Week 13 | Production on dedicated VPS |

## Hosting Approach

| Phase | Environment | Hosted On |
|-------|-------------|-----------|
| Sprints 1-5 | Staging | Our VPS — client receives staging URL for sprint reviews |
| Sprint 6 | Production | Dedicated VPS — final deployment after UAT sign-off |
