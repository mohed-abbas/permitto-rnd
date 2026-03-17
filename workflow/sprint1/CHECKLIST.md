# Sprint 1 Checklist — Working Prototype (Weeks 1-3)

**Sprint Goal:** Live staging URL with full core PTW workflow operational.
**Payment Milestone:** $1,600

---

## Feature Status

| # | Feature | Status | Date | Notes |
|---|---------|--------|------|-------|
| 1.1 | Project Infrastructure (Monorepo, Docker, CI/CD) | Done | 2026-01-21 | Phase 0 complete |
| 1.2 | Database Design & Setup (Prisma schema, migrations, seed) | Done | 2026-01-21 | Full schema designed |
| 1.3 | Authentication & Sessions (JWT, refresh, password reset) | Done | 2026-01-21 | Redis-backed sessions |
| 1.4 | RBAC — 5 Roles | Partially Done | 2026-01-21 | 4 roles done. HSSE Officer role missing |
| 1.5 | User Management (CRUD, deactivation, profiles) | Partially Done | 2026-01-22 | CRUD done. Profile page & notification prefs missing |
| 1.6 | Project & Zone Management | Done | 2026-01-22 | Projects, zones, user assignment |
| 1.7 | One-Time Profile Signature | Partially Done | — | Signature component exists. Profile setup flow & reuse on approvals needs work |
| 1.8 | Permit Template Builder (14 field types, versioning, preview) | Done | 2026-01-22 | Enhanced in Phase 8 with checkbox_grid, yes_no_na, bg color |
| 1.9 | Permit Request & Submission (multi-step form, drafts, uploads) | Partially Done | 2026-01-24 | Works. Auto-save & permit ID format missing |
| 1.10 | 4-Level Approval Workflow (WPR→WPI→PC→HSSE) | Partially Done | 2026-01-23 | 3 levels work. HSSE 4th level & additional measures flow missing |
| 1.11 | Daily Re-Approval Cycle | Partially Done | 2026-01-23 | Core works. Bulk UI & reactivation flow missing |
| 1.12 | Permit Close-Out & Cancellation | Done | 2026-01-23 | Close-out and cancellation paths |
| 1.13 | PTW Register (search, filter, export) | Partially Done | 2026-01-24 | Search/filter works. Export missing |
| 1.14 | Basic SIMOPs Awareness (zone overlap detection) | Not Started | — | Zone overlap detection not built |
| 1.15 | Background Automation (cron jobs) | Done | 2026-01-23 | Daily reminders, suspension, expiry |
| 1.16 | Email Notifications | Partially Done | 2026-01-23 | Templates & queue built. Workflow event integration missing |
| 1.17 | In-App Notifications | Partially Done | 2026-01-24 | Bell, dropdown, mark read. Click-to-navigate & real-time missing |
| 1.18 | On-Demand PDF Generation | Done | 2026-01-23 | Two-page layout, redesigned in Phase 8 |
| 1.19 | File Storage | Partially Done | 2026-01-22 | Uses R2. Proposal requires VPS filesystem |
| 1.20 | Audit Trail | Partially Done | 2026-01-23 | Logging works. Timeline view on permit detail missing |
| 1.21 | Responsive Frontend | Done | 2026-01-24 | Mobile-first, role-based dashboards |
| 1.22 | Staging Deployment | Not Started | — | No staging environment deployed |

---

## Summary

| Status | Count |
|--------|-------|
| Done | 9 |
| Partially Done | 11 |
| Not Started | 2 |
| **Total Features** | **22** |

**Estimated Completion:** ~65%

---

## Remaining Work (Priority Order)

1. **HSSE Officer Role** — Add 5th role to RBAC, permissions, schema, frontend
2. **4th Approval Level (HSSE)** — Add HSSE approval step to state machine and workflow
3. **Additional Measures Flow** — Request measures + resubmit loop (not just reject)
4. **Basic SIMOPs Awareness** — Zone overlap detection with warnings
5. **Auto-Save** — Save permit form on field blur / interval
6. **Permit ID Format** — `{PROJECT_CODE}-{TYPE}-{YEAR}-{SEQUENCE}`
7. **Email Integration** — Connect emails to workflow events
8. **Profile Signature Setup** — One-time draw + reuse on all approvals
9. **PTW Register Export** — CSV/Excel export
10. **Bulk Daily Approval UI** — Frontend bulk approve interface
11. **Audit Trail Timeline View** — Visual timeline on permit detail
12. **File Storage Migration** — R2 → VPS filesystem (or document for production)
13. **Click-to-Navigate Notifications** — Link notifications to permits
14. **Staging Deployment** — Deploy working prototype to staging URL
