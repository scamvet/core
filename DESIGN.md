# Kavach — the ScamVet design system

- **Status:** Phase 0 baseline (living document)
- **Last updated:** 2026-07-22
- **Owner:** Ayush Gupta

> **Kavach is an internal codename for this design system.** It is never a public
> brand, never appears in user-facing copy, app names, or marketing. The product
> is ScamVet.

This document is written before the first component. Tokens are defined once here,
expressed once in the Tailwind config, and consumed by every surface — web app,
Android, desktop, bot cards, status page. A surface that invents its own value is
a defect.

---

## 1. Principles

**Calm over clever.** Someone reading this interface may have just lost money. No
flourish, no celebration, no motion that draws attention to itself.

**The verdict is the interface.** Everything else is chrome around a three-band
answer and its reasons.

**Colour is meaning, never decoration.** Green, amber and red are reserved
absolutely for verdicts. They appear nowhere else — not on buttons, not on links,
not on charts.

**Honest states.** Loading states show what is actually happening. Uncertainty is
rendered, not hidden. Empty states explain rather than apologise.

**Degrades by design.** Every visual must survive reduced motion, no network,
large text, and dark mode. If it only works in the happy path, it is not done.

## 2. The motif — Lens

**Replaces the shield motif**, which was inherited from the project's earlier name
and does not fit a product whose verb is *vet*: to examine. See ADR-005.

The lens is chosen because it is literal (vetting is inspection), because it is
structurally native rather than decorative (Scan Before You Pay is an actual
camera), and because its natural motion — an unfocused field resolving into
sharpness — resolves into stillness rather than into a flourish.

**How it appears:**

- **Loading:** the content field begins slightly unfocused and low-contrast, and
  resolves. Nothing spins. Nothing bounces.
- **Verdict arrival:** focus snaps to sharp and stays. The settle is the payoff;
  there is no secondary animation after it.
- **Idle brand mark:** a sharp, still lens. It does not animate on the home
  screen.
- **Scan surface:** the camera frame *is* the motif — the viewfinder ring is the
  lens, and it is the same shape as the brand mark.
- **Rescue mode:** the lens is present but static. Nothing resolves, nothing
  animates. See §9.

**What the motif must never do:** rotate continuously, pulse for attention,
appear during an error, or animate in Rescue mode.

## 3. Colour

### 3.1 Brand — trust indigo

```
--kv-indigo-50:  #F2F1FD
--kv-indigo-100: #E5E3FA
--kv-indigo-200: #CBC8F5
--kv-indigo-300: #A8A2ED
--kv-indigo-400: #8079E3
--kv-indigo-500: #5D53D6
--kv-indigo-600: #4A41B8
--kv-indigo-700: #383191
--kv-indigo-800: #2A2670
--kv-indigo-900: #1E1B4B
--kv-indigo-950: #16143A
```

Primary brand is `--kv-indigo-700` on light surfaces and `--kv-indigo-300` on
dark.

### 3.2 Surface — warm off-white and ink

```
--kv-paper-0:  #FFFFFF
--kv-paper-50: #FAF8F4    /* default light surface — warm, not clinical */
--kv-paper-100:#F4F1EA
--kv-paper-200:#E8E3D8

--kv-ink-900:  #14121F    /* default dark surface */
--kv-ink-800:  #1D1A2B
--kv-ink-700:  #2A2639
--kv-ink-600:  #3B3650
```

Light theme text: `--kv-ink-900` on `--kv-paper-50`.
Dark theme text: `--kv-paper-50` on `--kv-ink-900`.

Dark mode ships day one and is a peer theme, not an afterthought — the crisis
flow is frequently opened at night.

### 3.3 Verdict — reserved

**These six tokens may only be used to express a verdict band.** Any other use is
a defect.

```
/* light theme */
--kv-verdict-green-fg: #0B6B41
--kv-verdict-green-bg: #E6F4ED
--kv-verdict-amber-fg: #8A4B08
--kv-verdict-amber-bg: #FCF0DC
--kv-verdict-red-fg:   #9B1C15
--kv-verdict-red-bg:   #FBE9E7

/* dark theme */
--kv-verdict-green-fg-dark: #5BD99B
--kv-verdict-green-bg-dark: #0C2B1E
--kv-verdict-amber-fg-dark: #F5C062
--kv-verdict-amber-bg-dark: #33230A
--kv-verdict-red-fg-dark:   #F79189
--kv-verdict-red-bg-dark:   #38110E
```

**Colour is never the only signal.** Every band renders colour + icon + text
label together:

| Band | Icon | Label (en) | Label (hi) |
|------|------|-----------|-----------|
| green | check in circle | Looks safe | सुरक्षित लगता है |
| amber | exclamation in triangle | Be careful | सावधान रहें |
| red | cross in octagon | Do not trust | भरोसा न करें |

Low confidence never renders green, per the PRD verdict contract.

### 3.4 Neutral utility

Links, focus rings, and interactive states use indigo. Destructive UI actions
(delete a saved case) use a **neutral-dark** treatment, never red — red belongs to
verdicts alone.

```
--kv-focus-ring: #5D53D6
--kv-danger-ui:  #3B3650    /* destructive UI actions, deliberately not red */
```

## 4. Typography

One family for UI, one for numerals in filings where alignment matters.

```
--kv-font-ui:      "Inter", system-ui, sans-serif
--kv-font-mono:    "IBM Plex Mono", ui-monospace, monospace
```

Devanagari must render at parity with Latin — the Hindi UI is not a smaller,
uglier variant. Verify the chosen family's Devanagari coverage before first
component; substitute a matched pair if coverage is poor.

### 4.1 Scale (base)

```
--kv-text-xs:   0.75rem / 1.1rem
--kv-text-sm:   0.875rem / 1.35rem
--kv-text-base: 1rem / 1.6rem
--kv-text-lg:   1.125rem / 1.75rem
--kv-text-xl:   1.375rem / 1.9rem
--kv-text-2xl:  1.75rem / 2.2rem
--kv-text-3xl:  2.25rem / 2.6rem
```

Body copy is never below `--kv-text-base` on a consumer surface. `--kv-text-xs`
is permitted only for metadata such as timestamps.

### 4.2 Large-text mode

A product setting, not browser zoom. Multiplies the scale by **1.3** and increases
line-height proportionally. All layouts must survive it without truncation or
horizontal scroll — this is a test case, not a hope.

## 5. Space, radius, elevation

```
--kv-space-1: 4px    --kv-space-5: 24px
--kv-space-2: 8px    --kv-space-6: 32px
--kv-space-3: 12px   --kv-space-7: 48px
--kv-space-4: 16px   --kv-space-8: 64px

--kv-radius-sm: 6px
--kv-radius-md: 10px
--kv-radius-lg: 16px
--kv-radius-full: 999px

--kv-elev-1: 0 1px 2px rgba(20,18,31,0.06)
--kv-elev-2: 0 4px 12px rgba(20,18,31,0.08)
--kv-elev-3: 0 12px 32px rgba(20,18,31,0.12)
```

Elevation is used sparingly; the verdict card is the only element permitted
`--kv-elev-3`.

Minimum touch target is **48×48px** everywhere, and **56×56px** in Rescue mode.

## 6. Motion

```
--kv-dur-fast:   120ms
--kv-dur-base:   240ms
--kv-dur-focus:  420ms
--kv-ease-settle: cubic-bezier(0.22, 1, 0.36, 1)
--kv-ease-standard: cubic-bezier(0.4, 0, 0.2, 1)
```

**Focus-pull** (the signature): `filter: blur(6px)` → `blur(0)`, `opacity: 0.6` →
`1`, `scale: 1.01` → `1`, over `--kv-dur-focus` with `--kv-ease-settle`. Used when
a verdict or a generated artifact arrives.

**Verdict settle:** the band colour and icon arrive with the focus-pull and then
stop. No bounce, no confetti, no secondary motion. The absence of celebration is
deliberate — a red verdict and a green verdict must feel equally composed.

**Reduced motion:** when `prefers-reduced-motion: reduce` is set, all focus-pull
becomes a plain opacity fade at `--kv-dur-fast`, and the loading field renders at
full sharpness immediately. Nothing is lost but the transition.

## 7. Loading and progress

**Never a bare spinner.** Three permitted patterns:

1. **Focus-pull skeleton** — the content shape appears unfocused and resolves as
   real content arrives. Default for verdicts.
2. **Agent timeline** — for multi-step work, each step is listed and lights up as
   it completes ("reading the message", "checking the link", "scoring", "writing
   the explanation"). The architecture becomes the explanation. Steps are named
   in plain language, never with internal component names.
3. **Step counter** — for Rescue, an explicit "Step 2 of 5 — bank email ready".
   Always visible, never hidden behind a hamburger.

If a step exceeds its expected duration, the UI says so in words and offers the
degraded result rather than continuing to wait silently.

## 8. Component inventory (Phase 0 baseline)

| Component | Notes |
|-----------|-------|
| Verdict card | The centrepiece. Band, headline, ordered reasons, unknowns, confidence, next steps. |
| Reason row | Text plus the evidence type that produced it. |
| Unknowns block | Visually equal to reasons, never de-emphasised. |
| Intake field | One field, many artifact types, paste and voice and camera affordances. |
| Scan viewfinder | Lens ring, live decode, one-second verdict. |
| Agent timeline | Loading pattern 2. |
| Crisis step screen | One question, one action, progress visible. See §9. |
| Field-confirm row | Extracted value, source crop, confidence, confirm/edit. |
| Artifact preview | Generated filing, review-before-use, copy and export. |
| Deadline item | Date, what it is, what happens if missed. |
| Scam Weather card | Shareable image plus accessible text equivalent. |
| Status pill | Live/degraded/down, mirrored on the public status page. |
| Disclaimer bar | Zero-fee, informational-not-legal-advice. Persistent in Rescue. |

## 9. Rescue mode addendum — calm urgency

Rescue mode is a distinct visual treatment, not a themed variant of the Vet UI. It
activates for the entire crisis flow.

**Rules:**

- **Type scale ×1.25** over base, applied on top of any large-text setting.
- **One action per screen.** Exactly one primary control. Secondary actions are
  demoted to a single text link or removed.
- **Progress always visible.** "Step 2 of 5" persists at the top; the user must
  never wonder how much is left.
- **No animation.** No focus-pull, no timeline shimmer, no transitions beyond
  instant state changes. Motion reads as delay to someone in a hurry.
- **No decoration.** No illustrations, no empty-state art, no brand flourish.
- **Touch targets 56px minimum.** Hands shake.
- **Calm palette:** the crisis surface uses `--kv-paper-50` / `--kv-ink-900` with
  indigo reduced to `--kv-indigo-800` and desaturated. Verdict colours remain
  reserved and are used only where a genuine verdict is shown.
- **No red as alarm.** The situation is already alarming. Red stays a verdict
  colour; the crisis UI itself is neutral.
- **The zero-fee and not-legal-advice statements are persistent**, not dismissible
  and not a one-time modal.
- **The first screen answers "what do I do right now"** before asking anything.

**Crisis palette override:**

```
--kv-crisis-surface:      #FAF8F4
--kv-crisis-surface-dark: #14121F
--kv-crisis-accent:       #2A2670   /* indigo-800, desaturated */
--kv-crisis-accent-dark:  #A8A2ED
```

## 10. Accessibility

- **Contrast:** WCAG AA minimum for all text (4.5:1 body, 3:1 large). Verdict
  foreground/background pairs above are chosen to clear AA on their own
  backgrounds in both themes; re-verify any change to them with a contrast checker
  before merge.
- **Never colour alone**, per §3.3.
- **Focus visible** on every interactive element, using `--kv-focus-ring`, never
  removed.
- **Voice input** available anywhere text input is.
- **Screen reader:** verdict cards announce band, headline, then reasons in order.
  The agent timeline announces completion, not every intermediate frame.
- **Large-text mode** is a layout requirement, tested, not a zoom.
- **Reduced motion** honoured everywhere, per §6.

## 11. Token distribution

Tokens are authored **once** in the Tailwind config and flow outward:

```
tailwind.config  →  web (PWA)
                 →  NativeWind  →  Android (Expo)
                 →  CSS vars    →  Tauri desktop
                 →  static map  →  bot cards, OG images, status page
```

Rules:

- A surface never hardcodes a hex value. If a value is missing, it is added to the
  token set, not inlined.
- A token change is a cross-surface change and lands in one commit across every
  surface that renders it, per the cross-surface parity rule.
- Bot cards and generated share images consume the same palette; a verdict green
  in Telegram is the same green as in the app.

## 12. Do-not list

- Do not use verdict colours for anything but verdicts.
- Do not add a spinner.
- Do not animate in Rescue mode.
- Do not celebrate a green verdict.
- Do not use red for destructive UI actions.
- Do not put "Kavach" in user-facing copy.
- Do not introduce a second typeface family without updating this document first.
- Do not ship a component that has not been checked in dark mode, large text, and
  reduced motion.
