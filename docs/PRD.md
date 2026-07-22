# ScamVet — Product Requirements

- **Status:** Phase 0 baseline (living document)
- **Last updated:** 2026-07-22
- **Owner:** Ayush Gupta

> **Everyone tells you it's a scam. ScamVet fights to get your money back.**

This document defines what ScamVet is, who it serves, and what "done" means for
each capability. It is the product contract. Architecture lives in
`ARCHITECTURE.md`; visual and interaction rules live in `DESIGN.md`; irreversible
decisions live in `docs/adr/`.

---

## 1. The problem

Two problems, one continuum.

**Before.** An ordinary Indian phone receives a stream of messages, links, QR
codes, job offers, and calls, some fraction of which are fraudulent. Existing
tools answer "is this a scam?" adequately — that market is crowded. Answering it
in the user's own language, with reasons they can act on, is where there is still
room.

**After.** The moment money leaves an account, the user is alone. There is a
government helpline, a national reporting portal, a bank fraud cell, and a police
station — four channels, four different information formats, several deadlines,
and no product that orchestrates any of it. Widely reported guidance is that the
chance of recovery falls steeply within the first hours. In that window the
victim is panicking, has no idea what a UTR number is, and is being circled by
paid "recovery agents" whose business model is a second fraud.

**Nobody builds for the hour after.** That is the product.

## 2. Users

**Primary — the victim in the first hour.** Just lost money, on a phone, likely
panicking, possibly on a bad connection, possibly not comfortable in English.
Cannot be asked to read, choose between options, or understand terminology. Needs
one action at a time and a visible sense of progress.

**Primary — the daily checker.** Receives something suspicious, wants a fast
answer with a reason. Uses the product a few times a week; this frequency is what
puts the app on the phone *before* the crisis.

**Secondary — the family protector.** Installs and configures on behalf of a
parent or relative. Cares about the group-chat and weekly-report surfaces.

**Secondary — the person about to pay.** Standing at a shop or looking at a
payment request, about to scan a QR. Has seconds, not minutes.

Design implication: the daily checker funds the Rescue wing's existence by
keeping the app installed. Neither wing is optional.

## 3. Scope

### In scope

Two wings sharing one intake, one verdict language, one design system, and one
API.

| Wing | When | Purpose |
|------|------|---------|
| **Vet** | Before / daily | Explain whether something is safe, in the user's language, with reasons |
| **Rescue** | The hour after | Turn a crisis into a complete, correctly-formatted set of filings and deadlines |

### Explicit non-goals

These are not scope gaps; they are refusals.

- **We never touch money.** No payments, no holds, no reversals, no escrow.
- **We never charge for recovery.** No fees, no commission, no "success" pricing,
  no premium crisis tier. Ever.
- **We never promise recovery.** Odds are quoted as reported guidance, never as
  our forecast, never as a number attached to the user's specific case.
- **We never auto-submit to a government portal.** No API exists for this, and
  automating the forms would mean defeating a bot wall. The copilot prepares; the
  human files.
- **We do not give legal advice.** Everything produced is informational and is
  reviewed by the user before it is filed.
- **We do not accuse.** Outputs are risk assessments about artifacts, not
  allegations about named people.
- **We do not compete on caller ID or SMS interception.** Truecaller and Google
  Messages own that surface. Our lane is explain-and-act.

## 4. Vet wing

### 4.1 Accepted inputs

One intake, many artifact types. The user should never have to choose a category
before pasting.

| Input | Notes |
|-------|-------|
| Free text / forwarded message | Including Hinglish and Devanagari |
| URL | Including shorteners, which are resolved |
| Screenshot | Multimodal extraction, then treated as its underlying type |
| UPI ID (VPA) | Payee-handle checks |
| QR payload | Decoded, then treated as a payment request |
| Job / offer description | A distinct scam family with its own signals |
| Call narrative | User describes what was said; no audio interception |

### 4.2 The verdict contract

Every verdict, on every surface, is the same object. This contract is binding on
the API, PWA, Telegram bot, Android app, and desktop app simultaneously — a
change to it is a change to all of them in the same commit.

```
verdict {
  band:        "green" | "amber" | "red"
  headline:    short plain-language sentence
  reasons:     [ { text, evidence_type, weight } ]   // ordered, most important first
  confidence:  "high" | "medium" | "low"
  unknowns:    [ text ]        // what we could not check, stated openly
  next_steps:  [ action ]      // always at least one
  language:    "en" | "hi"
  generated_at, model_version, ruleset_version
}
```

Rules:

- **Three bands only.** No numeric score on the consumer surface. Scores exist
  internally and in the API, not in the user's face.
- **Reasons are mandatory.** A verdict with no reasons is a bug, not a terse
  verdict.
- **Unknowns are shown, not hidden.** "We could not check X" is a first-class
  part of the output. This is what makes the product honest rather than
  confident.
- **Low confidence never renders as green.** Uncertainty resolves toward caution,
  and the band is accompanied by the reason it is uncertain.
- **Never colour-only.** Every band carries an icon and a text label, because a
  meaningful share of users cannot distinguish the colours.

### 4.3 Layered evaluation

The verdict is assembled in three layers, and the layer that produced each reason
is retained.

1. **Deterministic facts.** Blocklist membership, domain age, resolved
   destination after unshortening, ranking presence, feed hits. Cheap, fast,
   never hallucinated.
2. **Calibrated model score.** The scoring library's probability, calibrated so
   the number means what it says.
3. **Language reasoning.** Explanation, translation, and pattern recognition over
   the artifact — constrained by, and never contradicting, layers 1 and 2.

If layer 3 is unavailable (throttling, outage), layers 1 and 2 still produce a
usable verdict. This is a requirement, not a fallback.

### 4.4 Scan Before You Pay

Camera opens, QR decodes, verdict lands in about a second.

- Decode the payment payload; surface the payee handle and the name the payment
  app will show.
- Flag: handle-to-name mismatch, recently-seen-in-reports handles, signs of a
  tampered or overlaid sticker, unusual request amounts where present.
- Must work with no network for the deterministic layer.
- Single screen, no menu, camera-first. This is the highest-frequency surface and
  therefore the one that earns the install.

### 4.5 Scam Weather

One generated card per day: what is circulating right now, in one glance, shareable
into a family group. Generated from the intelligence pipeline, not written by hand.
Must render as an image for sharing and as text for accessibility.

### 4.6 Family Circle

A bot in the family group. Anyone can forward a message and get a verdict in the
thread. A weekly summary reports what was checked and what was caught. No private
message content leaves the group beyond the artifact submitted for checking.

## 5. Rescue wing

### 5.1 Entry

One unmistakable action, present on every surface: home screen, app shortcut and
widget, share sheet, bot command, and a persistent entry point in the web app.
From cold start to first question must be under three seconds.

### 5.2 Crisis intake

- **Voice-first**, with typing always available. Hindi, Hinglish, English.
- **One question per screen.** No multi-field forms.
- **Branches by fraud family:** UPI fraud, card fraud, task/investment scam,
  "digital arrest", predatory loan app, mule-account recruitment. Each branch
  asks only what its filings actually require.
- **Never blocks on a missing answer.** "I don't know" is always accepted and is
  carried forward as a gap to fill later.
- The first screen states, before anything else, what the user should do in the
  next five minutes — this is delivered immediately, not after the interview.

### 5.3 Evidence extraction

The user uploads screenshots; the system extracts the fields the filings need.

| Field | Typical source |
|-------|----------------|
| Transaction reference / UTR | Payment app receipt |
| Amount, date, time | Receipt or bank SMS |
| Beneficiary handle / account | Receipt |
| Beneficiary name as displayed | Receipt |
| Payer account / card tail | Bank SMS |
| Counterparty phone numbers | Call log, chat header |
| Bank and channel | SMS sender, app branding |

Requirements:

- **Per-field confidence, always visible.** Every extracted field is shown next
  to the crop it came from.
- **Mandatory user confirmation.** Nothing enters a filing unconfirmed. A wrong
  UTR in a complaint is worse than a missing one.
- **Refuse rather than invent.** If a screenshot is the wrong type or unreadable,
  the correct output is "I couldn't read this — here's what I need", never a
  plausible-looking guess. This is an explicit red-team case.

### 5.4 The response pack

Four artifacts, generated together, each independently usable.

**1. Helpline call sheet.** A single screen holding exactly the information the
operator will ask for, in the order they ask for it, in large type, readable
one-handed while speaking. Nothing else on the screen.

**2. National portal complaint pre-fill.** Complaint narrative in filing-ready
language, plus the evidence bundle organised to match the portal's own
categories, plus a checklist of what to upload where. The user copies and files.

**3. Bank fraud-cell email.** Addressed to the correct channel for that bank,
citing the applicable customer-liability provisions, stating the reporting time,
and attaching the portal acknowledgement once it exists. Written to be sent
without editing, but presented for review first.

**4. Police walk-in pack.** A printable bundle: complaint copy, evidence index,
transaction table, and the acknowledgements already obtained. The purpose is to
walk in with a complete file rather than assemble one at the counter.

**Correctness requirements for all four:**

- Every field, format, and citation is verified against the primary source at
  each release, and the verified date is recorded in the artifact's own metadata.
- Procedural guidance is **versioned** and treated as content that expires.
  Portal categories, forms, and required fields change without notice; a stale
  instruction is a defect of the same severity as a crash.
- Any claim about deadlines or liability that cannot be traced to a current
  primary source does not ship.

> This document deliberately does not restate specific statutory deadlines,
> citation dates, or portal field names. Those live in the versioned guidance
> pack, are verified per release, and are the single source of truth for the
> generators. Hardcoding them here would guarantee they drift.

### 5.5 Deadline engine

- Computes the user's dates from the incident time and the current guidance pack.
- Reminds ahead of each deadline, not on the day.
- Escalation drafts are prepared automatically when a case goes unanswered past
  the documented threshold.
- Works if the user never opens the app again: reminders reach them where they
  already are.

### 5.6 Case tracker

Acknowledgement numbers in one place, a status checklist, and a plain-language
explainer of what happens next at each stage. This exists because the most common
reported experience after filing is silence, and silence is what makes people
give up on a live case.

## 6. Cross-cutting requirements

### 6.1 Always live

Binding, per ADR-004. Nothing pausable on the hot path; graceful degradation to a
fast local verdict; five-minute liveness pings; public status page. The test
applied to every user-facing component before it ships: *untouched for eight
months, opened on hotel wifi — is there a working product in three seconds?*

For the Rescue wing this is not a quality bar, it is the product. A cold start
during the golden hour is a total failure regardless of how good the output would
have been.

### 6.2 Language

Full Hindi and Hinglish across both wings, including voice input and generated
artifacts. English is not the default; the user's language is. Filings are
generated in the language the receiving institution expects, while the
explanation of that filing is in the user's language.

### 6.3 Accessibility

Large-text mode as a first-class setting, not a browser zoom. Contrast targets
per `DESIGN.md`. Reduced-motion respected. Voice input everywhere text input
exists. No jargon on any consumer surface — if a term must appear because a form
requires it, it is defined inline.

### 6.4 Privacy

- **Local-first for crisis data.** Extracted fields stay on the device wherever
  the flow allows. Server-side storage is limited to what the user explicitly
  saves.
- **Minimisation.** We collect what a filing needs and nothing else.
- **Analytics carry no case content.** Aggregate patterns only, consented, with
  identifiers and amounts stripped or banded before they leave the device.
- **A visible, plain-language privacy note** on the Rescue entry screen, before
  the first question.

### 6.5 Trust posture

The product's closest commercial neighbours are paid recovery operations that
prey on victims a second time. Every design choice must make the difference
obvious at a glance: free, open source, no account required to get help, no
payment surface anywhere in the crisis flow, and every route ending at an
official channel rather than at us.

## 7. Success criteria

Targets, not achievements. Until real users exist, every figure reported anywhere
is dogfooded or simulated and is labelled as such. No user counts will be claimed
that are not real.

**Vet wing**
- Verdict precision and recall clear the release bars on the labelled golden set.
- Zero false-positive regressions on the red-team set of legitimate-but-alarming
  messages.
- Verdict returned within the latency target at p95, including the degraded path.

**Rescue wing**
- Per-field extraction precision clears its bar on the labelled screenshot set.
- Wrong-type screenshots produce a refusal, never fabricated fields.
- Artifact-correctness checklist passes against primary sources at each release.
- Complete pack generated in under ten minutes from first tap, measured in
  dogfood runs.

**Both**
- Quality gate blocks release on any of the above.
- Every public URL verified logged out before it is called done.

## 8. Risks

| Risk | Mitigation |
|------|-----------|
| Procedural guidance goes stale and misleads a real filing | Guidance versioned, verified per release, community-reportable, expiry visible in-artifact |
| Misextraction corrupts a complaint | Per-field confidence, mandatory confirmation, refusal over invention |
| Free tiers change or throttle | Degradation path is a build item; no single provider on the hot path |
| The product is mistaken for a paid recovery service | No payment surface, zero-fee statement on every crisis screen, open source |
| A verdict is wrong in a way that costs someone money | Layered stack, honest unknowns, uncertainty never rendered as green, measured before every release |
| Crisis flow used by someone in acute distress | Calm design rules in `DESIGN.md`; route to official human channels early and prominently |

## 9. Open questions

- Which fraud families warrant their own intake branch at launch versus a
  generic branch with follow-ups.
- Whether the case tracker should support a household view without weakening the
  local-first privacy stance.
- How the guidance pack is versioned and distributed so a stale client cannot
  generate an outdated filing.
- What the offline boundary is for Rescue: which artifacts can be produced with
  no connectivity at all.
