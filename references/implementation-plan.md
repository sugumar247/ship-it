# Implementation Plan — Reference Template

## When to load this file
Load when the user runs `/impl` or `/implementation-plan`.

---

## Document Header (always include)

```
# [Project Name] — Implementation Plan
Version: 1.0 | Status: Draft | Date: [Date]
Author: [Name] | PRD Reference: [PRD Version/Link]
Timeline: [Start Date] → [Target Launch Date]
Team size: [N people]
```

---

## What this document covers

The Implementation Plan is the **execution roadmap** — turning requirements into a delivery schedule.
It answers: Who does what, in what order, and how do we know we're on track?

This document should be updated weekly as the project progresses.

---

## Section Definitions

---

### 1. Project Summary

One paragraph: What are we building, why, by when, and for whom?
Include the single most important constraint (timeline, budget, team, scope).

**Current State**: [What's already built? What's the starting point?]
**Target State at Launch**: [What will exist when this is done?]
**Launch Definition**: [What must be true for us to call this "launched"?]

---

### 2. Team & Roles

| Name | Role | Responsibility | Availability |
|---|---|---|---|
| [Name] | Tech Lead / Architect | Architecture, code review, unblocking | Full-time |
| [Name] | Frontend Engineer | React/UI implementation | Full-time |
| [Name] | Backend Engineer | API, database, infra | Full-time |
| [Name] | Designer | UI/UX (if not already done) | Part-time |
| [Name] | Product Manager | Prioritization, stakeholder comms | Part-time |
| [Name] | QA | Test planning, bug triage | Part-time from Sprint 2 |

**Decision-maker**: [Who has final say on scope changes?]
**External dependencies**: [Third parties, vendors, other teams we rely on]

---

### 3. Phases & Milestones

Break the project into phases. Each phase should be independently deliverable and reviewable.

| Phase | Name | Duration | Goal | Success Criteria |
|---|---|---|---|---|
| 0 | Setup & Foundation | 1 week | Dev environment, CI/CD, project scaffolding | All devs can run the app locally; CI pipeline green |
| 1 | Core Backend | 2 weeks | Database + auth + primary API endpoints | All P0 API endpoints tested and documented |
| 2 | Core Frontend | 2 weeks | Primary screens functional against real API | Core user flow works end-to-end |
| 3 | Polish & Edge Cases | 1 week | Error handling, loading states, empty states | 0 P0 bugs; all user flow paths work |
| 4 | Beta / Soft Launch | 1 week | Internal testing + first real users | 5 beta users complete core flow; 0 critical bugs |
| 5 | Public Launch | — | Launch! | Launch checklist complete |

Total timeline: [X weeks] from [start date] to [launch date].

---

### 4. Sprint Plan

*(Use 1-week or 2-week sprints consistently. Pick what fits the team.)*

---

**Sprint 0: Setup & Foundation** [Week 1]

**Goal**: Every developer has a working local environment and the project skeleton is in place.

| Task | Owner | Estimate | Priority |
|---|---|---|---|
| Set up GitHub repo, branch strategy, PR template | Tech Lead | 2h | P0 |
| Configure CI pipeline (lint, type-check, unit tests) | Tech Lead | 4h | P0 |
| Set up staging and production environments | Tech Lead | 4h | P0 |
| Initialize frontend project (Next.js/Vite, TypeScript, ESLint, Tailwind) | FE Eng | 4h | P0 |
| Initialize backend project (framework, TS, ORM, folder structure) | BE Eng | 4h | P0 |
| Set up database (local + staging) and run initial migration | BE Eng | 3h | P0 |
| Configure secrets management | Tech Lead | 2h | P0 |
| Write README: local setup guide | Tech Lead | 2h | P0 |
| Design system / component library setup | FE Eng | 3h | P1 |
| Error tracking setup (Sentry) | BE Eng | 2h | P1 |

**Sprint 0 DoD**: PR opened, reviewed, and merged for all tasks. All devs confirm local setup works in <10 minutes from README.

---

**Sprint 1: Authentication & Core Data Models** [Week 2]

**Goal**: Users can register, log in, and the core data schema is live.

| Task | Owner | Estimate | Depends on |
|---|---|---|---|
| Implement auth schema (users, sessions tables) | BE Eng | 4h | Sprint 0 |
| Implement register/login/logout API | BE Eng | 6h | Auth schema |
| Implement JWT + refresh token flow | BE Eng | 4h | Auth API |
| Create core schema migrations (invoices, clients, etc.) | BE Eng | 5h | Sprint 0 |
| Login / Register screens | FE Eng | 6h | Auth API |
| Auth state management + protected routes | FE Eng | 4h | Auth screens |
| Unit tests: auth endpoints | BE Eng | 3h | Auth API |
| API error handling middleware | BE Eng | 3h | Sprint 0 |

**Sprint 1 DoD**: User can register with email/password, log in, and receive a JWT. Token is used to access a protected test route. All auth endpoints have >80% unit test coverage.

---

*(Continue with Sprint 2, 3, 4, 5... for each phase of development)*
*(Each sprint must have: goal, task table with owner + estimate + dependency, and Definition of Done)*

---

### 5. Task Breakdown by Feature

Group tasks by feature area. This complements the sprint plan and helps track completeness.

For each feature, define tasks at the sub-day level (no task > 1 day):

**Feature: Invoice CRUD**
- [ ] `BE` Create invoice table migration
- [ ] `BE` `POST /invoices` — create invoice endpoint
- [ ] `BE` `GET /invoices` — list with filters + pagination
- [ ] `BE` `GET /invoices/:id` — detail view
- [ ] `BE` `PATCH /invoices/:id` — update invoice
- [ ] `BE` `DELETE /invoices/:id` — soft delete
- [ ] `BE` Invoice number auto-generation (sequential per org)
- [ ] `BE` Unit tests for all invoice endpoints
- [ ] `FE` Invoice list screen with filters
- [ ] `FE` Invoice list loading + empty states
- [ ] `FE` New invoice form
- [ ] `FE` Invoice form validation
- [ ] `FE` Invoice detail view
- [ ] `FE` Edit invoice flow
- [ ] `FE` Delete confirmation modal
- [ ] `FE` Optimistic update for status changes

**Feature: Email Send**
- [ ] `BE` Email service integration (SendGrid/Resend)
- [ ] `BE` Invoice HTML email template
- [ ] `BE` `POST /invoices/:id/send` endpoint
- [ ] `BE` Track sent/viewed status (via pixel or link tracking)
- [ ] `FE` "Send Invoice" modal with preview
- [ ] `FE` Email sent confirmation state

*(Cover all features from the PRD. Use `BE` / `FE` / `Design` / `QA` prefixes for clarity.)*

---

### 6. Dependencies & Critical Path

The critical path is the sequence of tasks where a delay in ANY task delays the launch date.

**Critical path for this project:**
```
Auth schema → Auth API → Auth UI → Core data schema → Invoice API → Invoice UI → Email send → QA → Launch
```

**Blocking dependencies** (if these slip, the launch slips):
| Dependency | Type | Owner | Needed by | Risk if late |
|---|---|---|---|---|
| Stripe/payment gateway approval | External | PM | Week 3 | Can't launch payment features |
| Design system finalized | Internal | Designer | Week 1 | Frontend can't start |
| Legal review of T&C | External | Stakeholder | Week 5 | Can't launch publicly |

---

### 7. Risk Register

| Risk | Likelihood | Impact | Mitigation | Owner |
|---|---|---|---|---|
| Scope creep from stakeholders | High | High | PRD sign-off before Sprint 1. Change requests go through PM. | PM |
| Auth complexity (OAuth edge cases) | Medium | High | Use proven auth library (Supabase/Auth.js). Spike in Sprint 0. | BE Lead |
| Third-party API rate limits hit unexpectedly | Low | Medium | Add caching layer + mock API for dev env | BE Eng |
| Team member unavailability | Medium | High | Document as you go. No single-person dependencies. | Tech Lead |
| Performance issues under load | Low | High | Load test in Sprint 4. Define pass/fail before starting. | BE Lead |

---

### 8. Quality Gates

Before moving to the next phase, these must be true:

**Before Phase 1 → Phase 2 (Backend → Frontend)**:
- [ ] All P0 API endpoints return correct data
- [ ] Auth flow (register → login → protected route) works end-to-end
- [ ] Postman/Bruno collection for all endpoints is up to date
- [ ] No unresolved P0 bugs in the backend

**Before Phase 2 → Phase 3 (Frontend → Polish)**:
- [ ] Core user flow works end-to-end in staging
- [ ] No console errors or React warnings in core flows
- [ ] Mobile layout is functional (not just desktop)

**Before Phase 3 → Phase 4 (Polish → Beta)**:
- [ ] All P0 and P1 user stories are marked complete
- [ ] All error states, empty states, and loading states are implemented
- [ ] Lighthouse CI score: Performance > 80, Accessibility > 90
- [ ] No P0 bugs open

**Launch Checklist**:
- [ ] All Beta feedback resolved or triaged
- [ ] Security review complete (OWASP Top 10 checked)
- [ ] Load test passed (target: [X] concurrent users)
- [ ] Monitoring, alerts, and on-call rotation set up
- [ ] Database backups verified
- [ ] SSL certificates valid (and auto-renewal configured)
- [ ] Privacy policy, Terms of Service, Cookie policy published
- [ ] Error tracking (Sentry) confirmed receiving events
- [ ] Runbook for common incidents written
- [ ] Post-launch support plan in place

---

### 9. Communication & Process

**Standups**: [Daily / 3x week / async via Slack]. Format: What did I do? What will I do? Any blockers?

**Sprint cadence**:
- Sprint planning: Monday AM ([X hours])
- Sprint review: Friday PM ([X hours]) — demo to stakeholders
- Retrospective: Friday PM ([Y mins]) — just the dev team

**Issue tracking**: [Linear / Jira / GitHub Issues / Notion]
**Code review**: All PRs require 1 reviewer. No self-merges. Max 24h review turnaround.
**Branch strategy**: `main` (protected) ← `develop` ← `feature/[ticket-id]-description`
**Deployment**: Staging auto-deploys on merge to `develop`. Production is manual trigger.

**Escalation**: If blocked > 4 hours → post in #engineering channel. If blocked > 1 day → PM + Tech Lead meeting.

---

### 10. Post-Launch Plan

**Week 1 post-launch:**
- Monitor error rate, API latency, and user activity in real time
- Daily bug triage. P0 bug SLA: fix within 4 hours
- [Name] is primary on-call

**Metric review at Day 7, Day 30, Day 90**:
- Check against KPIs defined in the PRD
- Share results with stakeholders

**Phase 2 feature planning**:
- Collect user feedback via [in-app survey / support tickets / user interviews]
- Prioritize Phase 2 features based on data from Phase 1
- Write Phase 2 PRD by [date]

---

## Implementation Plan Anti-Slop Checklist

Before outputting, verify:
- [ ] Every sprint has a clear goal, task table, and Definition of Done
- [ ] Every task has an owner and estimate (no orphaned tasks)
- [ ] Critical path is identified — not just a list of all tasks
- [ ] Dependencies are listed with dates and risk consequences
- [ ] Launch checklist covers security, monitoring, backups, and legal
- [ ] Post-launch plan specifies who's on-call and how bugs are triaged
- [ ] Risk register has mitigations (not just a list of things that could go wrong)
- [ ] Timeline is realistic — not just divide requirements by team size
