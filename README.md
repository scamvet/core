# ScamVet Core

> **Vet anything before you trust it. Fight back the moment you're hit.**

The multi-agent app behind ScamVet — both wings, the API, and all clients.

- **Vet wing** — paste or scan anything suspicious (message, link, screenshot,
  UPI ID, QR, job offer, call story) and get an explained 3-color verdict in
  English or Hindi. Plus QR Scan-Before-You-Pay, the Scam Weather daily card, and
  Family Circle.
- **Rescue wing** — the golden-hour crisis copilot. One screen: an agent
  interviews you, extracts every field from your screenshots, and generates the
  complete response pack — 1930 call sheet, NCRP pre-fill, bank fraud-cell email
  citing the relevant RBI Master Direction, and an FIR walk-in pack — then runs
  deadline reminders and case tracking.

> ⚠️ ScamVet is free and open, never charges recovery fees, never promises
> recovery, and only routes you into official channels (1930, cybercrime.gov.in,
> your bank, the RBI ombudsman). It prepares your filings; you file them.
> Informational assistance, not legal advice.

## Status

🚧 Early development. This repo is being built chapter by chapter; the Vet agents
land first (Chapter B), the Rescue wing follows (Chapter C).

## Architecture

Agents in LangGraph — intake (multimodal, Hinglish) → evidence (tool loops via an
MCP server) → risk scorer (calls RiskEngine models) → explainer (SHAP-grounded,
EN/HI) → guardian (guardrails + cost-router). Hot path is edge-first and always
live. See [ARCHITECTURE.md](ARCHITECTURE.md).

## The flywheel

ScamPulse feeds → [RiskEngine](https://github.com/scamvet/riskengine) retrains →
[ShieldOps](https://github.com/scamvet/shieldops) canary-deploys → Core serves
users → usage + consented, PII-scrubbed rescue-case patterns flow back to
[ScamPulse](https://github.com/scamvet/scampulse).

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md). A CLA (one click on your first PR) is
required. Good first issues are labelled.

## License

[AGPL-3.0-or-later](LICENSE).

## Built by

[Ayush Gupta](https://github.com/ayushgupta07xx).
