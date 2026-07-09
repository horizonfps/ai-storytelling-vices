# The Techniques

This is the index of techniques for preventing and repairing the vices in this
repo. Each one works both ways: as a move you can make by hand while steering an AI
in a chat window, and as a pattern you can wire into a narrative tool or engine.
They are ordered from most foundational, the ideas nearly every fix rests on, to
more specialized methods you reach for once the basics are in place.

Start here for the reasoning behind all of them:

- [The Core Idea](../the-core-idea.md): why prohibitions fail and why an affirmative
  pre-write commitment is the durable fix.

---

- [Reflexive Schema Gates](schema-gates.md): Add a required audit field that makes the
  model re-state an affirmative commitment right before it writes; the payoff is the
  pre-write re-read, not self-detection.
- [No Pink Elephant](no-pink-elephant.md): Never quote the bad pattern in an
  instruction. Naming it makes it salient, so state rules affirmatively and keep literal
  bad examples out of prompts and enums.
- [Principles Over Rules](principles-over-rules.md): A short affirmative principle plus a
  complete list of observed surface faces generalizes to unseen cases, where long
  enumerated rule-lists overfit and leak.
- [Examples Plant Their Vices](no-vice-planting-examples.md): Few-shot examples transmit
  their own flaws, so default to principles-first, zero-shot, and real verbatim references
  only.
- [Ground Voice in Real Exemplars](reference-real-exemplars.md): Steer voice, register,
  and character range with short verbatim passages of real published prose that span the
  axis the model otherwise collapses on.
- [Separate Planning From Prose](separate-planning-from-prose.md): Split the factual
  planner (emits data, no prose gates) from the prose writer (renders it, carries all the
  gates) so style discipline lands only where prose exists.
- [Anti-Autophagy Memory](anti-autophagy-memory.md): Stop the writer feeding on its own
  prose with distilled facts for continuity, a small scene-scoped raw-prose window, and a
  worn-imagery ledger. This is an architecture fix, not a prompt fix.
- [Post-Hoc Validation and Regeneration](post-hoc-validation-and-regen.md): Catch
  semantic and excess vices with a detector after generation and regenerate, layered
  behind the pre-write gates.
- [Second-Pass Reviewer](second-pass-reviewer.md): A separate reviewer reads the whole
  scene with fresh full records and category-aware heuristics to catch continuity breaks,
  agency theft, and residual vices before commit.
- [Testing Vices and Accepting Ceilings](testing-vices-and-ceilings.md): Probe varied and
  blind scenarios to catch migration, measure real rates with an independent judge, and
  accept the model's residual ceiling instead of over-tightening.
