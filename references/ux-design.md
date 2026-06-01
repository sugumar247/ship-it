# UI/UX Design Document — Reference Template

## When to load this file
Load when the user runs `/ux` or `/ui-ux-design`.

---

## Document Header (always include)

```
# [Project Name] — UI/UX Design Document
Version: 1.0 | Status: Draft | Date: [Date]
Designer: [Name] | PRD Reference: [PRD Version/Link]
```

---

## Section Definitions

---

### 1. Design Vision & Principles

**1.1 Design Vision Statement**
One sentence. What is the design experience this product should deliver?

Example: "A tool so fast and obvious that users never need documentation."
Not: "A clean, intuitive, user-friendly interface."

**1.2 Design Principles** (3–5 principles)

For each principle:
- **[Principle Name]**: [One-line statement]
- **What this means in practice**: [2–3 concrete examples of how this shapes decisions]
- **What to sacrifice for this**: [What you'd give up to uphold this principle]

Example:
- **Speed over beauty**: Every interaction should complete in under 200ms. We'll use optimistic UI updates even when that requires more complex code.

---

### 2. User Personas Summary

*(Reference PRD personas — summarize or repeat the 2–3 personas here)*

For each persona add the design-specific lens:
- **Visual literacy**: Can they handle data-dense interfaces? Or need progressive disclosure?
- **Device context**: Desktop at desk? Phone on the go? Both?
- **Interaction patterns**: Power keyboard user? Touch-first? Mouse-heavy?
- **Accessibility needs**: Any known or likely accessibility considerations for this user group?

---

### 3. Information Architecture

**3.1 Site/App Map**

Describe the full navigation structure as a hierarchy. Use text outline or ASCII tree:

```
/ (Root)
├── /auth
│   ├── /login
│   ├── /register
│   └── /reset-password
├── /dashboard (authenticated)
├── /invoices
│   ├── /invoices/new
│   ├── /invoices/:id
│   └── /invoices/:id/edit
├── /clients
└── /settings
    ├── /settings/profile
    └── /settings/billing
```

**3.2 Navigation Model**
What is the primary navigation pattern?
- Sidebar (persistent, collapsible?)
- Top nav (horizontal tabs?)
- Bottom nav (mobile tab bar?)
- Contextual (changes per section?)
- Command palette (power users?)

Justify the choice based on the number of sections, user mental model, and device priority.

**3.3 Content Hierarchy**
For each primary page, what is the visual hierarchy of information? What does the user see first, second, third? What is the primary CTA?

---

### 4. Key Screen Specifications

For each major screen (cover all primary screens in the app):

**Screen: [Screen Name]** (e.g., Invoice List)

- **URL/Route**: `/invoices`
- **Purpose**: What does the user accomplish here?
- **Entry points**: How does the user get here?
- **Exit points**: Where do they go from here?
- **Primary action**: The #1 thing we want the user to do
- **Secondary actions**: Other available actions
- **Empty state**: What does the user see when there's no data?
- **Loading state**: How does loading display? (skeleton, spinner, progressive?)
- **Error state**: What does the user see if data fails to load?
- **Layout description**: Describe the layout in prose — what components, in what arrangement, at what sizes

Cover minimum: Dashboard/Home, Primary list view, Detail/edit view, Onboarding/welcome, Settings, Key modal/dialog.

---

### 5. User Flows

Document each major user flow as a sequence. Format:

**Flow: [Flow Name]** (e.g., Create and Send Invoice)
**Trigger**: [What starts this flow]
**Success condition**: [What completing this flow achieves]

```
Step 1: User clicks "New Invoice" → Invoice creation form opens
Step 2: User fills in client name → Autocomplete shows existing clients
  → If client exists: select from list
  → If client new: inline "Add client" appears
Step 3: User adds line items → Running total updates in real time
Step 4: User clicks "Save Draft" → Saved, returns to invoice list
  → OR clicks "Send Now" → Email preview modal opens
Step 5: User confirms send → Invoice emailed, status changes to "Sent"
  → Confirmation toast: "Invoice #042 sent to client@email.com"
```

Cover: Onboarding flow, Core value action (the main thing users do), Error recovery flow, Account/settings management.

---

### 6. Component Design System

Define the design language. Be specific enough for a developer to implement.

**6.1 Color Palette**

| Token | Value (Hex) | Usage |
|---|---|---|
| `color-primary` | #[value] | Primary buttons, links, active states |
| `color-primary-hover` | #[value] | Hover state of primary |
| `color-secondary` | #[value] | Secondary actions |
| `color-success` | #[value] | Success states, confirmations |
| `color-warning` | #[value] | Warnings, pending states |
| `color-error` | #[value] | Errors, destructive actions |
| `color-text-primary` | #[value] | Body text |
| `color-text-secondary` | #[value] | Muted text, labels |
| `color-bg` | #[value] | Page background |
| `color-surface` | #[value] | Card/panel background |
| `color-border` | #[value] | Dividers, input borders |

**6.2 Typography**

| Token | Font | Size | Weight | Line Height | Usage |
|---|---|---|---|---|---|
| `text-h1` | [Font] | 32px | 700 | 1.2 | Page titles |
| `text-h2` | [Font] | 24px | 600 | 1.3 | Section headers |
| `text-h3` | [Font] | 18px | 600 | 1.4 | Card titles |
| `text-body` | [Font] | 14px | 400 | 1.6 | Body text |
| `text-small` | [Font] | 12px | 400 | 1.5 | Labels, captions |
| `text-code` | [Mono font] | 13px | 400 | 1.5 | Code, IDs |

**6.3 Spacing System**
Base unit: [4px / 8px]. Scale: [4, 8, 12, 16, 24, 32, 48, 64px]

**6.4 Border Radius**
- Small: [4px] — inputs, tags
- Medium: [8px] — cards, buttons
- Large: [12px] — modals, dialogs
- Full: [9999px] — pills, avatars

**6.5 Elevation / Shadow**

| Level | CSS | Usage |
|---|---|---|
| 0 | none | Flat surfaces |
| 1 | `0 1px 3px rgba(0,0,0,0.1)` | Cards |
| 2 | `0 4px 12px rgba(0,0,0,0.12)` | Dropdowns |
| 3 | `0 8px 24px rgba(0,0,0,0.15)` | Modals |

**6.6 Core Component Specs**

Document behavior (not just appearance) for:

- **Button**: Primary, Secondary, Ghost, Destructive, Icon-only. States: Default, Hover, Active, Disabled, Loading.
- **Input**: Text, Select, Textarea, Checkbox, Radio, Toggle. States: Default, Focus, Error, Disabled.
- **Table/List**: How are rows shown? Sorting? Selection? Pagination (offset-based or cursor)?
- **Modal/Dialog**: Max width, backdrop behavior (click to close?), animation (slide, fade?), focus trap behavior.
- **Toast/Notification**: Position, duration, types (success, error, info, warning), max stack size.
- **Empty State**: Icon + heading + body + optional CTA. How does this look for each primary list?

---

### 7. Interaction Design

**7.1 Micro-interactions**
List key micro-interactions with their purpose:
- e.g., Button press → subtle scale (0.98) + haptic feedback on mobile → conveys direct manipulation
- e.g., Row hover → background shift → indicates clickable
- e.g., Form save → optimistic UI update → instant feedback before server confirms

**7.2 Animation Principles**
- Duration range: [100–300ms for UI, up to 500ms for transitions]
- Easing: [ease-out for entrances, ease-in for exits, ease-in-out for transformations]
- What never animates: [data loading, error states — no decorative delays on critical paths]

**7.3 Loading States**
- Under 200ms: no loading indicator (feels instant)
- 200ms–1s: skeleton screens (for content areas)
- 1s+: spinner + informative copy ("Loading your invoices...")
- Async background: Progress indicator or notification

**7.4 Form Design**
- Validation: when does validation fire? (on blur, on submit, real-time?)
- Error placement: inline below field (not top-of-form summary only)
- Success feedback: what happens after form submit?
- Autofill: support browser autofill everywhere possible

---

### 8. Responsive Design

**8.1 Breakpoints**

| Name | Min width | Layout behavior |
|---|---|---|
| Mobile | 0px | Single column, bottom nav |
| Tablet | 768px | Two column where appropriate |
| Desktop | 1024px | Sidebar + main content |
| Wide | 1440px | Constrained max-width with side padding |

**8.2 Mobile-specific considerations**
- Touch targets: minimum 44×44px for all interactive elements
- Thumb zone: primary actions within bottom 60% of screen on mobile
- Navigation: [bottom tab bar / hamburger menu / other] on mobile
- Data tables on mobile: [horizontal scroll / card layout / progressive disclosure]

---

### 9. Accessibility Requirements

- **Standard**: WCAG 2.1 AA minimum
- **Color contrast**: 4.5:1 for normal text, 3:1 for large text and UI components
- **Focus management**: All interactive elements keyboard-accessible. Tab order is logical. Focus visible.
- **Screen reader**: All images have alt text. Form labels are associated. ARIA roles where needed.
- **Motion**: `prefers-reduced-motion` respected. Animations can be disabled.
- **Error identification**: Errors communicated via text, not color alone.

---

### 10. Design Handoff Notes

What developers need to know:
- Design system location (if Figma/Storybook exists, link it)
- Component naming conventions
- Which components are custom vs. from a library (e.g., shadcn/ui, Radix, Chakra)
- Icon library used
- Image/asset format requirements
- Font loading strategy (self-hosted vs. Google Fonts)
- Dark mode: supported at launch? Later? Never?

---

## UX Anti-Slop Checklist

Before outputting, verify:
- [ ] Color values are actual hex codes, not "blue" or "primary color"
- [ ] Typography uses specific sizes and weights, not "large" or "bold"
- [ ] Every primary screen has empty state, loading state, and error state defined
- [ ] User flows cover error paths, not just happy paths
- [ ] Accessibility requirements are specific (contrast ratios, not just "accessible")
- [ ] Mobile behavior is explicitly addressed for every major pattern
- [ ] Component states cover all variants (default, hover, active, disabled, loading, error)
