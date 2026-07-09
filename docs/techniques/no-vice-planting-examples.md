_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Examples Plant Their Vices

> Every example you show a model teaches its own flaws. A mediocre sample becomes the quality ceiling; a viced sample becomes the vice. Prefer principles and zero-shot; when you must show, show clean, real prose.

## The problem it solves

Models learn far more from what you *show* than from what you *say*. A concrete example in a prompt is a demonstration, and a demonstration outweighs an abstract rule; the stronger the model, the more this holds. That single fact produces a family of failures:

- **Few-shot poisoning.** A hand-written "here is good prose" example carries the author's own tics, and the model reproduces them. If your sample paragraph ends on a portable maxim, you have just taught [aphorism and oracle voice](../vices/aphorism-and-oracle.md). If it interprets a gesture, you taught [gesture gloss](../vices/gesture-gloss.md).
- **Assertion/demonstration mismatch.** Your instructions ask for a warm, energetic register, but every example you pasted is cold and restrained. The examples win, and the output drifts toward the demonstrated pole, which is one route into [tonal drift](../vices/tonal-drift.md).
- **Template convergence.** One vivid example of a place or a character becomes the mold every future one is cast in. That is a driver of [voice homogeneity](../vices/voice-homogeneity.md) and the flattening described in [the uniform laconic voice](../vices/uniform-laconic-voice.md).
- **Pink-elephant planting.** A literal "bad" example you quote to forbid it gets reproduced verbatim. That failure is close kin and has its own page: [no pink elephants](no-pink-elephant.md).

This technique is the discipline that keeps all of those from entering through your own examples.

## How it works

The mechanism is imitation. When a model sees a passage in its context labeled as the kind of thing it should produce, it treats that passage as a sample from the target distribution and steers toward it. It copies not just the intent but the surface: cadence, punctuation habits, closing shapes, the specific words. An example is compressed, high-salience instruction that lands right where the model is deciding how to write.

Two consequences follow, and they are the whole reason to be careful:

1. **Your example is a ceiling, not a floor.** The model rarely writes better than what you show it, and it reliably inherits any weakness in the sample. A "good enough" hand-written paragraph caps quality at "good enough," and every flaw you didn't notice gets amplified across the run.
2. **Invented examples are born with the vice.** A sample you write yourself (or worse, one you ask a model to draft for you) carries whatever tics you or the drafting model already have. Then those tics leak into production. This is not hypothetical: a maxim planted in an exemplar has been observed reappearing, near word-for-word, in a character's dialogue several turns later.

So the safe defaults are, in order of preference: **say the principle and show nothing**; if you must show, **show real, verified prose in the target register**; and never show an invented sample, clean-looking or not. A principle generalizes to cases you didn't foresee. A real excerpt anchors register without smuggling in a drafter's habits.

There is one deliberate exception, and it is narrow enough that it belongs on its own page rather than here: a literal *style* string, clearly marked as forbidden, can act as a recognition anchor for a stubborn surface tic. That is the opposite of planting, and it is spelled out under [no pink elephants](no-pink-elephant.md). Everything in *this* document is about the far more common case, examples offered as *good*, which is where the poisoning happens.

## Manual version (in a chat)

You are the one supplying examples, so this technique is entirely in your hands.

- **Lead with the principle, not a sample.** "Write it warm and forward-moving; end each scene on a concrete action" steers better than "write it like this: <paragraph>." The paragraph pins the model to your one instance; the principle lets it generalize.
- **If you paste an example, paste real writing, not your own quick mock-up.** A genuine excerpt from a published author in the register you want anchors the voice cleanly. A paragraph you dashed off carries your defaults straight into the output.
- **Check that your "good" examples actually demonstrate what you asked for.** If you told the model to be vivid and every sample you gave it is muted and clipped, you are teaching muted and clipped. Rebalance so at least one example truly shows the target register.
- **Watch your own prose in the request itself.** The model reads your instructions as text to imitate. If you write your ask in staccato fragments or a "not this, but that" flourish, expect a little of that back. Keep your instructions in the same clean register you want out.
- **When output drifts, suspect the example before adding rules.** If the register keeps coming out wrong, remove or replace the sample you're leaning on rather than stacking more prohibitions on top of it. The example is usually the stronger signal.

The closest analogue to the engineering version below is keeping a short, real reference passage in view while you write, instead of re-reading your own last few paragraphs (which quietly amplifies your habits; see [anti-autophagy memory](anti-autophagy-memory.md)).

## Systematic version (in a tool)

In a pipeline, make "principles first, real exemplars only, no invented few-shot" a house rule, and enforce it in how you build the prompt.

- Keep the voice/style section as **principles plus a short self-check**, with no narrative few-shot block.
- If you need concrete grounding, load **verbatim real excerpts** chosen to span the range you want (age, warmth, loudness, formality), and label them for calibration, not for copying.
- Put reference material in the **cached static prefix** so it is affordable to include on every call.
- Keep concrete option lists (economies, conflict types, name shapes) **only in the generator components that need to make a choice**, never in the prose writer, where a list becomes a new mold.

```python
# Build a prose-writer prompt from principles + real, verbatim references.
# No hand-written "good example" paragraphs anywhere in the prompt.

VOICE_PRINCIPLES = """\
Voice: warm, concrete, forward-moving. Ground every image in something a
character sees or does. Close each scene on a physical action or image.

Before writing, confirm to yourself:
- I stated the thing directly (no defining it against a denied alternative).
- I showed gestures without a clause that interprets them.
- I ended on a concrete act, not a summarizing line.
"""

def load_reference_passages() -> str:
    # Verbatim excerpts from vetted, real source prose in the target register.
    # Chosen to span the range the model tends to collapse.
    # NOT invented, NOT model-drafted.
    return read_file("reference/target_register.md")

def build_writer_prompt(base: str) -> str:
    refs = load_reference_passages()
    # Static, cacheable prefix first; volatile scene state appended later.
    return (
        base
        + "\n\n" + VOICE_PRINCIPLES
        + "\n\n--- REFERENCE VOICE (real, verbatim; for calibration, do not copy) ---\n"
        + refs
    )

# Anti-pattern this replaces (do NOT do this):
#   FEW_SHOT = "Example of good output:\n" + author_writes_a_sample_paragraph()
#   -> the sample's tics become the model's tics, and its quality is the ceiling.
```

A quick audit you can run over prompt files: flag any block labeled "example," "sample," or "e.g." that contains narrative prose, and confirm it is real, verbatim, and clean, or else remove it. Route the confirmation of clean references through the same check your *output* has to pass, so a reference can't carry a vice you gate against elsewhere.

If you are also grounding character or setting generation, the same rule applies and is developed further under [exemplar grounding](reference-real-exemplars.md): variety comes from spanning real references, not from an invented palette.

## Pitfalls

- **"Show nothing" is not always right.** Hard format and safety constraints still need explicit, concrete statement. This technique is about *prose and register* examples, not about being vague on structure or rules.
- **Even a clean-looking invented example is risky.** The tics you can't see are exactly the ones that leak. If you cannot use real prose, prefer principles alone over a polished mock-up.
- **Real references can still be copied verbatim.** Label them "for calibration, do not copy," and watch output for the reference surfacing directly. Grounding is for range and register, not for lifting sentences.
- **Don't sneak option lists into the writer.** An enumerated "variety list" (mining town, casino, border fort) placed in the prose writer becomes the new default mold. Keep such lists in the component that actually selects, and give the writer a structural rule instead.
- **Removing an example is a change, so measure it.** Intuition about what a sample is teaching is unreliable. When you pull or swap an example, compare vice rates before and after on fresh, out-of-sample cases; don't assume the swap helped.
- **This does not replace the reflexive gate.** Clean examples reduce planting, but stubborn surface vices still need a re-read gate ([schema gates](schema-gates.md)) and semantic ones still need [post-hoc validation](post-hoc-validation-and-regen.md). Example hygiene is upstream of both, not a substitute for them.

## Related

- [The core idea](../the-core-idea.md)
- [no pink elephants](no-pink-elephant.md)
- [exemplar grounding](reference-real-exemplars.md)
- [schema gates](schema-gates.md)
- [post-hoc validation](post-hoc-validation-and-regen.md)
- [anti-autophagy memory](anti-autophagy-memory.md)
- [aphorism and oracle voice](../vices/aphorism-and-oracle.md)
- [gesture gloss](../vices/gesture-gloss.md)
- [tonal drift](../vices/tonal-drift.md)
- [voice homogeneity](../vices/voice-homogeneity.md)
