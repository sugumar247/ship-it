# Backend Schema Document — Reference Template

## When to load this file
Load when the user runs `/schema` or `/backend-schema`.

---

## Document Header (always include)

```
# [Project Name] — Backend Schema Document
Version: 1.0 | Status: Draft | Date: [Date]
Author: [Name] | TRD Reference: [TRD Version/Link]
Database: [PostgreSQL 16 / MongoDB 7 / MySQL 8 / etc.]
```

---

## What this document covers

The Schema document defines the **complete data model** for the backend:
- Database tables/collections with all fields, types, and constraints
- Relationships between entities (with foreign keys and cardinality)
- Indexes and performance considerations
- API request/response shapes (the full contract)
- Seed data, migrations strategy, and validation rules

This is NOT duplicating the TRD (which covers architecture). This IS the ground truth for the data layer.

---

## Section Definitions

---

### 1. Data Model Overview

**1.1 Entity Map**
List all primary entities (tables/collections) and their purpose:

| Entity | Description | Estimated row count at launch | Growth pattern |
|---|---|---|---|
| `users` | Authenticated user accounts | 500 | Slow (new signups) |
| `organizations` | Team workspaces | 100 | Slow |
| `invoices` | Invoice records | 5,000 | Fast |
| `invoice_items` | Line items on invoices | 25,000 | Fast |
| `clients` | Client contact records | 1,000 | Medium |
| `payments` | Payment records | 3,000 | Medium |

**1.2 Entity Relationship Summary**
Describe the relationships in plain English before the formal schemas:

Example:
- A `user` belongs to one `organization`
- An `organization` has many `users`, many `clients`, many `invoices`
- An `invoice` belongs to one `client` and one `organization`
- An `invoice` has many `invoice_items`
- An `invoice` may have zero or one `payment`

**1.3 ERD (Entity Relationship Diagram)**
Draw the ER diagram as ASCII or describe clearly:

```
organizations (1) ──< (many) users
organizations (1) ──< (many) clients
organizations (1) ──< (many) invoices
invoices (1) ──< (many) invoice_items
invoices (1) ──── (0 or 1) payments
clients (1) ──< (many) invoices
```

---

### 2. Table / Collection Schemas

For each entity, define the complete schema. Use the appropriate format for the database type.

**For SQL (PostgreSQL / MySQL / SQLite):**

```sql
-- ============================================================
-- Table: users
-- ============================================================
CREATE TABLE users (
  id            UUID          PRIMARY KEY DEFAULT gen_random_uuid(),
  email         VARCHAR(255)  NOT NULL UNIQUE,
  email_verified BOOLEAN      NOT NULL DEFAULT false,
  password_hash VARCHAR(255)  NULL,  -- NULL for OAuth-only users
  full_name     VARCHAR(255)  NOT NULL,
  avatar_url    TEXT          NULL,
  role          user_role     NOT NULL DEFAULT 'member',  -- enum: admin, member, viewer
  org_id        UUID          NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
  last_login_at TIMESTAMPTZ   NULL,
  created_at    TIMESTAMPTZ   NOT NULL DEFAULT NOW(),
  updated_at    TIMESTAMPTZ   NOT NULL DEFAULT NOW(),
  deleted_at    TIMESTAMPTZ   NULL  -- soft delete
);

-- Indexes
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_org_id ON users(org_id);
CREATE INDEX idx_users_deleted_at ON users(deleted_at) WHERE deleted_at IS NULL;

-- Enum type (define before table)
CREATE TYPE user_role AS ENUM ('admin', 'member', 'viewer');
```

Do this for EVERY table. Include:
- All columns with data types, constraints (NOT NULL, UNIQUE, DEFAULT)
- Foreign key references with ON DELETE / ON UPDATE behavior
- Enum types before tables that use them
- Indexes (explain WHY each index exists — what query does it serve?)
- Soft delete pattern if used (include `deleted_at` column and filtered index)

**For NoSQL (MongoDB):**

```typescript
// Collection: invoices
// Validator schema (MongoDB JSON Schema)
interface Invoice {
  _id: ObjectId;
  invoiceNumber: string;          // e.g., "INV-0042", unique per org
  orgId: ObjectId;                // ref: organizations
  clientId: ObjectId;             // ref: clients
  createdBy: ObjectId;            // ref: users
  status: 'draft' | 'sent' | 'viewed' | 'paid' | 'overdue' | 'cancelled';
  issueDate: Date;
  dueDate: Date;
  lineItems: {
    description: string;
    quantity: number;             // positive, max 2 decimal places
    unitPrice: number;            // in cents (integer), avoid float math
    taxRate: number;              // 0–100, percentage
    total: number;                // computed: quantity * unitPrice * (1 + taxRate/100)
  }[];
  subtotal: number;               // in cents
  taxTotal: number;               // in cents
  total: number;                  // in cents
  currency: string;               // ISO 4217, e.g., "USD"
  notes: string | null;
  paymentTerms: string | null;    // e.g., "Net 30"
  sentAt: Date | null;
  paidAt: Date | null;
  createdAt: Date;
  updatedAt: Date;
}

// Indexes
// { orgId: 1, status: 1 }     — list by org filtered by status
// { orgId: 1, clientId: 1 }   — invoices per client
// { orgId: 1, invoiceNumber: 1 } UNIQUE — enforce number uniqueness per org
// { dueDate: 1 }               — overdue detection jobs
```

---

### 3. Enum & Type Definitions

List all enums, custom types, and constants used in the schema:

```sql
CREATE TYPE invoice_status AS ENUM (
  'draft',      -- created, not sent
  'sent',       -- emailed to client
  'viewed',     -- client opened the link
  'paid',       -- payment recorded
  'overdue',    -- past due date, unpaid
  'cancelled'   -- voided
);

CREATE TYPE payment_method AS ENUM (
  'bank_transfer',
  'credit_card',
  'paypal',
  'cash',
  'other'
);
```

---

### 4. Data Validation Rules

Beyond database constraints, document application-level validation:

| Field | Rule | Error message |
|---|---|---|
| `email` | Valid email format, max 255 chars | "Enter a valid email address" |
| `password` | Min 8 chars, 1 uppercase, 1 number | "Password must be at least 8 characters with one number and uppercase letter" |
| `invoice.dueDate` | Must be >= issueDate | "Due date cannot be before issue date" |
| `invoice_item.quantity` | > 0, max 2 decimal places | "Quantity must be greater than 0" |
| `invoice_item.unitPrice` | >= 0 (allow free items) | "Price cannot be negative" |
| `currency` | Valid ISO 4217 code | "Enter a valid currency code (e.g., USD, EUR)" |

---

### 5. Row-Level Security / Authorization Rules

For each table, define who can read/write what:

| Table | Read | Create | Update | Delete |
|---|---|---|---|---|
| `users` | Own row; Admin sees org members | Auth service only | Own row (except role); Admin can change role | Admin only (soft delete) |
| `invoices` | Own org's invoices | Authenticated member | Owner or Admin | Owner or Admin (soft delete) |
| `clients` | Own org's clients | Authenticated member | Any org member | Admin only |
| `payments` | Own org's payments | System (auto-created on payment) | Admin only | Admin only |

If using Postgres Row Level Security (RLS):
```sql
-- Example RLS policy for invoices
ALTER TABLE invoices ENABLE ROW LEVEL SECURITY;

CREATE POLICY invoices_org_isolation ON invoices
  USING (org_id = current_setting('app.current_org_id')::uuid);
```

---

### 6. Database Functions & Triggers

Document computed fields, auto-update triggers, and stored procedures:

```sql
-- Auto-update updated_at on row change
CREATE OR REPLACE FUNCTION set_updated_at()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Apply to all tables with updated_at
CREATE TRIGGER set_users_updated_at
  BEFORE UPDATE ON users
  FOR EACH ROW EXECUTE FUNCTION set_updated_at();
```

List all triggers and what they do. No surprise side effects.

---

### 7. API Request / Response Contracts

The full data shapes for each API endpoint.

**Convention**: All monetary values in **cents (integer)**. All dates in **ISO 8601 UTC**. Pagination via cursor (not offset) for large tables.

**Authentication Endpoints:**

```typescript
// POST /auth/register
Request: {
  email: string;       // max 255 chars
  password: string;    // min 8 chars
  fullName: string;    // max 255 chars
  orgName?: string;    // creates a new org; or invite token to join existing
}
Response 201: {
  user: {
    id: string;
    email: string;
    fullName: string;
    role: UserRole;
  };
  token: string;       // JWT, 7-day expiry
  refreshToken: string;
}
Errors: 400 email_already_exists | 400 weak_password | 422 validation_error
```

**Invoice Endpoints:**

```typescript
// GET /invoices
Query: {
  status?: InvoiceStatus;
  clientId?: string;
  after?: string;      // cursor for pagination
  limit?: number;      // default 20, max 100
  sortBy?: 'createdAt' | 'dueDate' | 'total';
  order?: 'asc' | 'desc';
}
Response 200: {
  data: InvoiceSummary[];  // see type below
  pagination: {
    nextCursor: string | null;
    hasMore: boolean;
    total: number;
  };
}

type InvoiceSummary = {
  id: string;
  invoiceNumber: string;
  status: InvoiceStatus;
  client: { id: string; name: string };
  total: number;       // in cents
  currency: string;
  issueDate: string;   // ISO 8601
  dueDate: string;     // ISO 8601
  createdAt: string;
};

// POST /invoices
Request: {
  clientId: string;
  issueDate: string;     // ISO 8601 date
  dueDate: string;       // ISO 8601 date, must be >= issueDate
  lineItems: {
    description: string; // max 500 chars
    quantity: number;    // > 0
    unitPrice: number;   // in cents, >= 0
    taxRate: number;     // 0–100
  }[];
  currency: string;      // ISO 4217, default "USD"
  notes?: string;        // max 2000 chars
  paymentTerms?: string; // max 100 chars
}
Response 201: Invoice  // full Invoice object
```

Write contracts for ALL primary endpoints covering at minimum: Auth, primary CRUD, search/filter.

---

### 8. Indexing Strategy

Explain the indexing rationale — don't just list indexes, explain what queries they serve:

| Index | Table | Columns | Type | Purpose |
|---|---|---|---|---|
| `idx_invoices_org_status` | invoices | `(org_id, status)` | B-tree composite | Filter invoices by status within an org — most common list query |
| `idx_invoices_due_date` | invoices | `(due_date)` WHERE `status NOT IN ('paid', 'cancelled')` | Partial B-tree | Overdue invoice detection cron job |
| `idx_invoice_items_invoice` | invoice_items | `(invoice_id)` | B-tree | Join invoices → line items |
| `idx_users_email` | users | `(email)` | Unique B-tree | Login lookup, uniqueness enforcement |

---

### 9. Migration Strategy

**9.1 Migration Tool**: [Flyway / Liquibase / Prisma Migrate / Alembic / custom]

**9.2 Migration Naming Convention**: `V[timestamp]__[snake_case_description].sql`
Example: `V20240115_001__create_invoices_table.sql`

**9.3 Migration Rules**:
- Migrations are one-way. No rollback migrations (use forward fixes instead).
- Additive changes (new columns, tables) are safe to deploy without downtime.
- Destructive changes (dropping columns, changing types) require a multi-step deploy plan.

**9.4 Multi-step Deploy Plan for Breaking Changes**:
```
Step 1 (Deploy A): Add new column nullable, write to both old + new
Step 2 (Data migration): Backfill existing rows
Step 3 (Deploy B): Make column NOT NULL, stop writing to old column
Step 4 (Deploy C): Drop old column
```

**9.5 Seed Data**:
Document seed data required for the app to function (roles, categories, config):
- List what seed data exists
- Which environment it applies to (dev only, staging, prod)
- How it's run (automated on first deploy, manual, etc.)

---

## Schema Anti-Slop Checklist

Before outputting, verify:
- [ ] Every table has all columns with actual types and constraints (not just "id, name, timestamps")
- [ ] All foreign keys specify ON DELETE behavior
- [ ] Soft delete pattern is consistent across the schema (either all use `deleted_at` or none do)
- [ ] Monetary values are stored as integers (cents), not floats — this is documented
- [ ] Every index has an explanation of what query it serves
- [ ] RLS or authorization rules are documented for every table
- [ ] API contracts show request types, response types, AND all error codes
- [ ] Enum values are listed with their meaning (not just the name)
- [ ] Migration strategy addresses how to handle breaking changes safely
