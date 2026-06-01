# Technical Requirements Document (TRD) — Reference Template

## When to load this file
Load when the user runs `/trd` or `/technical-requirements-document`.

---

## Document Header (always include)

```
# [Project Name] — Technical Requirements Document
Version: 1.0 | Status: Draft | Date: [Date]
Author: [Engineering Lead] | PRD Reference: [PRD Version/Link]
```

---

## Section Definitions

---

### 1. Technical Overview

**1.1 System Summary**
One paragraph: what does this system do technically? What are its core technical responsibilities?

**1.2 Architecture Style**
State the architectural pattern (e.g., monolith, microservices, serverless, event-driven, CQRS) and why it was chosen for THIS project's scale, team, and timeline. Not just what it is — why it's right here.

**1.3 System Context Diagram** (describe as ASCII or prose)

Draw (or describe clearly) the system's boundaries and external actors:

```
[External Actor] → [API Gateway] → [App Server] → [Database]
                                  ↕
                             [Third-Party Service]
```

List every external system that interacts with this product.

---

### 2. Technology Stack

Justify every choice. "We use X because Y" — not just a list.

| Layer | Technology | Version | Rationale |
|---|---|---|---|
| Frontend | e.g., Next.js 14 | 14.x | SSR for SEO, App Router for layouts |
| Backend | e.g., Node.js + Fastify | 20 LTS | Type-safe, fast cold starts |
| Database | e.g., PostgreSQL | 16 | ACID compliance, row-level security |
| Cache | e.g., Redis | 7.x | Session store + rate limiting |
| Auth | e.g., Supabase Auth | — | PKCE flow, built-in OAuth |
| Storage | e.g., S3 + CloudFront | — | CDN for assets |
| Queue | e.g., BullMQ | — | Reliable background jobs |
| Hosting | e.g., Vercel + Railway | — | Zero-config deploys |
| Monitoring | e.g., Sentry + Datadog | — | Error tracking + APM |

---

### 3. System Architecture

**3.1 High-Level Architecture**
Describe each major layer/service and its responsibility. Use clear names that match the actual codebase structure you'll recommend.

**3.2 Service Boundaries** (if microservices or modular monolith)
For each service/module:
- **Name**: What it's called
- **Owns**: What data/domain it owns
- **Exposes**: Its API surface (HTTP/gRPC/events)
- **Depends on**: Other services it calls
- **Scales independently**: Yes/No + reason

**3.3 Deployment Architecture**
How does this run in production?
- Containerized? (Docker, Kubernetes, ECS?)
- Serverless? (Lambda, Vercel Functions?)
- Where? (Region, multi-region?)
- CI/CD pipeline overview (GitHub Actions → where?)

---

### 4. API Design

**4.1 API Style**: REST / GraphQL / tRPC / gRPC — with rationale.

**4.2 Base URL & Versioning**: e.g., `https://api.project.com/v1/`

**4.3 Authentication**: How does auth work on the API? (JWT Bearer, API keys, session cookies, OAuth flow?)

**4.4 Endpoint Reference**

For each major endpoint group, document:

```
POST /auth/register
  Request: { email: string, password: string, name: string }
  Response 201: { userId: uuid, token: JWT }
  Response 400: { error: "email_taken" | "weak_password" }
  Response 429: { error: "rate_limit_exceeded", retryAfter: number }
  Rate limit: 5/min per IP
  Auth required: No

GET /invoices
  Query: ?status=draft|sent|paid&page=1&limit=20
  Response 200: { data: Invoice[], total: number, page: number }
  Auth required: Yes (Bearer token)
  Permissions: User sees only their own invoices; Admin sees all
```

Cover all major CRUD operations for primary entities. Flag which are MVP vs. later.

**4.5 Error Response Format**
Standardize error responses:
```json
{
  "error": {
    "code": "RESOURCE_NOT_FOUND",
    "message": "Invoice with id 'abc-123' does not exist",
    "details": {},
    "requestId": "req_xyz"
  }
}
```

**4.6 Rate Limiting Strategy**
Define limits per endpoint category. How are limits enforced? (Redis token bucket, etc.)

---

### 5. Security Architecture

**5.1 Authentication Flow**
Step-by-step auth flow with token lifetimes, refresh strategy, revocation mechanism.

**5.2 Authorization Model**
Role-based (RBAC) or attribute-based (ABAC)? List roles, their permissions, and how they're enforced (middleware, row-level security, etc.).

**5.3 Data Protection**
- Encryption at rest: which fields, which algorithm
- Encryption in transit: TLS version requirements
- PII fields: which are PII, how are they handled (masked in logs, right to erasure)

**5.4 Security Requirements**
- SQL injection prevention (parameterized queries, ORM)
- XSS prevention (CSP headers, output encoding)
- CSRF protection (SameSite cookies, CSRF tokens)
- Secrets management (env vars, secrets manager — not hardcoded)
- Dependency scanning (Dependabot, Snyk)
- OWASP Top 10 mapping for this project

---

### 6. Data Architecture

*(Brief here — full schema is in Backend Schema document)*

**6.1 Primary Data Store**: Database type, why, hosted where.

**6.2 Data Flow**: How does data move through the system? (User action → API → DB → cache → response)

**6.3 Caching Strategy**:
- What is cached? (Sessions, query results, static assets)
- Cache invalidation strategy
- TTLs for each cache type

**6.4 Data Retention & Deletion**:
- How long is user data kept?
- Soft delete vs hard delete?
- GDPR right-to-erasure implementation

---

### 7. Performance Requirements

Derive from PRD non-functional requirements. Be specific:

| Metric | Target | Measurement Method |
|---|---|---|
| API p50 response time | < 50ms | APM (Datadog/New Relic) |
| API p95 response time | < 200ms | APM |
| API p99 response time | < 500ms | APM |
| Page First Contentful Paint | < 1.2s | Lighthouse CI |
| Page Time to Interactive | < 2.5s | Lighthouse CI |
| Concurrent users (launch) | 500 | Load test (k6) |
| Concurrent users (12mo) | 5,000 | Load test (k6) |
| Database query p95 | < 20ms | Slow query log |
| Uptime SLA | 99.5% | Uptime monitor |

**Load Testing Plan**: Before launch, run [tool] simulating [X] users over [Y] minutes. Pass criteria: [define].

---

### 8. Reliability & Observability

**8.1 Error Handling**
- Global error handler behavior
- Retry logic (which operations retry, how many times, with what backoff?)
- Circuit breaker pattern (if calling external services)
- Graceful degradation (what happens if service X is down?)

**8.2 Logging**
- Log levels used (DEBUG, INFO, WARN, ERROR) and when
- What is always logged: request id, user id (masked), endpoint, duration, status
- What is NEVER logged: passwords, tokens, PII
- Log aggregation: where do logs go? (CloudWatch, Datadog, Loki?)

**8.3 Monitoring & Alerting**
- What metrics are tracked? (error rate, latency, queue depth, DB connections)
- Alerting thresholds and who gets paged
- Dashboard: what's on it?

**8.4 Backup & Recovery**
- Database backup frequency and retention
- Point-in-time recovery capability
- RTO (Recovery Time Objective): [X hours]
- RPO (Recovery Point Objective): [X hours]

---

### 9. Testing Requirements

| Test Type | Tool | Coverage Target | When Run |
|---|---|---|---|
| Unit tests | [Jest/Vitest/pytest] | 80% line coverage | Every commit |
| Integration tests | [Supertest/Playwright] | All API endpoints | PR merge |
| E2E tests | [Playwright/Cypress] | Critical user paths | Pre-deploy |
| Load tests | [k6/Artillery] | Peak traffic scenario | Pre-launch |
| Security scan | [Snyk/OWASP ZAP] | All dependencies | Weekly |

Define what "done" means for each test type. Include test data strategy (fixtures, factories, seeds).

---

### 10. Infrastructure & Deployment

**10.1 Environments**

| Environment | Purpose | Deploy trigger | Data |
|---|---|---|---|
| Local | Development | Manual | Seed data |
| Staging | QA + UAT | PR merge to main | Anonymized prod copy |
| Production | Live users | Manual release | Real data |

**10.2 CI/CD Pipeline**
Step-by-step from code push to production deploy:
1. Developer pushes → PR opened
2. CI runs: lint → type-check → unit tests → integration tests → build
3. If CI passes: preview deploy created
4. PR merged → staging deploy
5. Manual approval → production deploy (blue/green or rolling)

**10.3 Rollback Plan**
How do you roll back a bad deploy? (Feature flags, database migration reversal strategy)

---

### 11. Third-Party Integrations

For each external service:
- **Service**: Name and purpose
- **Integration method**: REST API, SDK, webhook
- **Credentials**: How stored and rotated
- **Fallback**: What happens if this service is down?
- **Cost implication**: Pricing model and expected cost at scale

---

### 12. Technical Decisions Log (ADRs)

For each major architectural decision, document:

**ADR-001: [Decision Title]**
- **Status**: Accepted / Proposed / Deprecated
- **Context**: What situation required this decision?
- **Decision**: What was decided?
- **Rationale**: Why this option over alternatives?
- **Consequences**: What gets easier? What gets harder?
- **Alternatives considered**: [Option A], [Option B] — why rejected?

---

## TRD Anti-Slop Checklist

Before outputting, verify:
- [ ] Every technology choice has a rationale (not just listed)
- [ ] API endpoints have request/response schemas and error codes
- [ ] Security covers auth, authz, data protection, and OWASP Top 10
- [ ] Performance targets have actual numbers and measurement methods
- [ ] Rollback and recovery strategy is documented
- [ ] No section says "use best practices" without defining what they are for this project
- [ ] ADRs capture WHY, not just WHAT was decided
