---
name: ship-it
description: >
  Generate professional, production-grade project documentation across all 6 pillars:
  PRD (Product Requirements Document), TRD (Technical Requirements Document), UI/UX Design,
  Appflow, Backend Schema, and Implementation Plan. Use this skill whenever the user says
  /ship-it prd, /ship-it trd, /ship-it ux, /ship-it appflow, /ship-it schema, /ship-it impl,
  /ship-it all, or asks to "create project docs", "write a PRD", "generate technical spec",
  "document my project", "write a product requirements document", "design the backend schema",
  "create an implementation plan", or any variation of professional project documentation.
  Also triggers when user says "I'm building X, help me document it" or "we need docs for
  our project". Works across Claude Desktop, Claude Code, Gemini CLI, OpenCode, and any
  agent with file access.
version: 1.0.0
author: sugumar247
license: MIT
---

# ship-it Skill

Generates 6 professional project documents with real depth — not generic filler.
Every document is grounded in project-specific context gathered before generation.

---

## Commands

| Command | Alias | Document |
|---|---|---|
| `/ship-it prd` | `/ship-it product-requirements` | Product Requirements Document |
| `/ship-it trd` | `/ship-it technical-requirements` | Technical Requirements Document |
| `/ship-it ux` | `/ship-it ui-ux-design` | UI/UX Design Document |
| `/ship-it appflow` | `/ship-it app-flow` | Application Flow Document |
| `/ship-it schema` | `/ship-it backend-schema` | Backend Schema Document |
| `/ship-it impl` | `/ship-it implementation-plan` | Implementation Plan |
| `/ship-it all` | `/ship-it all-docs` | All 6 documents |
| `/ship-it help` | `/ship-it commands` | Show this command list |

**Format flags** (append to any command):
- `--format md` (default) · `--format pdf` · `--format docx` · `--format html` · `--format json`
- `--output <filename>` to set the output filename

**Examples:**
```
/ship-it prd --format docx
/ship-it schema --output db-design.md
/ship-it all --format html
/ship-it trd MyApp is a SaaS invoicing tool for freelancers using Next.js + Supabase
```

---

## Execution Workflow

### Step 1 — Parse the command

1. Extract which document(s) to generate from the command or user message.
2. Check if the user already provided context inline (e.g., `/ship-it prd MyApp is a...`).
3. Determine the output format (default: `.md`).

### Step 2 — Context Discovery

**If sufficient context is NOT already in the conversation**, run the discovery interview.
Do not skip this. A document generated without context is guaranteed to be slop.

Ask the user these questions in a single message (not one at a time):

```
To generate a [DOCUMENT NAME] that's actually useful, I need to understand your project.
Answer as briefly or thoroughly as you like:

1. Project name and one-sentence description
2. Who are the primary users? (role, context, technical level)
3. What core problem does this solve? What's the pain today?
4. What platform(s)? (web app / mobile iOS+Android / API only / desktop / all)
5. Tech stack (or preferences if greenfield): language, framework, database, cloud?
6. Any existing systems this integrates with or replaces?
7. Team size and rough timeline?
8. Any hard constraints? (budget, compliance like HIPAA/GDPR, offline support, etc.)
```

For `/ship-it trd`, `/ship-it schema`, `/ship-it appflow` also ask:
- Expected scale at launch and at 12 months (users, requests/day)
- Authentication model (email/password, OAuth, SSO, magic link, etc.)

For `/ship-it ux` also ask:
- Any existing brand guidelines or design systems?
- Competitors or reference products for visual direction?

For `/ship-it impl` also ask:
- Who is on the team and what are their roles?
- What's already built vs. what's greenfield?

**If context IS already clear from conversation history**, summarize what you understood and ask the user to confirm or correct before generating.

### Step 3 — Load Reference Template

Before generating each document, load the relevant reference file from this skill's `references/` directory:

| Command | Reference File |
|---|---|
| `/ship-it prd` | `references/prd.md` |
| `/ship-it trd` | `references/trd.md` |
| `/ship-it ux` | `references/ux-design.md` |
| `/ship-it appflow` | `references/appflow.md` |
| `/ship-it schema` | `references/backend-schema.md` |
| `/ship-it impl` | `references/implementation-plan.md` |

For `/ship-it all`, load all reference files before generating.

### Step 4 — Generate the Document

Follow the template and quality standards in the reference file exactly.
Write every section with project-specific content — never generic placeholder text.

### Step 5 — Output

- **Default (`.md`)**: Write to `<project-name>-<doc-type>.md` in the current directory.
- **Other formats**: Adapt accordingly. For PDF/DOCX, use available tools. For JSON, output structured data. For HTML, generate a clean, readable page.
- **Present the file** to the user with `present_files` if available, otherwise print the path.
- After output, offer: "Want me to generate any of the other 5 documents next? Try `/ship-it trd`, `/ship-it ux`, or `/ship-it all`"

---

## The Anti-Slop Manifesto

These rules are non-negotiable. Violating them means the document is junk.

### ❌ Never do this
- Write "The system should be scalable" → **what does scalable mean for this project?**
- Write "Users will be able to..." without tying it to a specific persona and scenario
- Use section headers with `[TODO: fill in later]` content
- Copy-paste the same boilerplate across different projects
- Write "This is out of scope" without explaining why or what IS in scope
- Invent features the user didn't mention
- Generate a schema without knowing the actual data relationships
- Write an implementation plan without knowing who's implementing it

### ✅ Always do this
- Use the actual project name, real user personas, real tech stack throughout
- Quantify everything that can be quantified: "p95 < 200ms", "support 500 concurrent users at launch"
- Name the specific pain point: "today, freelancers export CSV from Notion manually and re-enter data into Excel" — not "users have data management challenges"
- Write implementation plans in real sprints with real team roles
- Write schemas with actual field names, types, constraints, and indexes — not `id, name, created_at, ...`
- Flag genuine unknowns explicitly: "⚠️ Decision needed: We don't yet know X. Options are A, B, C."
- Cross-reference between documents when generating multiple (e.g., TRD references PRD user stories by ID)

### Quality Gate

Before outputting any document, mentally run this check:
1. Could this section apply to ANY other project? → If yes, make it more specific.
2. Is every requirement testable / verifiable? → If not, add acceptance criteria.
3. Are there unexplained acronyms or assumed knowledge? → Define them.
4. Does the document tell a coherent story from problem → solution → execution?

---

## Multi-Document Generation (`/ship-it all`)

When generating all 6 documents:

1. Run discovery once (combined questions from all document types).
2. Generate documents in this order (each builds on the previous):
   - PRD → TRD → UI/UX → Appflow → Backend Schema → Implementation Plan
3. Use consistent terminology, IDs, and references across all 6.
4. Output each as a separate file. Also offer a combined `ship-it-bundle.md`.
5. Print a summary table at the end listing all generated files.

---

## Format Output Guide

| Format | How to Handle |
|---|---|
| `.md` | Standard Markdown. Use `##` sections, tables, code blocks for schemas/code. |
| `.pdf` | Generate Markdown first, then use available PDF skill/tool to convert. |
| `.docx` | Generate Markdown first, then use available DOCX skill/tool to convert. |
| `.html` | Generate a clean, self-contained HTML page with inline CSS. No external deps. |
| `.json` | Structured JSON matching the document schema. Include all sections as keys. |

If the user requests PDF/DOCX but the relevant conversion tool isn't available,
output Markdown and tell the user: "I've generated the Markdown version. Use your
available conversion tool or re-run with `--format md`."

---

## Agent Compatibility Notes

This skill is designed to work across all agents without requiring special tooling.

- **Claude Code / Claude Desktop**: Full support. Uses `bash_tool` for file writes, `present_files` to share output.
- **Gemini CLI / OpenCode / other CLI agents**: Falls back to printing document content directly if file tools aren't available. User can then pipe or redirect to a file.
- **Web-only agents (no file tools)**: Renders document as a formatted code block in chat.

If you detect you're in a file-capable environment, always write to disk. If not, print clearly formatted output the user can copy.
