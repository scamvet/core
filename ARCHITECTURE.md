# Architecture — ScamVet Core

> Skeleton. The full per-chapter spec (schemas, API contracts, edge cases, test
> plans, DoD) is produced at the start of each chapter and lands here as it's built.

## Two wings, one app

- **Vet wing** (Chapter B, D) — the daily/before layer.
- **Rescue wing** (Chapter C) — the golden-hour crisis copilot. The hero.

## Agent system (LangGraph)

| Agent | Role |
|-------|------|
| Intake | Multimodal (Gemini Flash): text / screenshot / URL / UPI ID / QR / job offer / call story. Hinglish. Structured output schema. |
| Evidence | Tool loops via an **MCP server**: WHOIS/domain age, URL unshortener, Tranco rank, PhishTank/OpenPhish/URLhaus lookup, Telegram-handle checks. |
| Risk scorer | Calls RiskEngine registry models as tools; fuses deterministic facts + ML scores with explicit uncertainty. |
| Explainer | Plain-language verdict (EN + HI), SHAP-grounded reasons, 3-color contract. |
| Guardian | Guardrails, hallucination checks, cost-router (small model triages, larger explains). |

## Hot path (always-live, binding)

Next.js PWA (Vercel / Cloudflare Pages) → Cloudflare Workers API → D1/KV or Turso
→ Telegram bot as a Workers webhook. Supabase free is **banned** from the hot
path; Neon is cold-path only. GitHub Actions 5-min cron pings; public status page.
Graceful degradation: local blocklist + a distilled ONNX scorer give an instant
verdict if the LLM tiers throttle — degrade to fast-and-useful, never to a spinner.

**Design test for every component:** untouched for 8 months, opened on hotel wifi
— working product in 3 seconds?

## Rescue wing flow (Chapter C)

"I've been scammed" (home screen / Telegram command / share-sheet) → calm crisis
UI → crisis intake agent (voice-first, one question per screen, branches by fraud
type) → evidence extractor (per-field confidence + user confirmation) →
response-pack generator (1930 sheet / NCRP pre-fill / bank email / FIR pack) →
deadline engine → case tracker.

**Rescue hard rules:** zero fees, never touch money, never promise recovery, no
auto-submission to portals (prepare, don't file), informational not legal advice,
PII local-first.
