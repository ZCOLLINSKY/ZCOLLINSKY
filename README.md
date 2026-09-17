# Zach Collins

**Founder & developer of [LightDeck](https://www.lightdeck.tech/) · Owner of [Evening Glow](https://www.eveningglowllc.com/) · U.S. Army veteran**

I build software for landscape-lighting contractors, informed by running a lighting business in Central Kentucky. My work connects JavaScript applications, serverless APIs, Postgres, and AI workflows with explicit review boundaries.

## LightDeck

LightDeck brings property photos, fixture plans, contractor-set pricing, and proposal preparation into one workflow. I work across product requirements, application logic, database design, automated testing, and release verification.

**Current product scope, September 2026:** a closed, free, founder-supported beta for field capture and proposal, PDF, and invoice drafts. Client publishing, messages, signatures, payments, and generated night concepts are not enabled for beta walkthroughs.

[Product](https://www.lightdeck.tech/) · [Founder site](https://zachcollins.me/) · [Architecture](https://github.com/ZCOLLINSKY/lightdeck-architecture) · [Postgres patterns](https://github.com/ZCOLLINSKY/supabase-production-patterns)

## Engineering work you can inspect

| Problem | Approach | Public evidence |
| --- | --- | --- |
| Serverless instances need a shared usage limit | An atomic Postgres ledger records usage and checks the account limit | [Rate-limit RPC](https://github.com/ZCOLLINSKY/supabase-production-patterns/blob/main/sql/2026-07-16-durable-rate-limit-ledger.sql) |
| Webhooks can arrive concurrently or be retried | Bounded event claims and durable completion state | [Webhook claims](https://github.com/ZCOLLINSKY/supabase-production-patterns/blob/main/sql/2026-07-16-stripe-event-claims.sql) |
| A crash can happen after state is committed | An outbox records pending effects alongside the state change | [Outbox pattern](https://github.com/ZCOLLINSKY/supabase-production-patterns/blob/main/sql/2026-07-15-invoice-effect-outbox.sql) |
| AI output must not become business truth by accident | Structured source facts, constrained model proposals, validation, and human review | [AI architecture decisions](https://github.com/ZCOLLINSKY/lightdeck-architecture/blob/main/docs/ai-runtime-architecture.md) |
| Browser roles should not directly access application tables | Explicit grants, row-level security, and database constraints | [Database access boundaries](https://github.com/ZCOLLINSKY/supabase-production-patterns/blob/main/sql/2026-07-16-public-schema-lockdown.sql) |

These public repositories are dated engineering snapshots published September 3, 2026. Their source headers and notes distinguish implemented patterns, design work, and unverified deployment state. They are not a claim that every described capability is enabled in the current beta.

## Stack and working approach

- **Application:** JavaScript, browser workflows, Vercel serverless functions.
- **Data:** Postgres on Supabase, SQL functions, constraints, indexes, and tenant-scoped data access.
- **Quality:** Playwright, API and contract tests, GitHub Actions, and release verification.
- **Applied AI:** structured context, model-output validation, human review, usage limits, and provenance.

I use AI development tools throughout the build process and take responsibility for product decisions, architecture, review, and validation. I’m particularly interested in reliable AI workflows and vertical SaaS grounded in real operational problems.

Running Evening Glow keeps me close to those problems: site visits, design decisions, pricing, installation, and customer handoffs. My background also includes Army service, logistics, and operations.

## Connect

[LinkedIn](https://www.linkedin.com/in/zachcollins-ky/) · [zachcollins.me](https://zachcollins.me/) · [LightDeck](https://www.lightdeck.tech/)

Lexington, Kentucky. Open to conversations about vertical SaaS, applied AI, and software for service businesses.
