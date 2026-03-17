# Sprint 6 — UAT & Production Deployment (Weeks 12-13)

**Goal:** Client UAT on staging, all feedback resolved, production deployed on client's VPS, initial data setup, handover complete.

**Payment Milestone:** $1,200 on production go-live and handover.

**Prerequisites:** Sprints 1-5 must be complete (all features built, tested, mobile-optimized, bugs fixed).

---

## 6.1 Client UAT (User Acceptance Testing)

**Ref:** Proposal Section 3 (Sprint 6)

### Sub-Tasks

- [ ] **UAT environment preparation** — staging environment with fresh, clean test data
- [ ] **UAT test accounts** — create accounts for all 5 roles for client testers
- [ ] **UAT guide** — document for client team explaining what to test and how
- [ ] **UAT kickoff meeting** — walk client team through the system and testing process
- [ ] **Client testing period** — client team tests all workflows on staging (1-2 weeks)
- [ ] **Issue tracking** — collect and track all UAT-reported issues
- [ ] **Daily standup during UAT** — brief check-in with client on progress and blockers

### Acceptance Criteria

- Client team has tested all 5 roles
- All critical workflows tested: permit creation, 4-level approval, daily re-approval, SIMOPs, condition change, extension, close-out
- All issues documented and prioritized

---

## 6.2 UAT Feedback Fixes

**Ref:** Proposal Section 3 (Sprint 6)

### Sub-Tasks

- [ ] **Triage UAT issues** — categorize as critical, high, medium, low
- [ ] **Fix critical issues** — must be resolved before go-live
- [ ] **Fix high issues** — should be resolved before go-live
- [ ] **Document deferred issues** — medium/low issues documented for post-launch fixes
- [ ] **Re-test fixes** — verify each fix on staging
- [ ] **UAT sign-off** — client confirms all critical/high issues resolved

### Acceptance Criteria

- All critical and high UAT issues resolved
- Client signs off on UAT
- Deferred issues documented with timeline

---

## 6.3 Final Adjustments

**Ref:** Proposal Section 3 (Sprint 6)

### Sub-Tasks

- [ ] **UI tweaks** — color, spacing, font adjustments per client feedback
- [ ] **Wording changes** — labels, messages, button text per client preference
- [ ] **Workflow adjustments** — minor workflow behavior changes per client feedback
- [ ] **Notification timing** — adjust daily reminder, suspension, and expiry times per client ops schedule
- [ ] **Branding finalization** — ensure company logo, colors, and branding are correct

### Acceptance Criteria

- UI matches client expectations
- All wording approved by client
- Notification timing aligned with client operations

---

## 6.4 Client VPS Setup

**Ref:** Proposal Section 2.19, Section 3 (Sprint 6)

### Sub-Tasks

- [ ] **VPS access** — obtain SSH access to client-provided VPS
- [ ] **System requirements check** — verify VPS meets minimum specs (CPU, RAM, disk)
- [ ] **Install Docker** — Docker Engine + Docker Compose on VPS
- [ ] **Install PostgreSQL** — set up PostgreSQL database (or Docker container)
- [ ] **Install Redis** — set up Redis instance (or Docker container)
- [ ] **Domain configuration** — configure client's domain to point to VPS
- [ ] **SSL certificate** — obtain and configure SSL/TLS certificate (Let's Encrypt)
- [ ] **Firewall configuration** — configure firewall rules (ports 80, 443, SSH)
- [ ] **Email service setup** — configure client's email service for transactional emails
- [ ] **Environment variables** — configure all production environment variables
- [ ] **Backup setup** — configure automated daily database backups with 30-day retention
- [ ] **Monitoring setup** — basic server health monitoring and alerting

### Acceptance Criteria

- VPS running Docker with PostgreSQL and Redis
- Domain resolves to VPS with valid SSL
- Email service configured and sending
- Automated backups running
- Basic monitoring in place

---

## 6.5 Production Deployment

**Ref:** Proposal Section 2.19, Section 3 (Sprint 6)

### Sub-Tasks

- [ ] **Build production images** — build Docker images for API and Web with production targets
- [ ] **Configure production docker-compose** — production-specific compose file
- [ ] **Deploy to VPS** — push images and start containers on client VPS
- [ ] **Run database migrations** — apply all Prisma migrations on production database
- [ ] **Configure reverse proxy** — Nginx/Caddy for SSL termination and routing
- [ ] **Health check verification** — all services responding on production
- [ ] **SSL verification** — HTTPS working correctly with valid certificate
- [ ] **Background jobs verification** — all cron jobs running on production schedule

### Acceptance Criteria

- Application accessible on client's domain over HTTPS
- All services healthy and responding
- Background jobs running on schedule
- No errors in production logs

---

## 6.6 Initial Data Setup

**Ref:** Proposal Section 3 (Sprint 6)

### Sub-Tasks

- [ ] **Create Super Admin account** — first admin user for the client
- [ ] **Create initial projects** — set up client's active projects
- [ ] **Create zones** — set up zones within projects
- [ ] **Create user accounts** — set up initial PTW-PC, WPI, HSSE, WPR accounts
- [ ] **Create permit templates** — set up 7 permit type templates per client's formats
- [ ] **Assign users to projects** — configure user-project assignments
- [ ] **Configure system settings** — notification times, suspension deadlines, etc.

### Acceptance Criteria

- All initial users can log in
- Projects and zones set up correctly
- Templates ready for permit creation
- System configured for client's operational schedule

---

## 6.7 Production Smoke Test

**Ref:** Proposal Section 3 (Sprint 6)

### Sub-Tasks

- [ ] **Full workflow test** — create permit → 4-level approval → daily re-approval → close-out on production
- [ ] **Email delivery test** — verify emails arrive from production
- [ ] **PDF generation test** — generate PDF on production, verify layout
- [ ] **File upload test** — upload supporting documents on production
- [ ] **Background job test** — verify cron jobs fire at scheduled times
- [ ] **PWA installation test** — install from production on mobile device
- [ ] **Offline mode test** — verify offline functionality on production
- [ ] **Performance check** — page load times acceptable on production hardware

### Acceptance Criteria

- Full workflow completes without errors
- All integrations (email, PDF, files, jobs) working
- Performance acceptable

---

## 6.8 Handover

**Ref:** Proposal Section 3 (Sprint 6), Section 7

### Sub-Tasks

- [ ] **Admin credentials delivery** — securely deliver Super Admin login credentials to client
- [ ] **System documentation** — hand over:
  - System architecture overview
  - API documentation
  - Database schema documentation
  - Deployment/infrastructure guide
  - Backup and recovery procedures
- [ ] **Source code delivery** — full source code repository access to client
- [ ] **Training session** — conduct train-the-trainer session (3-4 hours, see Section 4)
- [ ] **In-app training page** — verify training & help page is accessible and complete
- [ ] **Support channel setup** — establish ongoing support communication channel (WhatsApp/Teams/email)
- [ ] **Handover sign-off** — client confirms receipt of all deliverables

### Training Session Topics (Ref: Proposal Section 4)

1. System Overview & Navigation
2. User & Project Setup
3. Profile Signature Setup
4. Template Builder
5. Template Modification
6. Permit Request Flow (WPR)
7. 4-Level Approval Flow
8. SIMOPs Management
9. Daily Re-Approval
10. Condition Change & Extensions
11. Close-Out & Cancellation
12. PTW Register
13. PDF Generation
14. Q&A

### In-App Training Page Features

- [ ] **Role-based guides** — content filtered by user role
- [ ] **Step-by-step walkthroughs** with screenshots
- [ ] **Video walkthroughs** — recorded screen recordings
- [ ] **Quick reference** per role (one-page summary)
- [ ] **FAQ section**
- [ ] **Searchable** content
- [ ] **Always accessible** via help icon in navigation

### Acceptance Criteria

- Client has all credentials and documentation
- Source code delivered
- Training completed with client team
- In-app help page operational
- Support channel established
- Client signs off on handover

---

## Sprint 6 Deliverables Summary

| Deliverable | Description |
|-------------|-------------|
| UAT completion | Client-tested, signed-off staging environment |
| Production deployment | Live application on client's VPS with HTTPS |
| Initial data | Users, projects, zones, templates configured |
| Documentation | Architecture, API, deployment, backup guides |
| Source code | Full repository access |
| Training | 3-4 hour train-the-trainer session |
| In-app help | Training & help page with guides, videos, FAQ |
| Handover sign-off | Client confirmation of all deliverables |

---

## Post-Sprint 6: Hypercare

After go-live, the 4-week hypercare period begins (included in the monthly support & maintenance fee):

- Priority response: critical within 2 hours, other within 4 hours
- Daily system health checks
- Immediate bug fixes
- Direct communication channel with development team
