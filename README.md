# Zach Collins

Founder-operator. I run a lighting installation company in Central Kentucky and I built the software that runs it: a vertical SaaS on Supabase Postgres, Vercel, and Stripe, in production, with paying invoices flowing through it.

## What I'm building

**LightDeck** is a field, proposal, and invoicing workspace for landscape lighting contractors. It started as the tool I needed to stop losing three hours per proposal, then became the product. The system of record is Postgres on Supabase. AI providers are replaceable workers behind a typed contract: a model can propose copy, classifications, or pixels, but it never owns money, identity, legal terms, or project state. LightDeck compiles the deliverable and proves which facts and design produced it.

I ship it solo. The pace comes from running fleets of Claude Code and Codex agents against a hard CI gate, with audit waves that verify what the agents claim.

## Production on Supabase

Patterns that are live in the database today, with the SQL published in [supabase-production-patterns](https://github.com/ZCOLLINSKY/supabase-production-patterns):

- **Durable rate limiting in Postgres.** A `charge_ai_usage` RPC does an atomic upsert-and-check per account per day. Serverless functions have no shared memory, so the ledger lives in the database. Production fails closed when the ledger is unreachable.
- **Idempotent render attempts.** A compare-and-swap attempt store keyed by a SHA-256 fingerprint. Transport retries reuse one attempt id and get the exact prior artifact back. Seven-day retention purged by a SECURITY DEFINER function.
- **Webhook ownership.** Stripe events are claimed with a bounded lease before any side effect runs, and paid-invoice effects sit in a row-embedded outbox with per-channel completion so a crash between "paid" and "receipt sent" is recoverable, not lost.
- **Append-only receipts.** Render and presentation receipts are hash-bound rows with a mutation-rejecting trigger. No image bytes, no client identity, only provenance.
- **One-way state.** Onboarding is a `CHECK`-constrained state machine advanced only by server-owned events. A proposal's source job id is immutable after first binding, enforced by trigger, so stale callbacks cannot rewrite the wrong client document.
- **Deny by default.** RLS enabled and forced on every application table, browser roles revoked at the grant layer too, and an event trigger that locks down any future public object automatically.
- **One outbound gate.** Halt rows, send caps, dedupe keys, and actor attribution share one ledger, because four controls that must agree are cheaper to reason about in one table. The global kill switch is an env flag so it works when the database does not.
- **Backups proven, not assumed.** Managed PITR plus a daily logical export to a private store, count-checked per table, with a heartbeat that pages when the export does not run.

## How I work

- Agent fleets do the typing. A gate on four self-hosted runners does the judging: contract tests, money-path tests, visual baselines, and a repository health check on every PR to the production branch.
- Every "it works" claim needs a receipt. Deploy SHA, migration state, API health, and a real browser journey are checked before a release is called live.
- Runbooks exist for the bad nights: incident rollback, secret rotation, and production migration with a go/no-go list.
- The architecture and decision records are public in [lightdeck-architecture](https://github.com/ZCOLLINSKY/lightdeck-architecture).

## Numbers

| | |
|---|---|
| Commits, first commit to current production release | 2,785 in 98 days |
| Serverless API routes | 46 |
| SQL migrations / Postgres functions | 63 / 50 |
| Playwright test files (regression, e2e, visual, mobile, a11y, adversarial) | 517 |
| Self-hosted CI runners | 4 |
| First real invoice paid through the app | 40 days after the first commit |

## Stack

Postgres on Supabase (RLS forced, RPCs, triggers; no Supabase Auth, Edge Functions, or Storage in this build), Vercel serverless functions, Stripe (invoices, subscriptions, webhooks), Anthropic and OpenAI (vision, verification, generation), Resend, Twilio, Vercel Blob, Sentry, Playwright, GitHub Actions.

The rest of the business runs on Python I wrote: a back-office assistant with a Telegram bot, a Notion CRM worker, and schedulers on a VPS I manage over Tailscale and tmux, plus a local LinkedIn publishing studio that posts through the official API.

## Elsewhere

[LightDeck](https://www.lightdeck.tech) · [Evening Glow](https://www.eveningglowllc.com) · [zachcollins.me](https://zachcollins.me) · [LinkedIn](https://www.linkedin.com/in/zachcollins-ky) · zachcollins8989@gmail.com

Army veteran, University of Kentucky student, Lexington KY.
