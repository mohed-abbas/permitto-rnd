# Sprint 5 — Mobile Optimization & Testing (Weeks 10-11)

**Goal:** All pages audited and optimized for mobile/tablet, comprehensive testing across browsers and workflows, performance optimization, and all bug fixes from previous sprints resolved.

**Payment Milestone:** $1,200 on completion of mobile optimization and full testing.

**Prerequisites:** Sprints 1-4 must be complete (all features including PWA/offline operational).

---

## 5.1 Mobile Responsiveness

**Ref:** Proposal Section 1.1 #19

### Sub-Tasks

- [ ] **Page-by-page audit** — test every page on mobile (375px), tablet (768px), and desktop (1024px+)
- [ ] **Dashboard pages** — ensure role-based dashboards work on all screen sizes
- [ ] **Permit form filling** — multi-step forms usable on mobile (field sizing, keyboard handling)
- [ ] **Template builder** — ensure builder works on tablet (may not be fully mobile)
- [ ] **PTW Register** — responsive table with horizontal scroll or card view on mobile
- [ ] **Approval flows** — approve/reject modals sized correctly on mobile
- [ ] **Daily approval** — bulk approval UI optimized for mobile
- [ ] **SIMOPs screens** — countersign and checklist usable on mobile
- [ ] **Notification center** — dropdown and full page work on mobile
- [ ] **PDF viewer** — in-browser PDF display works on mobile browsers
- [ ] **Navigation** — sidebar collapses correctly, mobile nav works smoothly

### Acceptance Criteria

- Every page functional on 375px (mobile), 768px (tablet), 1024px+ (desktop)
- No horizontal overflow or broken layouts
- All interactive elements reachable on all screen sizes

---

## 5.2 Touch Target Optimization

**Ref:** Proposal Section 3 (Sprint 5)

### Sub-Tasks

- [ ] **Button sizing** — all interactive buttons minimum 44x44px touch target
- [ ] **Form inputs** — adequate spacing between form fields for finger tapping
- [ ] **Signature pad** — verify signature drawing works well on mobile touchscreens
- [ ] **Checkbox/radio targets** — enlarged hit areas for checkboxes and radio buttons
- [ ] **Swipe gestures** — consider swipe for navigation where appropriate (e.g., form steps)
- [ ] **Tap feedback** — visual feedback on touch interactions

### Acceptance Criteria

- All touch targets at least 44x44px
- Form filling comfortable on mobile devices
- Signature capture reliable on touchscreens
- No mis-taps from elements too close together

---

## 5.3 Cross-Browser Testing

**Ref:** Proposal Section 3 (Sprint 5)

### Sub-Tasks

- [ ] **Chrome** (latest) — full workflow testing
- [ ] **Safari** (latest, iOS + macOS) — full workflow testing
- [ ] **Edge** (latest) — full workflow testing
- [ ] **Firefox** (latest) — full workflow testing
- [ ] **PWA installation** — verify install works on all supported browsers
- [ ] **Service worker** — verify offline mode works across browsers
- [ ] **CSS compatibility** — check for vendor-specific CSS issues
- [ ] **JavaScript compatibility** — verify Web Crypto API, IndexedDB, Cache API across browsers

### Acceptance Criteria

- Application fully functional on Chrome, Safari, Edge, Firefox
- PWA installable on Chrome, Edge (Safari has limited support)
- No browser-specific rendering issues

---

## 5.4 End-to-End Workflow Testing

**Ref:** Proposal Section 3 (Sprint 5)

### Test Scenarios

#### Core Workflow (Sprint 1)

- [ ] **Happy path:** WPR creates permit → WPI approves → PTW-PC approves → HSSE approves → Active → Daily approval → Close-out
- [ ] **Rejection path:** Each approver rejects → WPR receives feedback → Revise & resubmit
- [ ] **Additional measures:** Approver requests measures → WPR implements → Resubmit
- [ ] **Daily re-approval:** All roles re-approve → Permit stays active
- [ ] **Suspension:** Miss daily deadline → Auto-suspension → Reactivation
- [ ] **Cancellation:** PTW-PC cancels permit → Register updated

#### SIMOPs (Sprint 2)

- [ ] **SIMOPs detection:** Two permits in same zone → SIMOPs gate triggered
- [ ] **Countersign:** Other WPI reviews and countersigns → Permit proceeds
- [ ] **Withhold:** WPI withholds → Permit blocked until resolved
- [ ] **SIMOPs checklist:** Complete checklist before resolution

#### Condition Change (Sprint 2)

- [ ] **Continue:** Flag condition → WPI assesses → Continue work
- [ ] **Re-permit:** Flag condition → WPI triggers re-permit → New draft created
- [ ] **Cancel:** Flag condition → WPI cancels → Permit cancelled

#### Extension (Sprint 2)

- [ ] **Grant:** WPR requests → WPI grants → Dates updated → Register updated
- [ ] **Deny:** WPR requests → WPI denies → Proceed to close-out

#### PDF (Sprint 3)

- [ ] **All 7 types:** Generate PDF for each permit type → Verify layout matches physical format
- [ ] **PDF with SIMOPs data:** Verify SIMOPs checklist included
- [ ] **PDF with extensions:** Verify extension history included

#### Offline (Sprint 4)

- [ ] **Offline viewing:** Go offline → View cached permits → Correct data displayed
- [ ] **Offline form filling:** Go offline → Fill form → Go online → Auto-sync
- [ ] **Sync conflict:** Go offline → Make changes → Server state changed → Conflict resolution

### Implementation

- [ ] **Write Playwright E2E tests** for all critical paths
- [ ] **Create test data fixtures** — seed data covering all scenarios
- [ ] **Automated test suite** — run on CI pipeline
- [ ] **Manual QA checklist** — cover edge cases not easily automated

### Acceptance Criteria

- All happy paths pass
- All error/rejection paths handled correctly
- Edge cases documented and tested

---

## 5.5 SIMOPs Edge Cases

**Ref:** Proposal Section 3 (Sprint 5)

### Sub-Tasks

- [ ] **3+ permits in same zone** — verify SIMOPs handles multiple overlapping permits
- [ ] **Sequential overlaps** — Permit A and Permit B overlap, Permit B and Permit C overlap, but A and C don't
- [ ] **Zone boundary** — permits in adjacent zones (should NOT trigger SIMOPs)
- [ ] **Time boundary** — permits that barely overlap (1 minute)
- [ ] **Active + pending** — one active, one pending in same zone
- [ ] **Extension affecting SIMOPs** — extending a permit creates new overlap

### Acceptance Criteria

- All edge cases handled correctly
- No false positives or false negatives in SIMOPs detection

---

## 5.6 Offline Sync Testing

**Ref:** Proposal Section 3 (Sprint 5)

### Sub-Tasks

- [ ] **Queue integrity** — verify action queue preserves order through multiple offline sessions
- [ ] **Large queue sync** — 10+ queued actions sync correctly
- [ ] **Partial sync failure** — some actions succeed, some fail → correct state
- [ ] **Network flap** — repeated online/offline transitions during sync
- [ ] **Concurrent offline users** — two users offline approve same permit → server resolves
- [ ] **Cache corruption recovery** — handle corrupted IndexedDB gracefully

### Acceptance Criteria

- Sync reliable under all tested conditions
- Conflicts resolved gracefully
- No data loss

---

## 5.7 Performance Optimization

**Ref:** Proposal Section 3 (Sprint 5)

### Sub-Tasks

- [ ] **Page load audit** — Lighthouse scores for all key pages (target: 90+ performance)
- [ ] **API response times** — all endpoints respond within 500ms under normal load
- [ ] **Database query optimization** — identify and optimize slow queries (add indexes)
- [ ] **Bundle size audit** — analyze and optimize JS bundle sizes
- [ ] **Image optimization** — optimize all static images, use next/image
- [ ] **Lazy loading** — lazy load non-critical components and routes
- [ ] **Caching headers** — proper cache-control headers on API responses

### Acceptance Criteria

- Lighthouse performance score 90+ on key pages
- API responses under 500ms
- First Contentful Paint under 1.5s

---

## 5.8 Bug Fixes

**Ref:** Proposal Section 3 (Sprint 5)

### Sub-Tasks

- [ ] **Collect all bugs** from Sprint 1-4 reviews
- [ ] **Prioritize by severity** — critical → high → medium → low
- [ ] **Fix all critical and high bugs**
- [ ] **Document known medium/low issues** for Sprint 6 if needed
- [ ] **Regression testing** — verify fixes don't break existing functionality

### Acceptance Criteria

- All critical and high-severity bugs fixed
- No regressions introduced
- Remaining issues documented with severity

---

## Sprint 5 Deliverables

| Deliverable | Description |
|-------------|-------------|
| Mobile audit report | Page-by-page responsiveness status |
| Cross-browser test results | Pass/fail per browser |
| E2E test suite | Automated Playwright tests |
| Performance report | Lighthouse scores, API timings |
| Bug fix list | All bugs fixed with PR references |
| Remaining issues | Documented medium/low issues |
