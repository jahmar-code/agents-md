# The agent company

A full software organization expressed as **role personas** — self-contained system
prompts that the main Claude Code loop loads *on demand* when a task falls inside one
discipline. They are **not** `.claude/agents/` subagents: nothing auto-discovers or
auto-spawns them. Your project's `CLAUDE.md` is the router — a **"Specialist agents"**
section there points the working agent at the right file, which it then reads and
adopts for the duration of the task.

23 roles across 6 departments, plus a leadership layer that coordinates them.

## Reference stack

Each persona is written against one concrete stack so its advice stays sharp rather
than generic role-play — but the *principles* are portable (translate the idioms for a
different stack; the DDD, WCAG, STRIDE, SRE, and DORA content doesn't depend on the
framework):

- **Next.js** (App Router, RSC, Server Actions) + **TypeScript**
- **Tailwind** + **shadcn/ui** (Radix) · **Postgres**/**Supabase** · **Drizzle** · **Zod**
- **Multi-tenant SaaS** — tenant model `org → workspace`; money in integer cents;
  timestamps UTC (`timestamptz`); soft-deletes
- **GitHub Actions** CI; serverless/edge or container deploys

## The org

### Leadership & coordination
| Persona | File | Owns |
|---|---|---|
| **Tech Lead** (orchestrator) | [`tech-lead.md`](tech-lead.md) | Decomposes a request, routes to specialists, sequences them, integrates their output |
| **Engineering Manager** | [`engineering-manager.md`](engineering-manager.md) | Delivery planning, estimation, risk, cadence, quality gates, unblocking |
| **Software Architect** | [`software-architect.md`](software-architect.md) | System design, tech selection, NFRs, boundaries, ADRs, conceptual integrity |

### Product & Design
| Persona | File | Owns |
|---|---|---|
| **Product Manager** | [`product-manager.md`](product-manager.md) | What & why — discovery, prioritization, PRDs, success metrics |
| **UX / Product Designer** | [`ux-designer.md`](ux-designer.md) | Flows, IA, interaction & states, wireframes, microcopy |
| **UX Researcher** | [`ux-researcher.md`](ux-researcher.md) | Research plans, usability testing, interviews, synthesis, personas |
| **Technical Writer** | [`technical-writer.md`](technical-writer.md) | Docs (Diátaxis), API reference, runbooks, changelogs, release notes |

### Engineering
| Persona | File | Owns |
|---|---|---|
| **Backend Engineer** | [`backend-engineer.md`](backend-engineer.md) | Server actions, `src/lib/` domain layer, API routes, DDD |
| **Frontend Engineer** | [`frontend-engineer.md`](frontend-engineer.md) | Components, UI/UX, design system, mobile-first, Core Web Vitals |
| **Database Engineer** | [`database-engineer.md`](database-engineer.md) | Schema, migrations, query correctness & performance, data integrity |
| **Mobile Engineer** | [`mobile-engineer.md`](mobile-engineer.md) | Native iOS/Android + React Native — offline, lifecycle, store review |
| **AI / ML Engineer** | [`ai-ml-engineer.md`](ai-ml-engineer.md) | LLM features — prompts, RAG, agents, evals, guardrails, cost/latency |

### Data & Analytics
| Persona | File | Owns |
|---|---|---|
| **Data Engineer** | [`data-engineer.md`](data-engineer.md) | Pipelines, warehouse/lakehouse, dbt, orchestration, data contracts |
| **Analytics Engineer** | [`analytics-engineer.md`](analytics-engineer.md) | Event tracking, the metrics/semantic layer, dashboards, experiments |

### Quality, Security & Reliability
| Persona | File | Owns |
|---|---|---|
| **QA Tester (SDET)** | [`qa-tester.md`](qa-tester.md) | End-to-end correctness, bug hunting, test specs |
| **Code Reviewer** | [`code-reviewer.md`](code-reviewer.md) | Reviewing a diff for correctness, design, maintainability |
| **Security & Pen Tester** | [`security-pentester.md`](security-pentester.md) | Authz, tenant isolation, tokens, input boundaries, threat review |
| **DevOps / Platform** | [`devops-platform.md`](devops-platform.md) | CI/CD, IaC, config/secrets, observability wiring, releases plumbing |
| **SRE** | [`sre.md`](sre.md) | SLOs, error budgets, on-call, incident response, toil, DR |
| **Performance Engineer** | [`performance-engineer.md`](performance-engineer.md) | Measure-first profiling, load testing, tail latency, capacity |
| **Accessibility Specialist** | [`accessibility-specialist.md`](accessibility-specialist.md) | Audit-level WCAG 2.2, AT testing, legal conformance, remediation |

### Delivery & Ops
| Persona | File | Owns |
|---|---|---|
| **Project Manager** | [`project-manager.md`](project-manager.md) | Process, ceremonies, schedule, stakeholders, scope-change control |
| **Release Manager** | [`release-manager.md`](release-manager.md) | Release strategy, go/no-go, versioning, rollout & rollback |

## How the company coordinates

For a single-discipline task, `CLAUDE.md` routes straight to one specialist. For a
cross-cutting request, load the **Tech Lead** first — it decomposes the work, decides
which specialists to load and in what order, defines the handoff contract between them
(each persona's *Definition of done* is the next one's *Definition of ready*), and
integrates the results. The canonical flow:

```
product-manager / software-architect   →  what & why, the design
        ↓
database-engineer  →  backend-engineer  →  frontend-engineer / mobile-engineer
        ↓
qa-tester  →  code-reviewer  →  security-pentester
        ↓
devops-platform / release-manager  →  sre  (observe in production)
```

Parallelize what has no dependency; the schema lands before the action that uses it.

### The three coordination roles are NOT the same — keep them distinct
- **Tech Lead** — *technical* decomposition + routing + integration. "Who does what, technically, in what order."
- **Engineering Manager** — delivery, estimation, risk, cadence, quality gates. "Is it on track, what's blocked, what's the risk."
- **Project Manager** — process, ceremonies, schedule, stakeholders, scope-change. "Process and stakeholders."

## Grounded in industry best practice

Each persona distills authoritative literature into concrete operating rules, cited
inline where load-bearing — a sample: DDD (Evans/Vernon), *DDIA* (Kleppmann),
Use-The-Index-Luke (Winand), Core Web Vitals + WCAG 2.2 (W3C WAI, Deque, WebAIM),
Refactoring UI + NN/g + Norman (design), Cagan/Torres/JTBD (product), Diátaxis (docs),
OWASP/ASVS/STRIDE (security), Google SRE + *Accelerate*/DORA (reliability & delivery),
Kimball + dbt + Kohavi (data & experiments), Brooks + Team Topologies (coordination),
Nygard ADRs + C4 + evolutionary architecture (architecture).

## Adopting these in a project

1. **Add a "Specialist agents" section to your `CLAUDE.md`** — a routing table (like
   the org above) telling the working agent which file to read for which task, and
   whether to load the **Tech Lead** first for cross-cutting work.
2. **Fill in the project-specifics the personas defer to** — they assume `CLAUDE.md`
   supplies the concrete grounding they reference generically: your critical rules, the
   real design tokens, actual module names, the domain's ubiquitous language, and the
   shared files that need coordination. The persona sharpens *how* to work; `CLAUDE.md`
   supplies *what this codebase is*.
3. **Different stack?** Translate the idioms in place, or let `CLAUDE.md` note the
   substitutions (e.g. "Prisma, not Drizzle" / "Remix, not Next").

## Rules for every persona

- **`CLAUDE.md` always wins.** A persona sharpens focus; it never overrides a critical
  rule or your architecture standards.
- **Stay in your lane.** When work drifts into another discipline, stop and load that
  persona or hand off by name — don't freelance across shared files (`schema.ts`,
  `validators.ts`, middleware, root layout, CI/IaC config) without the coordination
  they require.
- **Report back in the persona's "Definition of done" format** so the outcome is
  verifiable, not asserted — and so the next persona in the chain can pick it up.
