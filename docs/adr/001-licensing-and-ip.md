# ADR-001: Licensing & IP

- **Status:** Accepted
- **Date:** 2026-07-20
- **Deciders:** Ayush Gupta

## Context

ScamVet is a genuine open product with plausible future commercial paths
(hosted API / B2B for banks, fintechs, telcos). Open-source code can be copied,
so the licensing choice has to (a) keep the work fully public and recruiter-
readable, (b) deter a cloud reseller from taking the code and offering it as a
closed service, and (c) preserve the option to relicense or dual-license later.
Contributions from outside — especially during an October Hacktoberfest push —
permanently block relicensing unless a CLA is in place first.

## Decision

1. **AGPL-3.0-or-later** on the protected core: `core` and `riskengine`. Anyone
   offering a modified version as a network service must open-source their stack.
2. **MIT** on `shieldops`, `scampulse`, and `.github` — platform, data-intel, and
   org-meta repos where permissive licensing lowers friction and there's no core
   IP to protect. (SDKs/clients, when they exist, are also MIT/Apache.)
3. **CLA Assistant bot installed on the org before any external PR is merged.**
   Non-negotiable — without it, outside contributions permanently block future
   relicensing/dual-licensing.
4. **Claim the brand everywhere in week 1:** GitHub org, PyPI, social handles;
   domains when the first deploy needs them; a self-filed India trademark
   (~₹4,500) once revenue is plausible.
5. **Open-core split:** the golden eval dataset, eval harness, and any future
   premium (bank-facing/bulk) connectors stay private. Everything resume-relevant
   is public.

## Consequences

- The code is deliberately the *least* defensible layer. The real moat is the
  data flywheel (accumulated domain-reputation / warehouse history), the labelled
  golden set, the Rescue process knowledge, brand, and distribution.
- AGPL deters commercial free-riding and enables future dual licensing.
- The CLA adds one click to a contributor's first PR — accepted cost.
- **BSL** (source-available, converts to open after ~4 years, bans competing
  commercial use — the Sentry/HashiCorp model) is the documented fallback if
  commercialization gets serious.
- Not a lawyer; one hour with a real lawyer if money appears.

## Alternatives considered

- **MIT/Apache on core** — rejected: lets a cloud provider close-source and resell.
- **BSL from day one** — rejected as the default (recruiters read AGPL as normal
  OSS; BSL raises questions), but retained as the escalation path.
- **`pip install riskengine`** — blocked: PyPI normalizes `riskengine` to the
  existing `risk-engine` project (PEP 503), a hard 400 at upload. Dist name is
  therefore `scamvet-riskengine` (see ADR-003).
