# Permitto — Sprint Workflow Overview

**Total Duration:** 13 weeks (6 sprints)
**Total Development Cost:** $12,000
**Methodology:** Agile Scrum — Sprint 1 is 3 weeks, subsequent sprints are 2 weeks each.

---

## Sprint Summary

| Sprint | Duration | Weeks | Payment | Status | Completion |
|--------|----------|-------|---------|--------|------------|
| Sprint 1: Working Prototype | 3 weeks | 1-3 | $1,600 | In Progress | ~65% |
| Sprint 2: SIMOPs + Extensions | 2 weeks | 4-5 | $1,400 | Not Started | 0% |
| Sprint 3: PDF Templates + Notifications | 2 weeks | 6-7 | $1,200 | Not Started | 0% |
| Sprint 4: PWA & Offline Mode | 2 weeks | 8-9 | $1,400 | Not Started | 0% |
| Sprint 5: Mobile + Testing | 2 weeks | 10-11 | $1,200 | Not Started | 0% |
| Sprint 6: UAT + Production | 2 weeks | 12-13 | $1,200 | Not Started | 0% |

**Kick-Off Payment:** $4,000 (before development begins)
**Total:** $12,000 development + $6,000 support (6-month minimum @ $1,000/mo) = **$18,000**

---

## Feature Count by Sprint

| Sprint | Total Sub-Tasks | Done | In Progress | Not Started | Partially Done |
|--------|----------------|------|-------------|-------------|----------------|
| Sprint 1 | 22 features | 9 | 0 | 2 | 11 |
| Sprint 2 | 32 sub-tasks | 0 | 0 | 32 | 0 |
| Sprint 3 | 28 sub-tasks | 0 | 0 | 28 | 0 |
| Sprint 4 | 47 sub-tasks | 0 | 0 | 47 | 0 |
| Sprint 5 | 49 sub-tasks | 0 | 0 | 49 | 0 |
| Sprint 6 | 58 sub-tasks | 0 | 0 | 58 | 0 |
| **Total** | **236** | **9** | **0** | **216** | **11** |

---

## Key Milestones

| Milestone | Target Week | Deliverable |
|-----------|-------------|-------------|
| Working Prototype | Week 3 | Live staging URL — core PTW workflow |
| SIMOPs Complete | Week 5 | Full SIMOPs with countersign and checklist |
| All Features Complete | Week 9 | PWA, offline, all workflows |
| UAT Start | Week 12 | Client begins formal acceptance testing |
| Go-Live | Week 13 | Production on client's VPS |

---

## Critical Path

The following items are on the critical path and must be completed in order:

1. **Sprint 1 gaps** — HSSE role, 4th approval level, SIMOPs awareness, email integration, staging deployment
2. **Sprint 2** — SIMOPs full implementation (depends on #1), condition change, extensions
3. **Sprint 3** — 7 PDF formats (depends on #2 for SIMOPs/extension data), notification updates
4. **Sprint 4** — PWA/offline (depends on #3 for all features being online-functional)
5. **Sprint 5** — Testing (depends on #4 for all features including offline)
6. **Sprint 6** — UAT + production (depends on #5 for tested, polished product)

---

## Architecture Gaps (Current vs Proposal)

| Area | Current State | Proposal Requirement | Sprint |
|------|--------------|---------------------|--------|
| User Roles | 4 roles (no HSSE) | 5 roles (Super Admin, PTW-PC, WPI, HSSE, WPR) | Sprint 1 |
| Approval Levels | 3 levels (WPI, PC) | 4 levels (WPI, PC, HSSE) | Sprint 1 |
| File Storage | Cloudflare R2 | VPS filesystem | Sprint 1 |
| SIMOPs | Not implemented | Full countersign + checklist | Sprint 2 |
| Condition Change | Not implemented | Flag → re-assess → continue/re-permit/cancel | Sprint 2 |
| Permit Extension | Not implemented | Request → review → grant/deny | Sprint 2 |
| PDF Formats | 1 generic template | 7 type-specific templates | Sprint 3 |
| PWA/Offline | Not implemented | Full PWA with encrypted offline | Sprint 4 |
| In-App Training | Not implemented | Role-based guides, videos, FAQ | Sprint 6 |

---

## Directory Structure

```
workflow/
├── OVERVIEW.md          ← This file
├── sprint1/
│   ├── WORKFLOW.md      ← Detailed feature breakdown with sub-tasks
│   └── CHECKLIST.md     ← Status tracking (Done/In Progress/Not Started)
├── sprint2/
│   ├── WORKFLOW.md
│   └── CHECKLIST.md
├── sprint3/
│   ├── WORKFLOW.md
│   └── CHECKLIST.md
├── sprint4/
│   ├── WORKFLOW.md
│   └── CHECKLIST.md
├── sprint5/
│   ├── WORKFLOW.md
│   └── CHECKLIST.md
└── sprint6/
    ├── WORKFLOW.md
    └── CHECKLIST.md
```

---

## How to Use

1. **Before starting a sprint:** Read the sprint's `WORKFLOW.md` for detailed requirements
2. **During development:** Update the sprint's `CHECKLIST.md` as tasks are completed
3. **Sprint review:** Demo deliverables to client, collect feedback
4. **After sprint:** Update this `OVERVIEW.md` with sprint completion status
