# ADR-004: Always-live hot-path architecture

- **Status:** Accepted
- **Date:** 2026-07-22
- **Deciders:** Ayush Gupta

## Context

Earlier projects died quietly in public. A Hugging Face Space slept; a Supabase
free-tier database auto-paused after inactivity. Months later the links 404'd —
and a dead demo is worse than no demo, because it actively damages credibility
with anyone who clicks it.

ScamVet cannot fail that way. The Vet wing is checked by people who are already
suspicious; the Rescue wing is opened by someone who has just lost money and has
minutes that matter. A spinner, a cold start, or a paused database in that moment
is not a degraded experience — it is the product failing at the only time it
counts.

## Decision

**Nothing pausable is ever on the hot path.**

1. **Hot path (binding):** Next.js PWA on Vercel / Cloudflare Pages → Cloudflare
   Workers API → Cloudflare D1/KV or Turso → Telegram bot as a Workers webhook.
   Every element is edge-served and perpetually free with no sleep behaviour.
2. **Supabase free is banned from the hot path.** Neon is acceptable for
   cold-path/analytical use only. Any Python service on HF Spaces free is
   cold-path only and the hot path must never depend on one.
3. **Liveness is monitored, not assumed:** GitHub Actions cron pings every
   endpoint every 5 minutes, forever, plus a public status page
   (UptimeRobot / Better Stack).
4. **Graceful degradation is a contract, not a fallback.** If the LLM free tiers
   throttle or an upstream feed is down, a local blocklist plus a distilled ONNX
   scorer still returns an instant verdict. Degrade to fast-and-useful, never to
   a spinner. Rescue artifacts generate offline-first where possible.
5. **The standing design test**, applied to every user-facing component before it
   ships: *untouched for 8 months, opened on hotel wifi — is there a working
   product in 3 seconds?* If not, re-architect.

## Consequences

- Component choice is constrained by lifetime class, not convenience. "It's
  easier with X" does not override this; a later decision to reach for a sleeping
  free tier on the hot path is a violation of this ADR, not a trade-off.
- Perpetual free tiers carry everything user-facing. AWS credits carry only the
  documented cloud story, and the product never depends on them — which is what
  makes the planned month-5 migration off AWS a non-event for users.
- Zero cloud hard-coding from day one (Terraform modules, values-driven config)
  so the platform stays portable.
- The degradation path is real work, not a stub: the distilled ONNX scorer and
  the local blocklist are build items, not aspirations.
- Any public URL deliverable is verified logged-out (incognito) before it is
  marked done. Owner-view success is not public-view success.

## Alternatives considered

- **A single conventional backend host (Render / Railway / Fly).** Rejected:
  free tiers sleep, require a card, or have been withdrawn; a cold start on the
  Rescue flow is unacceptable.
- **Supabase for convenience** (auth + Postgres + storage in one). Rejected on
  the hot path specifically — free-tier auto-pause is the exact failure mode this
  ADR exists to prevent.
- **Accepting a spinner during throttling.** Rejected: the golden hour makes
  latency a safety property, not a polish item.
