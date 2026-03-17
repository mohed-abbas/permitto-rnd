# Sprint 4 — PWA & Offline Mode (Weeks 8-9)

**Goal:** Installable Progressive Web App with secure offline mode — field workers can view active permits, fill forms, and queue submissions while offline, with automatic sync on connectivity restore.

**Payment Milestone:** $1,400 on delivery of installable PWA with secure offline mode.

**Prerequisites:** Sprints 1-3 must be complete (all features operational, PDFs and notifications working).

---

## 4.1 PWA Setup

**Ref:** Proposal Section 2.18

### Sub-Tasks

- [ ] **Service worker registration** — Next.js service worker with Workbox
- [ ] **App manifest** (`manifest.json`) — app name, icons, theme color, display mode (standalone)
- [ ] **App icons** — multiple sizes for all platforms (192x192, 512x512, maskable)
- [ ] **Install prompt** — custom "Add to Home Screen" prompt for mobile/desktop
- [ ] **Splash screen** — branded loading screen on app launch
- [ ] **Navigation scope** — service worker handles all app routes
- [ ] **Cache strategy** — define caching strategies:
  - Static assets: Cache First
  - API responses: Network First with cache fallback
  - Pages: Stale While Revalidate

### Technical Notes

- Next.js supports PWA via `next-pwa` or custom service worker
- Service workers only work over HTTPS (and localhost for dev)
- Test installation on Chrome, Safari, Edge, and Firefox

### Acceptance Criteria

- App installable on mobile home screen and desktop
- Works like a native app when launched from home screen
- Custom splash screen with company branding
- Service worker registered and caching static assets

---

## 4.2 Offline Permit Viewing

**Ref:** Proposal Section 2.18 (Offline Permit Viewing)

### Sub-Tasks

- [ ] **Smart caching strategy** — only cache user's own active permits (not all system data)
- [ ] **Permit data cache** — cache permit detail data in IndexedDB when online
- [ ] **Permit list cache** — cache user's permit list for offline browsing
- [ ] **Cache refresh** — update cache when permit data changes (extension, daily approval, status change)
- [ ] **Offline indicator** — show offline badge on cached permits when offline
- [ ] **Stale data warning** — indicate when cached data may be outdated

### Acceptance Criteria

- User's active permits viewable without internet
- Only user's own permits are cached (data minimization)
- Cache refreshed on next online access after changes
- Clear indication of offline mode

---

## 4.3 Offline PDF Caching

**Ref:** Proposal Section 2.18 (Offline PDF Viewing)

### Sub-Tasks

- [ ] **PDF pre-caching** — when online, pre-generate and cache PDFs for user's active permits
- [ ] **Cache API storage** — store PDFs in Cache API (separate from IndexedDB)
- [ ] **Offline PDF viewer** — view/print cached PDFs without connectivity
- [ ] **Cache invalidation** — remove PDFs when permit status changes or permit closes
- [ ] **Storage management** — monitor cache size, purge old PDFs when limit approached

### Acceptance Criteria

- Active permit PDFs available offline for viewing/printing
- PDFs pre-cached when user is online
- Stale PDFs invalidated when permit data changes

---

## 4.4 Offline Form Filling

**Ref:** Proposal Section 2.18 (Offline Form Filling)

### Sub-Tasks

- [ ] **Offline form state** — store form data in IndexedDB during offline filling
- [ ] **Template caching** — cache permit templates so forms can be rendered offline
- [ ] **Field validation offline** — run Zod validators client-side without API
- [ ] **Draft persistence** — save drafts locally, sync to server on reconnect
- [ ] **File attachment queue** — queue file uploads for when connectivity returns
- [ ] **Offline form indicator** — show that form is being filled offline

### Acceptance Criteria

- Users can fill permit forms without internet
- Form data stored securely in encrypted local storage
- Drafts synced to server automatically on reconnect
- File uploads queued and sent on reconnect

---

## 4.5 Auto-Sync

**Ref:** Proposal Section 2.18 (Auto-Sync)

### Sub-Tasks

- [ ] **Action queue** — queue all offline actions (form saves, submissions, approvals) in IndexedDB
- [ ] **Sync manager** — background sync service that detects connectivity and processes queue
- [ ] **Conflict resolution** — handle cases where server state changed while offline:
  - Permit was closed/cancelled while user was offline
  - Daily approval deadline passed
  - Another user already approved
- [ ] **Retry logic** — retry failed sync actions with exponential backoff
- [ ] **Sync order** — process queued actions in chronological order
- [ ] **Server validation** — server validates all synced actions (offline actions logged with timestamps)

### Acceptance Criteria

- All offline actions automatically submitted when online
- Conflicts detected and presented to user for resolution
- Actions processed in correct chronological order
- Server validates every offline action

---

## 4.6 Sync Status Indicator

**Ref:** Proposal Section 2.18 (Sync Status Indicator)

### Sub-Tasks

- [ ] **Online/offline indicator** — persistent visual indicator in app header/footer
- [ ] **Pending action count** — show number of queued offline actions
- [ ] **Sync progress** — show progress during sync (X of Y actions synced)
- [ ] **Sync error display** — show which actions failed to sync and why
- [ ] **Manual sync trigger** — button to force sync attempt

### Acceptance Criteria

- Always visible online/offline status
- Pending action count displayed
- Progress shown during sync
- Failed actions clearly communicated

---

## 4.7 Encryption at Rest

**Ref:** Proposal Section 2.18 (Security — Encryption at Rest)

### Sub-Tasks

- [ ] **Web Crypto API integration** — generate encryption key from user's authenticated session
- [ ] **IndexedDB encryption** — encrypt all cached data before storing in IndexedDB
- [ ] **Decryption on read** — decrypt data transparently when reading from cache
- [ ] **Key derivation** — derive encryption key from session token (PBKDF2 or HKDF)

### Technical Notes

- Use `window.crypto.subtle` for AES-GCM encryption
- Key derived from user's JWT or session identifier
- All IndexedDB writes go through encryption layer

### Acceptance Criteria

- All cached data encrypted at rest
- Even direct device storage access cannot read data
- Encryption/decryption transparent to the application

---

## 4.8 Session-Bound Cache

**Ref:** Proposal Section 2.18 (Session-Bound Cache)

### Sub-Tasks

- [ ] **Encryption key tied to session** — key destroyed when session expires or user logs out
- [ ] **Session expiry detection** — detect expired sessions even while offline
- [ ] **Cache invalidation on session end** — cached data becomes permanently unreadable when key is destroyed

### Acceptance Criteria

- Cached data tied to authenticated session
- Expired session = unreadable cache
- No way to recover data after session destruction

---

## 4.9 Wipe on Logout

**Ref:** Proposal Section 2.18 (Wipe on Logout)

### Sub-Tasks

- [ ] **Complete data wipe** on logout:
  - Clear IndexedDB
  - Clear Cache API
  - Clear service worker cache
  - Destroy encryption key
- [ ] **Wipe confirmation** — ensure all local data is gone (verification check)
- [ ] **Graceful handling** — sync any pending actions before wiping (if online)

### Acceptance Criteria

- All local data completely wiped on logout
- No residual data in any browser storage
- Pending actions synced before wipe (if possible)

---

## 4.10 Session Timeout

**Ref:** Proposal Section 2.18 (Session Timeout)

### Sub-Tasks

- [ ] **Idle detection** — detect user inactivity (configurable: 15-30 minutes)
- [ ] **Auto-lock** — lock app after idle period, even while offline
- [ ] **Re-authentication** — require password/biometric to unlock
- [ ] **Lock screen** — show lock screen with company branding and re-auth form

### Acceptance Criteria

- App auto-locks after configurable idle period
- Works both online and offline
- Re-authentication required to access cached data

---

## 4.11 Data Minimization

**Ref:** Proposal Section 2.18 (Data Minimization)

### Sub-Tasks

- [ ] **Selective caching** — only cache user's own active permits
- [ ] **Auto-purge closed/expired** — automatically remove data for closed or expired permits
- [ ] **No system-wide caching** — never cache other users' data or system-wide lists

### Acceptance Criteria

- Only the minimum necessary data is cached
- Closed/expired permit data automatically purged
- No access to other users' permit data offline

---

## 4.12 Auto-Expiry

**Ref:** Proposal Section 2.18 (Auto-Expiry)

### Sub-Tasks

- [ ] **Cache TTL** — all cached data expires after configurable period (24-48 hours)
- [ ] **Expiry check** — periodic check for expired cache entries
- [ ] **Force re-cache** — after expiry, data must be re-fetched from server when online

### Acceptance Criteria

- Cached data expires automatically after 24-48 hours
- Expired data cannot be accessed
- Fresh data fetched on next online access

---

## 4.13 Offline Action Audit

**Ref:** Proposal Section 2.18 (Offline Action Audit)

### Sub-Tasks

- [ ] **Offline timestamp logging** — all offline actions logged with accurate device timestamps
- [ ] **Sync audit trail** — on sync, server records all offline actions in the audit trail
- [ ] **Offline action marker** — audit trail entries from offline actions marked as such
- [ ] **Timestamp validation** — server validates offline timestamps are reasonable

### Acceptance Criteria

- All offline actions logged with timestamps
- Server validates and records every offline action on sync
- Audit trail distinguishes online vs offline actions

---

## Sprint 4 Technical Notes

### Key Libraries

| Library | Purpose |
|---------|---------|
| `next-pwa` or Workbox | Service worker and caching |
| `idb` or `idb-keyval` | IndexedDB wrapper |
| Web Crypto API | Encryption at rest |
| Background Sync API | Auto-sync when online |

### Architecture Pattern

```
[App Layer] → [Sync Manager] → [Encrypted IndexedDB]
                              → [Cache API (PDFs)]
                              → [Action Queue]
     ↕ (when online)
[API Server]
```

### Testing Considerations

- Use Chrome DevTools → Application → Service Workers for debugging
- Simulate offline with DevTools → Network → Offline
- Test on actual mobile devices (service worker behavior varies)
- Test cache encryption/decryption performance
