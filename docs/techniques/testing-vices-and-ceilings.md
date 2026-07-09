_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Testing Vices and Accepting Ceilings

> A vice you "fixed" usually just moved somewhere you weren't looking. Test across varied scenarios, count real occurrences instead of trusting the model's self-report, and stop tightening once you hit the model's ceiling, which sits around the low 90s percent for a strong model, because past that you break more than you fix.

## The problem it solves

Every other technique in this guide *reduces* a vice. This one tells you when a reduction is real, when it is an illusion, and when to stop, so the other techniques' claimed wins can be verified rather than assumed.

Three things go wrong when people fix prose vices without a testing habit:

- **The vice migrates.** You close the [contrastive reveal](../vices/contrastive-reveal.md) in dialogue and it resurfaces in a reasoning field. You suppress a favorite word in the two scenes where it annoyed you and it blooms in a third scene that was clean before. A generative model has a distributed stylistic fingerprint; press it down here and it bulges out there. A one-scene check will miss the bulge every time.
- **The model lies to you politely.** Ask a [schema gate](schema-gates.md) whether it committed the vice and it will mark itself clean on nearly every run, including the runs where an independent check catches the fault red-handed. The self-report is close to worthless as a measurement. If you treat "ok" as evidence, you will ship regressions that felt fixed.
- **You chase a zero that does not exist.** Reflexive gates and post-hoc passes drive a large drop, but they never reach 100%. There is a per-model residual of roughly 6 to 11% on the hardest vices for a strong model, higher for a lighter one. Piling on rules to close that last gap dilutes the gates that were working, eats your output-token budget, and often degrades the prose elsewhere. Worse, it tempts you to loosen your test bar to force a green.

This technique underpins the whole family of surface-shape fixes ([reflexive schema gates](schema-gates.md), [principles over rules](principles-over-rules.md), [anti-pink-elephant](no-pink-elephant.md) discipline), and it is the guardrail on the self-reinforcing failures in [self-cannibalizing repetition](../vices/self-cannibalizing-repetition.md) and [tonal drift](../vices/tonal-drift.md), which only reveal themselves over a long run.

## How it works

Three habits, in order.

**Test across varied scenarios, not the one that annoyed you.** A vice lives in the model's distribution, not in a single prompt. So you probe the distribution: several fixtures, several genres, several scene shapes (crowded and empty, calm and violent, opening and mid-scene), plus the part people skip: *blind, out-of-envelope* cases written without the fix in view. Keep your original annoying cases as a non-regression baseline, and add fresh ones so you can tell generalization from memorization. A fix that scores 80% on the cases it was tuned against and collapses on blind cases is overfit; the number that tells the truth is the blind one.

Why this matters causally: a targeted fix changes the model's behavior along one axis, and the fingerprint redistributes. If you only re-run the scenario you fixed, you confirm the local win and stay blind to the migration. Re-running the *full* set, including scenarios that were already passing, is how you catch a global change that quietly regressed an untouched corner.

**Measure the real rate; never trust the self-report.** Count occurrences with something independent of the model's opinion: a pattern match for a syntactic vice (an em-dash aside, a negation frame), a small reviewer pass for a diffuse one, or an honest human read. Report a fraction like "gloss appeared in 3 of 24 runs", not an impression. For anything stochastic, one sample is noise; run enough that the number stabilizes.

The reason the self-mark fails is worth internalizing: the model doesn't perceive its own tic as a fault, because each individual sentence reads like competent craft. Asking it to grade itself asks it to see a thing it is constitutionally blind to. The value of a gate's self-check is the *re-read*, never the verdict. Measurement has to come from outside the writer.

**Accept the ceiling; set your bar there and stop.** Once a good fix has driven the rate down to the model's floor, the remaining residue is that floor, and tightening the prompt further will not close it. Document it and move on. If the residue is *large*, first check that your rule is stated at the level of the operation with its common surface forms listed, because a too-narrow rule fakes a high ceiling by catching only one face and letting siblings through. But once the rule is principled and the number has plateaued, more rules cost more than they return.

## Manual version (in a chat)

You can run all three habits by hand; you just do the counting yourself.

**Vary the probe.** When you land a fix (say the assistant stops gluing an interpretive clause onto every gesture), don't declare victory on the scene you were editing. Ask for three more short scenes in different registers and read them for the same tic:

> Write three short passages in different moods: a tense stakeout, a quiet domestic morning, a first meeting between strangers. I'll check them.

If the gloss is gone from the stakeout but creeps back into the domestic morning, you've found the migration, and you know the fix didn't generalize.

**Count, don't vibe.** Skim each passage hunting for one specific pattern at a time, and tally. "Two of the three still end on a little moral." That fraction is your signal. The assistant's cheerful "I've avoided that entirely!" is not.

**Know when to stop.** If four of five passages are clean and the fifth slips once, that is likely as good as this model gets. Adding a sixth stern instruction usually makes the prose stiffer without making it cleaner. Take the win and keep writing.

One honest limit: in a chat you can't run twenty samples cheaply, so your counts are small and noisy. Lean harder on variety (different genres surface migration fast) and on your own careful read, and be willing to say "good enough" earlier than a test harness would.

## Systematic version (in a tool)

Build a small harness that runs the *real* pipeline (the actual generator and writer stages, not a toy prompt), A/B across many runs per fixture, and judges each output with something independent of the writer.

```python
import re

# A/B a fix across varied fixtures, N runs each, counting a real pattern.
FIXTURES = [
    "noir_interrogation", "scifi_derelict_station", "fantasy_market",
    "domestic_kitchen", "period_dockside", "quiet_horror_stairwell",
    # blind, out-of-envelope cases authored WITHOUT the fix in view:
    "blind_courtroom", "blind_field_hospital", "blind_lighthouse",
]
N = 24

def gloss_present(prose: str) -> bool:
    # Independent detector: a pattern, not the model's own verdict.
    return re.search(r"\b(as if|like someone who|the way (a|one)\b)", prose, re.I) is not None

def measure(pipeline, fixtures, n) -> dict:
    rates = {}
    for fx in fixtures:
        hits = sum(gloss_present(pipeline.run(fx)) for _ in range(n))
        rates[fx] = hits / n
    return rates

before = measure(baseline_pipeline, FIXTURES, N)   # e.g. mixed, some ~0.50
after  = measure(patched_pipeline,  FIXTURES, N)   # e.g. mostly ~0.08

# 1) Did it drop where it hurt?           -> after[fx] < before[fx]
# 2) Did it regress anywhere clean?       -> any(after[fx] > before[fx])  == migration
# 3) Do blind cases match tuned cases?    -> small gap == real; large gap == overfit
CEILING = 0.10  # this model's known floor for this vice
done = (max(after.values()) <= CEILING
        and all(after[fx] <= before[fx] for fx in FIXTURES))
```

Notes that decide whether the harness tells the truth:

- **Judge with something the writer doesn't control.** A regex for a localizable vice, a separate reviewer model for a diffuse one, or a scored human read. Curate patterns to avoid false positives (a bare fragment like "of whom" will flag legitimate prose, so anchor to the real construction). Never route the writer's own self-audit field back in as the pass/fail signal.
- **Re-run the whole set, always.** Include scenarios that already passed. A global change, such as a broadened gate or a new prompt line, can regress a fixture you never touched. Marking a vice "done" after one targeted pass is how migration hides.
- **Separate in-sample from blind-fresh.** Report both. The blind number is the honest one. A wide gap means you memorized the tuned cases; the fix for overfit is usually a structural change, not another rule.
- **Run the real pipeline, not an arid stub.** Empty test scenes provoke fabrication and register drift the model would never produce in live play, so a stub can *over*-report a vice that barely exists in practice. Some vices are tail events of rich context that only appear end-to-end; validate there.
- **Instrument the whole run for long-horizon vices.** For [self-cannibalizing repetition](../vices/self-cannibalizing-repetition.md) and [tonal drift](../vices/tonal-drift.md), a short test shows nothing, because the decay is cumulative. Track a metric across a long session (repeated-n-gram counts, punctuation energy per thousand words, a register score) and set both a lower alarm for flattening and an upper alarm so a liveliness fix doesn't overshoot into spam.
- **Hold the bar; don't move it.** When you hit the ceiling, record the residual as accepted. Do not relax the harness's success threshold to paint a green; that only hides the residue you agreed to live with.

## Pitfalls

**Trusting the self-mark.** The model reports compliance while committing the vice. Treat its audit field as a steering device for the *writer*, never as a measurement. Measure from outside.

**Declaring victory after one scenario.** The single most common way a regression ships. A local win plus an unexamined global change equals a migrated vice you'll find in production. Re-run everything.

**Presuming instead of measuring.** "This prompt change obviously helped" is how quiet regressions survive. Count before and after. Intuition about what plants or fixes a vice is unreliable; the [anti-pink-elephant](no-pink-elephant.md) cases in particular routinely surprise people.

**Chasing zero past the ceiling.** Every extra rule you add to close the final few percent competes for attention with the gates that work, spends output budget, and tends to stiffen the prose. Diminishing returns arrive fast. Set the bar at the measured floor and stop.

**Mistaking a narrow rule's fake ceiling for the real one.** If the residual is high, the rule may be catching one surface form and missing its siblings, which inflates the apparent ceiling. Raise the rule to the operation with its faces listed (see [principles over rules](principles-over-rules.md)) *before* concluding the model can't do better.

**Over-fitting your test set.** A harness you tune against becomes a thing you optimize *for* rather than a thing that measures truth. Keep adding blind, out-of-envelope cases so the number keeps meaning something.

**Loosening the harness to force a green.** This is the worst failure precisely because it feels like progress. If you find yourself editing the pass criteria to make a run go green, stop: you are hiding the residual, not fixing it. Accept the ceiling honestly instead.

## Related

- [The core idea](../the-core-idea.md)
- [Schema gates](schema-gates.md)
- [Principles over rules](principles-over-rules.md)
- [No pink elephant](no-pink-elephant.md)
- [Ground voice in real exemplars](reference-real-exemplars.md)
- [Examples plant their vices](no-vice-planting-examples.md)
- [Tonal drift](../vices/tonal-drift.md)
- [Self-cannibalizing repetition](../vices/self-cannibalizing-repetition.md)
- [Contrastive reveal](../vices/contrastive-reveal.md)
- [Gesture gloss](../vices/gesture-gloss.md)
