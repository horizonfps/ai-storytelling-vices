_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Ground Voice in Real Exemplars

> When you want to steer a model's voice, register, or range, anchor it on short passages of real, published prose rather than on examples you write yourself. Your invented examples are born carrying the exact tics you are trying to remove, and the model imitates them faithfully.

## The problem it solves

There is a strong temptation, when a model's prose is bland or off-register, to show it a sample of what "good" looks like. You write a paragraph, drop it into the prompt as a few-shot example, and expect the model to match its quality. It does match it, including every flaw you didn't notice you'd baked in.

The trap is that a passage a language model (or a human writing quickly in a model's cadence) produces to *illustrate* good prose tends to arrive pre-loaded with the field's common failure modes. A demonstration example teaches harder than an abstract rule, so a mediocre sample becomes a quality ceiling and a vice vector at the same time. This technique addresses that directly by replacing invented samples with verbatim real ones.

It targets several failure modes at their root:

- **Mean-regressed, generic casts**, where every character is middle-aged, gruff, and interchangeable. This is the [voice homogeneity](../vices/voice-homogeneity.md) and flat-amplitude family, in which free generation collapses toward a statistical center.
- **The [uniform laconic voice](../vices/uniform-laconic-voice.md)**, where the model defaults every speaker to clipped, weighty terseness because that reads as "quality."
- **Grimdark / prestige [tonal drift](../vices/tonal-drift.md)**, where a warm or high-energy brief quietly cools into muted restraint because the only "good" prose the model was shown demonstrated the cold pole.
- The self-inflicted versions of the [aphoristic closer](../vices/aphorism-and-oracle.md), the [gesture gloss](../vices/gesture-gloss.md), and the [contrastive reveal](../vices/contrastive-reveal.md), which ride into production inside a hand-written exemplar and then reappear verbatim in the model's output.

It is a *positive-grounding* technique: it does not tell the model what to avoid. It shows the model a genuine target to steer toward. That makes it a natural partner to [no pink elephants](no-pink-elephant.md), which handles the negative side.

## How it works

The mechanism is imitation, aimed on purpose.

Strong models are exceptional imitators. Whatever concrete prose sits in their context becomes a pull on the next tokens, far more than any rule stated in the abstract. This is why demonstration dominates instruction, and why the pull gets *stronger* as models get more capable. You can either fight that pull or use it.

An invented exemplar uses it badly. When you write a sample of "vivid, warm" prose, you are writing in your own voice under time pressure, which for most of us drifts toward the same machine cadences the model already over-produces: the tidy verdict at the end, the "not X but Y" swerve, the gesture explained one clause too far. The model doesn't know these are flaws. It reads the sample as the authoritative target and reproduces the shape, tics and all. Worse, a concrete example overrides your abstract rules. You can write "vary sentence length" three times and still get metronomic prose if your one example is metronomic.

A real, verbatim exemplar uses the same imitation pull in your favor. Published prose that has actually earned its register carries the texture you want (the specific concrete detail, the varied rhythm, the range across warm and cold and loud and quiet) without teaching the tics, because a good writer isn't producing them. You are handing the model a true north instead of a decoy.

The second half of the mechanism is *spread*. Free generation regresses to the mean on any axis you don't actively push on: age clusters in the forties, temperament clusters on "wary," volume clusters on "quiet." A curated set of real exemplars chosen to span that axis, an elderly eccentric next to a boisterous teenager next to a cold professional, resets the model's sense of the range. It generates against the width of the reference set, not against its own narrow default.

## Manual version (in a chat)

You can run this by hand with no code at all, and it is one of the highest-leverage things a chat writer can do.

**Study, then show; don't invent.** Before you ask for prose in a particular register, find one or two short passages of real, published writing that hit that register, and paste them in as calibration, clearly labeled as reference:

> Here are two short passages whose *voice and rhythm* I want you to calibrate to. Not the plot, not the characters, just the register. Read them, then write my scene in that register. Do not quote or reuse their wording.

Pick passages deliberately. If your problem is that every character sounds the same, choose exemplars that are loud, quiet, formal, and blunt, so the model sees the range. If your problem is that the prose keeps going grim, choose warm, high-energy writing, because the model will otherwise default to the cold pole.

**Do not paste your own quick approximation.** The paragraph you dash off to "show what I mean" is the single most common way writers poison their own sessions. If you cannot find a real passage, describe the register in principles instead ("close scenes on a concrete action, let dialogue overlap and interrupt") and skip the example entirely, because a lean principle beats a viced sample.

**Re-anchor when the voice drifts.** Over a long chat the model slides back toward its defaults, especially if it has been feeding on its own recent output. When you notice the register cooling or the voices merging, re-paste the reference and ask it to recalibrate. (For why long sessions drift on their own, see [anti-autophagy memory](anti-autophagy-memory.md).)

**Keep the reference for range, not for copying.** Add the standing note "for register only; do not reproduce these figures or phrasings," so the model doesn't lift a real character or a distinctive line into your story.

## Systematic version (in a tool)

Wire a curated block of verbatim real exemplars into the generator's instructions, in the cached static part of the prompt, and point the relevant schema fields at that block for their sense of range.

Two design rules matter. First, the exemplars go in the **static prefix** that gets prompt-cached, before any per-call volatile content; otherwise you pay full price for a large reference block on every single call. Second, the exemplars are for **calibration**, so say so explicitly in the instruction, and keep any concrete option-lists (economies, dispositions, plot shapes) *out* of the prose writer, where they would become a new template the whole cast converges on.

```python
# Curated once: short verbatim passages from real, high-quality sources,
# chosen to SPAN the axis the model collapses on (age, volume, warmth, formality).
# Kept verbatim even if a source excerpt is imperfect; document gaps rather
# than "cleaning them up" into invented prose that reintroduces the tics.
REFERENCE_VOICES = load_reference_block("reference_voices.md")

def build_generator_prompt(base_instructions: str) -> str:
    # Reference block lives in the STATIC, cache-friendly prefix.
    return (
        base_instructions
        + "\n\n--- REFERENCE VOICES (real, verbatim) ---\n"
        + "Calibrate register, rhythm, and range to these passages. "
        + "They exist to widen your range, not to be copied; do not reuse "
        + "their wording, characters, or settings.\n\n"
        + REFERENCE_VOICES
    )

# A field that tends to regress to the mean points at the reference for its range,
# instead of enumerating an invented palette (which becomes a convergence template).
character_schema = {
    "disposition": {
        "type": "string",
        "description": (
            "Draw temperament and vocal amplitude from the full range shown in "
            "the reference voices, where loud and warm are as available as quiet and "
            "cold. Do not default to a single restrained register."
        ),
    }
}
```

For a small library, five to a dozen passages is plenty. Choose them the way you'd cast an ensemble: deliberately different from each other, each one covering a corner of the range the model otherwise ignores. When you later observe the generator still clustering on one pole, the first move is to add an exemplar at the opposite pole, not to add another rule.

Pair this with [schema gates](schema-gates.md) for the surface tics a gate can catch, and with [post-hoc validation](post-hoc-validation-and-regen.md) for the diffuse register vices that neither grounding nor a gate fully closes.

## Pitfalls

- **Invented examples are the whole problem, so don't reintroduce them.** A well-meant hand-written sample of "good output" is exactly what plants the vice. This has been observed directly: a grim one-liner sitting in a sample scene came back word-for-word in a character's dialogue. If you have any invented few-shot prose in your generator, suspect it before you suspect a missing rule.

- **Real anchors need to actually be real and verbatim.** A paraphrase you write "based on" a real passage is an invented example wearing a disguise; it carries your idiolect. Keep the source text exact, even if a given excerpt is slightly imperfect, and note the imperfection rather than smoothing it into fresh prose.

- **Guard against verbatim copying into output.** The instruction "for register only, do not reproduce" is load-bearing. Without it, a distinctive real character or a memorable line can be lifted straight into your story. Watch early outputs for this and tighten the instruction if it happens.

- **Choose for spread, or the grounding won't fix the collapse.** If every exemplar is quiet and literary, you've re-taught the quiet-literary default with better prose. Include the loud one, the old eccentric, the blunt professional, covering the exact axis that regresses to the mean, or the reference block just raises the floor on a range that is still too narrow.

- **Don't overload the context.** A huge reference dump can overfit the model to those specific voices and eats budget even when cached. A tight, varied handful outperforms a wall of text.

- **Grounding is not a gate.** Real exemplars steer register and widen range, but they don't reliably suppress a specific stubborn surface tic. For that, add a [reflexive gate](schema-gates.md); for diffuse semantic vices, add a [post-hoc check](post-hoc-validation-and-regen.md). Grounding steers register and widens range, but it will not catch a stubborn surface tic on its own; pair it with a gate and a post-hoc check.

- **Fix at the source when the data is frozen.** If a saved character profile already holds flat, invented voice notes, re-running the prose writer with a better reference block won't help, because the sameness is baked into the data. Regenerate or patch the stored profiles too.

## Related

- [The core idea](../the-core-idea.md)
- [No pink elephant](no-pink-elephant.md)
- [Schema gates](schema-gates.md)
- [Post-hoc validation and regeneration](post-hoc-validation-and-regen.md)
- [Anti-autophagy memory](anti-autophagy-memory.md)
- [Voice homogeneity](../vices/voice-homogeneity.md)
- [Uniform laconic voice](../vices/uniform-laconic-voice.md)
- [Tonal drift](../vices/tonal-drift.md)
