# Sprint 4 Checklist — PWA & Offline Mode (Weeks 8-9)

**Sprint Goal:** Installable PWA with secure offline mode — viewing, form filling, and auto-sync.
**Payment Milestone:** $1,400

---

## Feature Status

| # | Feature | Status | Date | Notes |
|---|---------|--------|------|-------|
| **4.1** | **PWA Setup** | | | |
| 4.1.1 | Service worker registration (Workbox/next-pwa) | Not Started | — | |
| 4.1.2 | App manifest (manifest.json) | Not Started | — | |
| 4.1.3 | App icons (192x192, 512x512, maskable) | Not Started | — | |
| 4.1.4 | Install prompt (Add to Home Screen) | Not Started | — | |
| 4.1.5 | Splash screen with branding | Not Started | — | |
| 4.1.6 | Cache strategies (Cache First, Network First, SWR) | Not Started | — | |
| **4.2** | **Offline Permit Viewing** | | | |
| 4.2.1 | Smart caching (user's own active permits only) | Not Started | — | |
| 4.2.2 | Permit data cache in IndexedDB | Not Started | — | |
| 4.2.3 | Permit list cache | Not Started | — | |
| 4.2.4 | Cache refresh on data changes | Not Started | — | |
| 4.2.5 | Offline indicator on cached permits | Not Started | — | |
| **4.3** | **Offline PDF Caching** | | | |
| 4.3.1 | PDF pre-caching for active permits | Not Started | — | |
| 4.3.2 | Cache API storage for PDFs | Not Started | — | |
| 4.3.3 | Offline PDF viewer | Not Started | — | |
| 4.3.4 | Cache invalidation on status change | Not Started | — | |
| **4.4** | **Offline Form Filling** | | | |
| 4.4.1 | Offline form state in IndexedDB | Not Started | — | |
| 4.4.2 | Template caching for offline form rendering | Not Started | — | |
| 4.4.3 | Client-side Zod validation | Not Started | — | |
| 4.4.4 | Draft persistence and sync | Not Started | — | |
| 4.4.5 | File attachment queue | Not Started | — | |
| **4.5** | **Auto-Sync** | | | |
| 4.5.1 | Action queue in IndexedDB | Not Started | — | |
| 4.5.2 | Sync manager (detect connectivity, process queue) | Not Started | — | |
| 4.5.3 | Conflict resolution | Not Started | — | |
| 4.5.4 | Retry logic with exponential backoff | Not Started | — | |
| 4.5.5 | Server validation of synced actions | Not Started | — | |
| **4.6** | **Sync Status Indicator** | | | |
| 4.6.1 | Online/offline visual indicator | Not Started | — | |
| 4.6.2 | Pending action count | Not Started | — | |
| 4.6.3 | Sync progress display | Not Started | — | |
| 4.6.4 | Sync error display | Not Started | — | |
| 4.6.5 | Manual sync trigger button | Not Started | — | |
| **4.7** | **Encryption at Rest** | | | |
| 4.7.1 | Web Crypto API integration | Not Started | — | |
| 4.7.2 | IndexedDB encryption layer | Not Started | — | |
| 4.7.3 | Key derivation from session (PBKDF2/HKDF) | Not Started | — | |
| **4.8** | **Session-Bound Cache** | | | |
| 4.8.1 | Encryption key tied to session | Not Started | — | |
| 4.8.2 | Session expiry detection (online + offline) | Not Started | — | |
| 4.8.3 | Cache invalidation on session end | Not Started | — | |
| **4.9** | **Wipe on Logout** | | | |
| 4.9.1 | Complete data wipe (IndexedDB + Cache API + SW cache) | Not Started | — | |
| 4.9.2 | Wipe verification check | Not Started | — | |
| 4.9.3 | Graceful sync before wipe (if online) | Not Started | — | |
| **4.10** | **Session Timeout** | | | |
| 4.10.1 | Idle detection (configurable 15-30 min) | Not Started | — | |
| 4.10.2 | Auto-lock (online + offline) | Not Started | — | |
| 4.10.3 | Re-authentication required to unlock | Not Started | — | |
| 4.10.4 | Lock screen with branding | Not Started | — | |
| **4.11** | **Data Minimization** | | | |
| 4.11.1 | Selective caching (own active permits only) | Not Started | — | |
| 4.11.2 | Auto-purge closed/expired permit data | Not Started | — | |
| **4.12** | **Auto-Expiry** | | | |
| 4.12.1 | Cache TTL (24-48 hours configurable) | Not Started | — | |
| 4.12.2 | Periodic expiry check | Not Started | — | |
| **4.13** | **Offline Action Audit** | | | |
| 4.13.1 | Offline timestamp logging | Not Started | — | |
| 4.13.2 | Sync audit trail on server | Not Started | — | |
| 4.13.3 | Offline action marker in audit trail | Not Started | — | |
| 4.13.4 | Timestamp validation | Not Started | — | |

---

## Summary

| Status | Count |
|--------|-------|
| Done | 0 |
| In Progress | 0 |
| Not Started | 47 |
| **Total Sub-Tasks** | **47** |

**Estimated Completion:** 0%

---

## Dependencies on Previous Sprints

- [ ] Sprint 1: Full permit lifecycle working
- [ ] Sprint 2: SIMOPs, condition change, extensions working
- [ ] Sprint 3: All 7 PDF formats generated correctly
- [ ] Sprint 3: Complete notification system operational
- [ ] HTTPS configured on staging for service worker testing
