# ADR-002: Product centering — Rescue wing is the hero

- **Status:** Accepted
- **Date:** 2026-07-20
- **Deciders:** Ayush Gupta

## Context

The original concept (ScamShield) was a scam *detector*: paste something, get a
verdict. A full originality stress-test — ~25 product ideas searched against the
live internet across scam / consumer-protection / legal / daily-utility domains,
20+ verified collision searches — reached two conclusions:

1. **Detection is saturated.** ScamAvert, Scamio, ScamCheck, FactsScan,
   ContractSafe, LegalHai, Nyaykar, AiCrix, plus Truecaller and Google Messages
   all occupy "is this a scam?". No room to be original there.
2. **Exactly one lane is empty:** the hour *after* an Indian gets scammed. No
   consumer product orchestrates the golden-hour response — the 1930 call, the
   NCRP filing, the bank fraud-cell email citing the RBI Master Direction, the
   FIR pack, and deadline tracking. The only adjacent players are scam-adjacent
   paid "recovery agents."

Recovery odds collapse with time (reported guidance: ≈50% within 60 min, ≈10% at
24 h, ≈2% at 7 days), so speed in the first hour is the whole value.

## Decision

Re-center the product on the empty lane.

- **Rescue wing** (the golden-hour crisis copilot) is **the hero** — the
  originality, the press story, the interview opener. Built by ~week 10.
- **Vet wing** (paste-anything checker, QR Scan-Before-You-Pay, Scam Weather,
  Family Circle) is retained as **retention/moat infrastructure** — it gets the
  app installed *before* the crisis, and it feeds the flywheel.
- One-liner: **"Everyone tells you it's a scam. ScamVet fights to get your money
  back."**

## Consequences

- Roadmap extends ~18 → ~20 weeks (Rescue front-loaded for early demo value).
- Hard rules follow from being the opposite of the "recovery agent" industry:
  **zero fees, never touch money, never promise recovery, no auto-submission to
  government portals (the copilot prepares, the human files), informational not
  legal advice, PII minimized / local-first.** These are non-negotiable.
- Recovery odds are always stated as *reported guidance*, never our guarantee.
- Standing lesson recorded for all future decisions: category-originality is
  extinct in 2026 (LLMs collapsed app-building cost). Originality = owning a
  specific moment/wedge + execution depth. Stop hunting virgin categories.

## Alternatives considered

- **Ship as a pure detector** — rejected: no empty lane, no differentiation.
- **Drop the Vet wing entirely** — rejected: without it the app isn't installed
  before the crisis hits, and the flywheel loses its consumer feed.
