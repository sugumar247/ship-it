# ship-it

A skill for AI coding agents that generates 6 professional project documents — PRD, TRD, UI/UX Design, Appflow, Backend Schema, and Implementation Plan.

Built for the [agent skills ecosystem](https://skills.sh). Works with Claude Code, Gemini CLI, OpenCode, Cursor, Codex, Antigravity, and [50+ other agents](https://github.com/vercel-labs/skills#supported-agents).

---

## Install

```bash
npx skills add sugumar247/ship-it
```

The [skills CLI](https://github.com/vercel-labs/skills) will detect your installed agents and ask which ones to install to. That's it.

**Install globally** (available in every project):
```bash
npx skills add sugumar247/ship-it -g
```

**Install to a specific agent only:**
```bash
npx skills add sugumar247/ship-it -a claude-code
npx skills add sugumar247/ship-it -a gemini-cli
npx skills add sugumar247/ship-it -a opencode
```

---

## Usage

Once installed, use these commands inside any agent chat:

| Command | Document Generated |
|---|---|
| `/ship-it prd` | Product Requirements Document |
| `/ship-it trd` | Technical Requirements Document |
| `/ship-it ux` | UI/UX Design Document |
| `/ship-it appflow` | Application Flow Document |
| `/ship-it schema` | Backend Schema Document |
| `/ship-it impl` | Implementation Plan |
| `/ship-it all` | All 6 documents |
| `/ship-it help` | Show command list |

**Format options** (default is `.md`):
```
/ship-it prd --format docx
/ship-it schema --format html
/ship-it all --format md
```

**Skip the interview by providing context inline:**
```
/ship-it prd MyApp is a SaaS invoicing tool for freelancers using Next.js + Supabase
```

---

## What makes this different

Most AI-generated project docs are generic filler. This skill enforces a quality gate:

- **Context first** — the agent asks for your project specifics before writing anything. No context = no document.
- **Anti-slop rules** — every document has a checklist. Vague statements like "the system should be scalable" are rejected in favor of "support 500 concurrent users at launch, 5,000 by month 6".
- **Real specifics** — color hex codes not "primary blue", actual SQL schemas not `id, name, timestamps`, actual sprint plans not "phase 1, phase 2".
- **Decision flags** — if something is genuinely unknown, the document marks it `⚠️ Decision needed:` instead of making it up.

---

## What's in each document

**PRD** — Executive summary, problem statement, user personas, user stories with acceptance criteria, functional requirements, non-functional requirements (with actual numbers), success KPIs, risks, open questions.

**TRD** — System architecture, technology stack with rationale, API design with request/response schemas and error codes, security model (auth, authz, OWASP), performance targets, ADRs.

**UI/UX Design** — Design principles, screen-by-screen specs (including empty/loading/error states), component design system (hex colors, px sizes, font weights), user flows with error paths, accessibility (WCAG 2.1 AA), responsive breakpoints.

**Appflow** — Route inventory, auth state machine, navigation flow maps for every feature, state management architecture, error handling for every screen, deep linking strategy.

**Backend Schema** — Full SQL/NoSQL schema with all field types and constraints, foreign keys with ON DELETE behavior, indexes with explanations, RLS/authorization rules, complete API request/response contracts, migration strategy.

**Implementation Plan** — Sprint-by-sprint breakdown with tasks, owners, and estimates, critical path analysis, risk register, quality gates between phases, launch checklist, post-launch plan.

---

## File structure

```
ship-it/
├── SKILL.md                    ← Skill entry point: commands, workflow, quality rules
├── README.md                   ← This file
└── references/
    ├── prd.md                  ← PRD section definitions + anti-slop checklist
    ├── trd.md                  ← TRD section definitions + anti-slop checklist
    ├── ux-design.md            ← UX Design section definitions + anti-slop checklist
    ├── appflow.md              ← Appflow section definitions + anti-slop checklist
    ├── backend-schema.md       ← Schema section definitions + anti-slop checklist
    └── implementation-plan.md  ← Impl Plan section definitions + anti-slop checklist
```

The agent reads `SKILL.md` for routing and workflow, then loads the relevant `references/` file before generating each document.

---

## Manual install (without the CLI)

If you prefer not to use `npx skills`:

```bash
# Claude Code
git clone https://github.com/sugumar247/ship-it ~/.claude/skills/ship-it

# Gemini CLI
git clone https://github.com/sugumar247/ship-it ~/.gemini/skills/ship-it

# OpenCode
git clone https://github.com/sugumar247/ship-it ~/.config/opencode/skills/ship-it

# Antigravity
git clone https://github.com/sugumar247/ship-it ~/.gemini/antigravity/skills/ship-it
```

---

## Contributing

PRs welcome. To improve a reference template, edit the relevant file in `references/` and add an entry to its anti-slop checklist if you're enforcing a new requirement.

To add a new document type: add a command to `SKILL.md`, create `references/your-doc.md`, update the routing table, and update this README.

---

## License

MIT
