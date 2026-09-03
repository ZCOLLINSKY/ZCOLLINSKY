# Zach Collins

Founder-operator. I run a lighting installation company in Lexington, Kentucky, and I built the software that runs it: LightDeck, a vertical SaaS on Supabase Postgres, Vercel, and Stripe. It runs in production as a closed free beta, and it is the system my own company invoices real jobs through.

## What I built

**LightDeck** is a field, proposal, and invoicing workspace for landscape lighting contractors. It started as the tool I needed to stop spending my evenings on proposals, then became the product. Postgres on Supabase is the system of record. The architecture record draws one line: a model may propose copy, classifications, placement suggestions, or pixels, and it never owns money, identity, legal terms, or project state. LightDeck compiles the deliverable and records which facts and design produced it.

I ship it solo. Fleets of Claude Code and Codex agents do the typing. A CI gate on runners I own does the judging. Audit waves re-run the gate on the integrated head and trace every failure to a root cause before anything merges.

## Postgres patterns from the production schema

The SQL is published in [supabase-production-patterns](https://github.com/ZCOLLINSKY/supabase-production-patterns). The private repo tracks application to production per file in a ledger, and where a file is authored but not yet applied, its header says so.

- **Durable rate limiting in Postgres.** A `charge_ai_usage` RPC does an atomic upsert-and-check per account per day. Serverless functions have no shared memory, so the ledger lives in the database, and production fails closed when the ledger is unreachable.
- **Idempotent render attempts.** A compare-and-swap attempt store keyed by a SHA-256 of tenant plus attempt id, carrying a separate fingerprint hash so a retry that quietly changed its request is refused instead of handed the earlier artifact. Seven-day retention purged by a `SECURITY DEFINER` function. The render lane fails closed when that store is unreachable, which is how I found the ambiguous-column bug that has it raising 42702 and returning 503 for every night render. The fix is written and still unapplied; applying and verifying it is an open go-live gate.
- **Webhook ownership.** Stripe events are claimed with a bounded lease before any side effect runs. Paid-invoice effects sit in a row-embedded outbox written by the same statement that marks the invoice paid, with per-channel completion, so a crash between "paid" and "receipt sent" is recoverable, not lost.
- **Append-only receipts.** Render and presentation receipts are hash-bound rows with a mutation-rejecting trigger. No image bytes, no client identity, only provenance, for every AI render a homeowner sees.
- **One-way state.** Onboarding is a `CHECK`-constrained state machine advanced only by server-owned events. A proposal's source job id is immutable after first binding, enforced by trigger, so a stale callback cannot rewrite the wrong client document.
- **Deny by default, and proven.** RLS enabled and forced, browser roles revoked at the grant layer too. A probe on 2026-09-03 with the public anon key against six PII tables returned 401, with row security enabled on 37 of 37 public tables. An event trigger extends the same lockdown to any table nobody has written yet.
- **One outbound gate.** Halt rows, send caps, dedupe keys, and actor attribution share one ledger. Before it existed, the only way to stop an agent emailing homeowners at 2am was pulling the deployment or the email key. The global halt is an env flag so it works when the database does not.
- **Backups in two layers, and the runbook says which one is proven.** A daily logical export to a private store, ordered and count-checked per table, failing closed on drift, wired for a dead-man's-switch ping so a cron that stops running can page me instead of going quiet. The runbook is explicit that a logical export is not a single MVCC point in time, that PITR is not on yet, and that no restore has been rehearsed.

## How I work

- Agent fleets do the typing. A gate on four self-hosted runners does the judging on every PR to the production branch: contract tests, money-path tests, desktop visual baselines at zero retries, and a repository health check.
- A release is not live until the merged SHA, a deployment receipt, `/api/release-info` reporting that exact SHA, and a live smoke suite all agree.
- Tests have to mean what they say. The shared fixture fails any spec that leaves a real API call unmocked, so a green run cannot certify a stub.
- Runbooks exist for the bad nights: incident rollback, secret rotation, and a production migration go/no-go list.
- A hardening audit this week shipped as three gated PRs in one day: cohort and sign-in, then the money path and render economics, then observability, the migration ledger, onboarding edges, and runbooks.
- The architecture and decision records are public in [lightdeck-architecture](https://github.com/ZCOLLINSKY/lightdeck-architecture).

## Numbers

Counts from the private repository at the current production release. Each one is a single command against the tree.

| Metric | Value |
|---|---|
| Commits, first commit to current production release | 2,785 in 98 days |
| Serverless API routes | 46 |
| SQL migration files / distinct Postgres functions | 63 / 43 |
| Playwright spec files (regression 340, e2e 113, mobile 9, a11y 3, adversarial 2, visual 1, integration 1, seed 1) | 470 |
| Self-hosted CI runners | 4 |

## Stack

- Postgres on Supabase: RLS forced, RPCs, row and event triggers. No Supabase Auth, Edge Functions, or Storage in this build.
- Vercel serverless functions and cron, Vercel Blob for images and private backups.
- Stripe invoices, subscriptions, and webhooks.
- Anthropic and OpenAI for vision, verification, and generation, behind the ownership boundary above.
- Resend, Twilio, Google Maps, Sentry, Telegram alerts, Healthchecks.
- Playwright and GitHub Actions on self-hosted macOS runners.

Outside this repo, Python runs the rest of the business: a back-office assistant with a Telegram bot, a Notion CRM worker, and schedulers on a VPS I manage over Tailscale and tmux, plus a local LinkedIn studio that publishes through the official API.

## Elsewhere

[LightDeck](https://www.lightdeck.tech) · [Evening Glow](https://www.eveningglowllc.com) · [zachcollins.me](https://zachcollins.me) · [LinkedIn](https://www.linkedin.com/in/zachcollins-ky) · zachcollins8989@gmail.com

Army veteran, University of Kentucky student, Lexington KY.
