# Sprint 2 Checklist — SIMOPs, Condition Change & Extensions (Weeks 4-5)

**Sprint Goal:** Full SIMOPs with countersign, condition change and extension workflows end-to-end.
**Payment Milestone:** $1,400

---

## Feature Status

| # | Feature | Status | Date | Notes |
|---|---------|--------|------|-------|
| **2.1** | **SIMOPs Full Implementation** | | | |
| 2.1.1 | SIMOPs detection service (zone overlap query) | Not Started | — | |
| 2.1.2 | SIMOPs gate in approval workflow (blocking step) | Not Started | — | |
| 2.1.3 | Countersign requirement (other WPI must approve) | Not Started | — | |
| 2.1.4 | Withhold mechanism (block if unresolved) | Not Started | — | |
| 2.1.5 | SIMOPs status tracking | Not Started | — | |
| 2.1.6 | SIMOPs API endpoints | Not Started | — | |
| 2.1.7 | SIMOPs conflict alerts UI | Not Started | — | |
| 2.1.8 | Countersign screens UI | Not Started | — | |
| 2.1.9 | Cross-permit visibility per zone | Not Started | — | |
| 2.1.10 | SIMOPs status indicators UI | Not Started | — | |
| 2.1.11 | Withhold notification UI | Not Started | — | |
| **2.2** | **SIMOPs Checklist** | | | |
| 2.2.1 | SIMOPs checklist data model | Not Started | — | |
| 2.2.2 | Checklist template (client format) | Not Started | — | Needs client-provided format |
| 2.2.3 | Checklist API (create, get) | Not Started | — | |
| 2.2.4 | Checklist form UI | Not Started | — | |
| 2.2.5 | Checklist validation (required before resolution) | Not Started | — | |
| 2.2.6 | Checklist data in permit PDF | Not Started | — | |
| **2.3** | **Condition Change Workflow** | | | |
| 2.3.1 | Condition change data model | Not Started | — | |
| 2.3.2 | Flag condition change endpoint | Not Started | — | |
| 2.3.3 | Automatic WPI notification on flag | Not Started | — | |
| 2.3.4 | Re-assessment endpoint (continue/re-permit/cancel) | Not Started | — | |
| 2.3.5 | State machine update for condition changes | Not Started | — | |
| 2.3.6 | Audit logging for condition changes | Not Started | — | |
| 2.3.7 | Flag condition change button UI | Not Started | — | |
| 2.3.8 | Condition change form UI | Not Started | — | |
| 2.3.9 | Re-assessment UI for WPI | Not Started | — | |
| 2.3.10 | Re-permit flow (auto-create new draft) | Not Started | — | |
| 2.3.11 | Condition change history on permit detail | Not Started | — | |
| **2.4** | **Permit Extension** | | | |
| 2.4.1 | Extension request data model | Not Started | — | |
| 2.4.2 | Extension request endpoint (WPR) | Not Started | — | |
| 2.4.3 | Extension review endpoint (WPI grant/deny) | Not Started | — | |
| 2.4.4 | Extension grant flow (update dates, register) | Not Started | — | |
| 2.4.5 | Extension deny flow (proceed to close-out) | Not Started | — | |
| 2.4.6 | Extension deadline alerts (background job) | Not Started | — | |
| 2.4.7 | Audit logging for extensions | Not Started | — | |
| 2.4.8 | Extension request button & form UI | Not Started | — | |
| 2.4.9 | Extension review UI for WPI | Not Started | — | |
| 2.4.10 | Extension status indicator & history UI | Not Started | — | |

---

## Summary

| Status | Count |
|--------|-------|
| Done | 0 |
| In Progress | 0 |
| Not Started | 32 |
| **Total Sub-Tasks** | **32** |

**Estimated Completion:** 0%

---

## Dependencies on Sprint 1

- [ ] HSSE Officer role must be implemented
- [ ] 4-Level approval workflow must be working
- [ ] Basic SIMOPs awareness (zone overlap detection) should be in place
- [ ] Notification system must be integrated with workflow events
- [ ] Profile signature reuse must be working
