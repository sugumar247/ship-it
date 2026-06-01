# Application Flow Document — Reference Template

## When to load this file
Load when the user runs `/appflow` or `/app-flow`.

---

## Document Header (always include)

```
# [Project Name] — Application Flow Document
Version: 1.0 | Status: Draft | Date: [Date]
Author: [Name] | UX Reference: [UX Doc Version/Link]
```

---

## What this document covers

The Appflow document is the **blueprint of how the application behaves** at runtime:
- How screens connect and transition
- What state the app holds and how it changes
- How data moves from user action → API → UI update
- What happens at every branch point (success, error, empty, loading)

This is NOT a design document (see UX doc) and NOT a data schema (see Backend Schema doc).
It IS the source of truth for developers implementing navigation and state logic.

---

## Section Definitions

---

### 1. Application Architecture Overview

**1.1 App Type**
- Single Page Application (SPA)? Native mobile? Hybrid? Server-rendered? Desktop?
- State management approach: [Redux / Zustand / Context API / Jotai / MobX / none]
- Routing library: [React Router / Next.js App Router / Expo Router / other]

**1.2 Core State Model**
What are the top-level pieces of state the app holds?

List each state slice:
- **[Slice Name]**: What it contains, who uses it, when it's reset

Example:
- **auth**: `{ user: User | null, token: string | null, isLoading: boolean }` — app-wide
- **invoices**: `{ list: Invoice[], selected: Invoice | null, filters: FilterState }` — invoices section
- **ui**: `{ sidebarOpen: boolean, activeModal: ModalId | null, toasts: Toast[] }` — global UI state

**1.3 Auth State Machine**
Define the application's authentication states and the transitions between them:

```
[Unauthenticated] ──login success──→ [Authenticated]
[Authenticated]   ──token expired──→ [Unauthenticated]
[Authenticated]   ──logout──────────→ [Unauthenticated]
[Any]             ──app launch────→ [Initializing]
[Initializing]    ──token valid───→ [Authenticated]
[Initializing]    ──no token──────→ [Unauthenticated]
```

What is the default screen for each auth state?
What happens when a protected route is accessed while unauthenticated?

---

### 2. Screen & Route Inventory

List every screen in the application. For each:

| Route | Screen Name | Auth Required | Role Required | Default State |
|---|---|---|---|---|
| `/` | Redirect to /dashboard or /login | — | — | — |
| `/login` | Login | No | — | Empty form |
| `/register` | Register | No | — | Empty form |
| `/dashboard` | Dashboard | Yes | Any | Loaded |
| `/invoices` | Invoice List | Yes | Any | Loading |
| `/invoices/new` | New Invoice | Yes | Any | Empty form |
| `/invoices/:id` | Invoice Detail | Yes | Any | Loading |
| `/invoices/:id/edit` | Edit Invoice | Yes | Owner/Admin | Loading |
| `/settings` | Settings | Yes | Any | Loaded |

Add all screens. Include modals and drawers as separate entries if they have their own state.

---

### 3. Navigation Flow Maps

For each major section of the app, document the navigation graph.

**3.1 Onboarding Flow**

```
App Launch
  └─ Check auth token
      ├─ Valid token → Dashboard
      └─ No token → Login Screen
           ├─ Login success → Dashboard
           ├─ "Register" → Register Screen
           │     ├─ Register success → Onboarding Step 1
           │     │     → Onboarding Step 2
           │     │     → Onboarding Step 3
           │     │     → Dashboard (first time)
           │     └─ Error → Register Screen (with error)
           └─ "Forgot Password" → Reset Password Screen
                 ├─ Email sent → Confirmation Screen
                 └─ Error → Reset Password Screen (with error)
```

**3.2 [Primary Feature] Flow**
*Create one flow map per major feature area.*

Use the same ASCII tree format. At each decision point, show all branches including error paths.

---

### 4. User Flow Specifications

For each major user journey, document the complete technical flow:

**Flow: [Flow Name]** (e.g., Create Invoice)

**Preconditions**: [What must be true for this flow to be accessible]
**Trigger**: [User action or system event that starts this flow]

| # | User Action | System Response | State Change | UI Update |
|---|---|---|---|---|
| 1 | Click "New Invoice" | Navigate to `/invoices/new` | `selectedInvoice = null` | Form renders empty |
| 2 | Select client from dropdown | Fetch client details `GET /clients/:id` | `form.client = clientData` | Client fields auto-fill |
| 3 | Add line item | Local calculation | `form.lineItems.push(item)` | Total updates in real time |
| 4 | Click "Save Draft" | `POST /invoices` with `status: 'draft'` | `invoices.list.unshift(newInvoice)` | Toast: "Draft saved", redirect to `/invoices/:id` |
| 4a | [Error: Network] | Request fails | No state change | Toast: "Couldn't save. Try again." |
| 5 | Click "Send Now" | Opens send confirmation modal | `ui.activeModal = 'sendInvoice'` | Modal appears with preview |
| 6 | Confirm send | `PATCH /invoices/:id { status: 'sent' }` | `selectedInvoice.status = 'sent'` | Status badge updates, modal closes |

**Post-conditions**: [State of the system after this flow completes successfully]
**Failure paths**: [List each failure point and the recovery path]

---

### 5. State Management Architecture

**5.1 State Update Flow**

For all data-mutating actions, describe the update pattern:

```
User action
  → Optimistic UI update (if applicable)
  → API call dispatched
  → Success: confirm/sync state with server response
  → Failure: revert optimistic update + show error
```

Which operations use optimistic updates? (Typically: like/unlike, reorder, simple status changes)
Which operations are pessimistic? (Typically: financial actions, deletions, anything irreversible)

**5.2 Cache & Refresh Strategy**

For each major data type:
- **[Data Type]**: Cached? TTL? When is it invalidated? Polling or real-time?

Example:
- **Invoice list**: Cached in memory. Invalidated on create/update/delete. No polling.
- **Notifications**: Polled every 30s OR WebSocket push (specify).
- **User profile**: Cached. Invalidated on settings save. No polling.

**5.3 Real-time Updates** (if applicable)
- What uses WebSockets / SSE / polling?
- What events are pushed to the client?
- How does the app reconnect after disconnect?
- Race condition handling: what if a push arrives while a local mutation is in flight?

---

### 6. Modal & Overlay System

Document every modal, drawer, and dialog in the app:

| Modal ID | Trigger | Contains | Can be dismissed? | Actions |
|---|---|---|---|---|
| `confirmDelete` | Delete button click | Warning message + item name | No (must choose) | Confirm Delete / Cancel |
| `sendInvoice` | "Send" button | Invoice preview + email input | Yes (backdrop click) | Send / Cancel |
| `addClient` | "New client" in dropdown | Client creation form | Yes | Save / Cancel |

For each modal: what happens to background content? What is the focus trap behavior? What is the animation?

---

### 7. Error & Edge Case Handling

**7.1 Global Error States**

| Error Type | When it occurs | What the user sees | Recovery action |
|---|---|---|---|
| Network offline | No connectivity | Offline banner at top | Auto-dismiss when reconnected |
| 401 Unauthorized | Token expired mid-session | Redirect to login + "Session expired" message | Re-login |
| 403 Forbidden | Accessing restricted resource | Error page: "You don't have access to this" | Link to dashboard |
| 404 Not Found | Invalid URL | Error page: "Page not found" | Link to dashboard |
| 500 Server Error | Server-side failure | Error page with retry option | Retry button |

**7.2 Form Validation States**
- When does validation run? (on-blur, on-change, on-submit)
- Which fields have async validation? (e.g., email uniqueness check)
- What is the debounce delay for async validation?
- How are validation errors displayed? (inline / top-of-form / toast)

**7.3 Empty States**

For each list or content area, document the empty state:

| Screen / Section | Empty state message | CTA |
|---|---|---|
| Invoice list | "No invoices yet. Create your first one." | "Create Invoice" button |
| Client list | "Add your first client to start invoicing." | "Add Client" button |
| Search results | "No results for '[query]'. Try different keywords." | Clear search button |
| Notifications | "You're all caught up!" | — |

---

### 8. Deep Linking & URL Strategy

**8.1 URL Parameters**
For each route with parameters, define:
- What each param represents
- Valid values / format
- What happens with invalid values (redirect? error page?)

**8.2 Query Parameters**
Which screens use query params for state? (filters, sort, pagination, search)
Are query params preserved across navigation? Shareable links?

**8.3 Deep Link Handling** (mobile apps)
- What deep links does the app support? (e.g., `app://invoices/123`)
- How are deep links handled when the app is closed?
- How are universal links / app links configured?

---

### 9. Performance-Critical Flows

Identify flows where performance matters most and document the optimization strategy:

| Flow | Why it's critical | Optimization strategy |
|---|---|---|
| App initial load | First impression | Code splitting, lazy loading non-critical routes |
| List screen render | Power users may have 1000+ items | Virtual scroll, cursor pagination |
| Search/filter | Should feel instant | Debounce 200ms, local filter for small datasets |
| Form submission | Users wait for confirmation | Optimistic update, disable button on submit |

---

## Appflow Anti-Slop Checklist

Before outputting, verify:
- [ ] Every route is in the screen inventory
- [ ] Auth states and transitions are fully documented (not just "user logs in")
- [ ] Every flow has error path branches, not just happy paths
- [ ] State changes are specified for every action (what changes in the store)
- [ ] Empty states are defined for every list/content area
- [ ] Real-time behavior (if any) specifies reconnection and race condition strategy
- [ ] Modal behaviors include dismiss rules and focus management
- [ ] No screen is described as "similar to X" without specifying the difference
