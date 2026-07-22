# ADR-005: Kavach visual motif — lens replaces shield

- **Status:** Accepted
- **Date:** 2026-07-22
- **Deciders:** Ayush Gupta

## Context

The design language was originally specified with a shield motif — "shield-ripple
motion", "loading = shield forming", and a shield glyph on the org profile. All of
it was inherited from the project's earlier name, which was abandoned after a
trademark collision. The rename changed the brand but the visual metaphor survived
unexamined.

A shield is the wrong metaphor for this product. The product's verb is *vet*: to
examine before trusting. A shield is passive armour; vetting is active inspection.
The shield motif also collides conceptually with the platform repo, which is
legitimately named for guarding deployments.

The motif is not cosmetic. It determines the loading language, the motion tokens,
and the brand mark across five surfaces that share one token set. Choosing it
after components exist means rewriting all of them.

## Decision

**The motif is a lens.**

- Loading is a field resolving from unfocused to sharp ("focus-pull"), not a
  shape assembling.
- Verdict arrival is focus snapping sharp and then stillness. No celebration.
- The brand mark is a still, sharp lens; it does not animate at rest.
- The scan viewfinder ring *is* the motif, making it structurally native rather
  than decorative.
- In Rescue mode the lens is present but entirely static.

Motion tokens and the full specification live in `DESIGN.md`.

## Consequences

- The shield-ripple and shield-forming language in the project's design notes is
  superseded and must be corrected wherever it appears.
- The org profile glyph changed from a shield to a magnifier; any remaining shield
  iconography in public surfaces is a defect.
- `shieldops` and `shield-operator` keep their names. They describe guarding
  deployments, not the consumer brand, and they carry no user-facing visual.
- The focus-pull transition must degrade to a plain opacity fade under
  `prefers-reduced-motion`, and vanish entirely in Rescue mode — so no component
  may depend on it to convey meaning.

## Alternatives considered

- **Vitals / pulse trace** — medical reading of "vet", and it would tie visually
  to the intelligence pipeline. Rejected: waveform imagery during a financial
  crisis risks reading as alarm, and the crisis surface is required to be calm.
- **Aperture / iris blades** — the most distinctive as a mark. Rejected: an iris
  animation is demanding to execute identically across web, React Native and a
  desktop webview, and the parity rule makes divergence a defect.
- **Keep the shield** — rejected: it is an artifact of a dead name and describes
  the wrong action.
