# Product Requirements Document (PRD) — Reference Template

## When to load this file
Load when the user runs `/prd` or `/product-requirements-document`.

---

## Document Header (always include)

```
# [Project Name] — Product Requirements Document
Version: 1.0 | Status: Draft | Date: [Date]
Author: [Author] | Reviewer: [Reviewer/Stakeholder]
```

---

## Section Definitions

Generate ALL sections below. Each section must contain project-specific content.
The instructions in parentheses are guidance — do not include them in the output.

---

### 1. Executive Summary

Write 2–4 sentences that answer:
- What is being built?
- Who is it for?
- What problem does it solve?
- What is the one metric that will prove this was worth building?

> **Quality bar**: A new engineer reading this section should understand the entire project in 30 seconds. No jargon. No buzzwords.

---

### 2. Problem Statement

**2.1 Current State**
Describe exactly what users do today. Be specific — name the tools, the manual steps, the workarounds. Example: "Sales reps open Salesforce, copy account data to a spreadsheet, paste it into a PowerPoint template, then email it to clients." Not: "The current process is manual and inefficient."

**2.2 Pain Points**
List 3–6 concrete pains with evidence where possible. Each pain should be traceable to a real user action or quote.

**2.3 Opportunity**
What does the world look like if we solve this? What do users gain? What becomes possible that wasn't before?

---

### 3. Goals & Non-Goals

**3.1 Goals (v1 scope)**
List 3–6 specific, measurable goals. Format each as:
- **[Goal]**: [Metric that proves it / Acceptance criteria]

Example:
- **Reduce invoice creation time**: From ~20 minutes to under 3 minutes per invoice
- **Eliminate data entry errors**: Invoice accuracy rate > 99% in first 30 days

**3.2 Non-Goals (explicitly out of scope for v1)**
List what this release will NOT do, and a one-line reason for each.
This is as important as the goals — it prevents scope creep.

---

### 4. Target Users & Personas

For each primary persona (create 2–3), include:

**Persona: [Name] — [Role]**
- **Context**: Where do they work? What's their day like?
- **Technical level**: [Non-technical / Moderate / Power user]
- **Primary job to be done**: [Core task they use this product for]
- **Key frustration today**: [The specific pain this persona has]
- **Success looks like**: [How they'd describe a great experience]

> Personas should be grounded in the project context — not invented archetypes.

---

### 5. User Stories

Format: `As a [persona], I want to [action], so that [outcome].`

Group by feature area. Each story should have:
- **Story ID** (e.g., `US-001`)
- **Priority**: Must Have / Should Have / Could Have (MoSCoW)
- **Acceptance Criteria**: Bullet list of testable conditions

Minimum: 8–15 user stories for a typical product. Cover the full core user journey.

---

### 6. Functional Requirements

Group by feature area (e.g., Authentication, Dashboard, Invoicing, Notifications).

For each requirement:
- **REQ-[N]** — [Requirement statement]
- **Priority**: P0 (launch blocker) / P1 (launch target) / P2 (nice to have)
- **Notes**: Any edge cases, exceptions, or dependencies

Write requirements in active voice: "The system sends an email notification when..." not "Email notifications should be sent when..."

---

### 7. Non-Functional Requirements

Cover ALL of the following that apply:

| Requirement | Specification |
|---|---|
| **Performance** | e.g., "Page load < 2s on 4G. API responses < 200ms p95." |
| **Availability** | e.g., "99.5% uptime. Max planned downtime: 4h/quarter." |
| **Scalability** | e.g., "Support 1,000 users at launch. 10,000 by month 6." |
| **Security** | e.g., "SOC 2 Type II compliance. Data encrypted at rest (AES-256) and in transit (TLS 1.3)." |
| **Accessibility** | e.g., "WCAG 2.1 AA compliance for all web surfaces." |
| **Browser/Device support** | e.g., "Chrome 110+, Firefox 115+, Safari 16+. iOS 16+, Android 13+." |
| **Compliance** | e.g., "GDPR Article 17 right to erasure. HIPAA if applicable." |
| **Internationalization** | e.g., "English only at launch. i18n-ready architecture." |
| **Offline** | e.g., "Core features available offline with sync on reconnect." |

Fill in actual values from the project context. Do not leave rows as vague statements.

---

### 8. User Journey Map

Write a narrative walkthrough of the primary user journey — from first touchpoint to core value.

Format: Step-by-step. For each step:
- **Step [N]: [Step name]**
- What the user does
- What the system does
- What the user sees/feels
- Potential failure point (and how the system handles it)

Cover at least: Onboarding → First core action → Repeat usage → Edge case/error scenario.

---

### 9. Success Metrics (KPIs)

Define how you'll know the product is working. Format as a table:

| Metric | Baseline (today) | Target (90 days) | How to measure |
|---|---|---|---|
| [Metric name] | [Current value] | [Target value] | [Analytics event / query] |

Include: activation rate, retention (D7, D30), core action completion rate, error rate, NPS or CSAT.

---

### 10. Assumptions & Dependencies

**Assumptions** — things believed to be true but not yet verified:
- List each assumption. Flag it with `⚠️ Assumption` if it's high-risk.

**Dependencies** — external things this product requires to ship:
- Third-party APIs, internal services, team deliverables, legal approvals, etc.
- For each: what it is, who owns it, when it's needed, what happens if it's late.

---

### 11. Risks & Mitigations

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| [Risk description] | High/Med/Low | High/Med/Low | [Specific mitigation action] |

Cover: technical risks, market/adoption risks, dependency risks, team/resource risks.

---

### 12. Open Questions

List anything that's unresolved. Each item should have:
- **Q[N]**: [The question]
- **Owner**: [Who needs to answer this]
- **Deadline**: [When it needs to be resolved]
- **Impact if unresolved**: [What breaks or gets blocked]

---

### 13. Appendices (optional)

Include if relevant:
- Competitive analysis
- User research quotes / data
- Mockup references
- Glossary of terms

---

## PRD Anti-Slop Checklist

Before outputting, verify:
- [ ] Every user story has testable acceptance criteria
- [ ] Non-functional requirements have actual numbers, not vague adjectives
- [ ] Goals have measurable success metrics
- [ ] Personas are project-specific, not generic archetypes
- [ ] Out-of-scope items are explicitly listed with reasons
- [ ] Open questions have owners and deadlines
- [ ] No section contains `[TODO]` or placeholder text
