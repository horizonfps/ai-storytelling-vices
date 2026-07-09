_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Tonal Drift (Grimdark Creep)

> Over a long session the register cools and darkens on its own. Wonder turns to risk-analysis, warmth turns to restraint, and an intended bright or hopeful story quietly slides into muted prestige-noir, one well-written sentence at a time.

**Also known as:** grimdark creep, gritty drift, prestige-TV register, noir slide, tonal cooling, register decay, atmospheric withholding.
**Family:** prose

## The tell

The individual sentences look fine. The aggregate register is wrong. You asked for one emotional genre and the model is steadily delivering a colder, more sombre one. Watch for these signatures:

- **Wonder narrated as a briefing.** A marvel arrives as a technical description instead of an experience: the sea-monster is logged, not gasped at. Awe gets muted; nobody in the scene is allowed to be delighted.
- **The ignored marvel.** Something extraordinary happens and the cast shrugs. "No one looked at the glowing shoal." A story about wonder tells the reader, through its own characters, that the wonder is not worth caring about.
- **Place before people, mood before action.** Scenes open on an empty room, dust, sagging awnings, gray light, with the setting described at length before any person does anything. The camera lingers on atmosphere and forgets to start the story.
- **The withholding close.** Beats end on a portentous coda that points past the frame ("and there was no one left to call") instead of landing on a concrete act. Every scene fades out wistful.
- **Everyone gets quieter and weightier.** Dialogue converges on clipped, loaded, humorless lines. The chatty innkeeper starts brooding. Exclamation and question marks disappear as the session runs on.
- **The lone-marvel-image-turned-eulogy.** A single sombre detail you dropped in early keeps returning as the closing shot.

**A quick test:** read an early scene and a late scene side by side. If the late one is grayer, quieter, and more restrained than the register you asked for, and you never asked it to darken, you have drift. The failure is invisible to line-level review because each sentence passes; only the trend line fails.

## Before / after

**Space sci-fi**

```text
Before (viced)
The lantern swept across the derelict's hold, and the derelict answered
with nothing. Mara logged the oxygen reading. "Structure's compromised,"
she said. "We take what floats and we leave." Behind the cracked glass, a
garden bloomed in vacuum. It was just another wreck.
```

```text
After
Mara's lantern hit the cracked glass and she stopped breathing for a
second. A whole garden, blooming in hard vacuum, petals the size of doors.
"No. No way." She pressed her helmet to the glass. "There's something in
there keeping it warm, has to be, and I want to see it."
```

**High fantasy**

```text
Before (viced)
The market square lay empty. Dust moved where feet used to. The awnings
sagged in the noon heat, and she stood at its edge a long while before she
went in, though there was no one left to ask.
```

```text
After
She shouldered into the market, caught the fruit-seller by the apron, and
asked, loud, so the whole row could hear, where they had taken her
brother. Awnings snapped overhead. Two stalls down, someone stopped
haggling to listen.
```

Both "afters" keep whatever gravity the scene had, but they open on a person doing something and let place arrive attached to a body. Wonder becomes a specific, physical guess a character makes out loud; grief becomes an action taken in front of witnesses. The register is warm and moving instead of cooled and observational, and it is genuinely more vivid, not merely louder.

## Why models do this

The register a model produces is set far more by what a prompt (and its own recent output) **shows** than by what it **states**. Two forces push toward the dark pole:

- **Cold restraint reads as "good taste."** In most training prose, sparse, understated, ominous writing correlates with prestige and quality. So when nothing actively pulls the register elsewhere, the model relaxes toward the sombre literary default. Stronger models are *more* susceptible, because they imitate demonstrated register more faithfully than they obey an abstract instruction.
- **One escape route from purple prose.** If your prompt fought "flowery slop" by showing only cold, clipped examples, you taught exactly one way out of purple: go minimal and grim. You never demonstrated the other way out, which is big feeling landed in a concrete image, so the model only knows the cold exit.

Two amplifiers make it worse over a session. First, **input contamination**: register lives in every field you hand the writer, not just the style note. A grim backstory, a lone melancholy ambient image, or a premise invented by an upstream step in a darker key all get faithfully transmitted and intensified downstream. Second, **self-conditioning**: if the writer is fed its own recent prose as "memory," it imitates its own tone, and a slightly quieter turn becomes the model for an even quieter next turn, a ratchet that no static style rule can outvote once the raw history dominates context.

## Why it hurts

The output ends up in the wrong emotional genre for the product. A hopeful, high-spirited, wondrous story loses the exact thing readers came for: joy, freedom, awe, momentum. They get muted melancholy instead, and they feel the mismatch even when they can't name a single bad sentence.

It also compounds silently. Because each line is well-written, quality-assurance that reads sentence by sentence never catches it; the drift only shows across many turns, by which point late scenes read like faded photocopies of the vivid early ones. And the withholding close deprives every scene of a landing, so the story never lets the reader arrive anywhere; it just keeps fading out.

## Fix it while writing with an AI

- **Name the target register affirmatively, and make it concrete.** Give the model a positive target rather than a vague adjective: "Big feeling, landed in a concrete gesture or image (a declared dream, an out-loud gasp, real laughter), with a physical floor under it. Open on a person doing something; let place arrive through them. Close on an action, not a mood."
- **Show, don't just tell, the register.** If you paste an example, make sure it actually demonstrates the warmth you want. A pile of restrained "good" examples teaches the cold pole no matter what your instructions say.
- **Fix the inputs, not just the style note.** If the backstory or scene seed you're feeding in is grim, the prose will be grim. Rewrite those seeds toward agency and life, or add: "Transmute heavy background into a living, active scene rather than transmitting its gloom."
- **Don't feed the model its own prose as a tone template.** For continuity, hand it a short factual summary of what happened, not its last several paragraphs to imitate. If you must show recent prose, keep it tiny and reset it at each scene break.
- **Re-anchor periodically.** Every several turns, ask it to re-read your intended-register line (or an early strong passage) *before* writing the next scene, and to vary any image it has leaned on.
- **What to paste back:** when a scene opens on an empty room or ends on a wistful fade, return it with "Open on a character acting; end on a concrete beat or the joke, not a fade."

## Fix it while building a tool

Tonal drift is a **diffuse register vice**, not a syntactic one, so a reflexive self-audit gate alone will barely touch it; the model marks itself "on register" and drifts anyway. The real levers are inputs, exemplars, and memory. Attack it in layers:

1. **Audit demonstration-vs-assertion.** Inventory every example, few-shot, and contrastive pair in your prompt and classify its register. If the tone section asserts one thing but the examples all show a colder one, the examples win. Rebalance so at least one exemplar demonstrates the intended warm-grounded register. Prefer verbatim excerpts from real, high-quality source prose over hand-written samples, because invented examples tend to carry the very vices you are fighting.

2. **Apply register discipline upstream, not only to the final writer.** The same model that writes prose often also generates premises, plots, and scene seeds, all with the same cold default. Fix drift at the earliest stage that authors register. Make upstream generators honor a supplied brief instead of inventing a darker one, and phrase trauma/"scar" fields as living-agency rather than static gloom.

3. **Break the self-conditioning loop.** Never feed the writer a growing pile of its own raw prose as memory. Cap any raw-prose window to the current scene and reset it at scene cut; carry long-term continuity as distilled *factual* summaries; and keep an overused-imagery ledger so history teaches divergence instead of imitation.

```python
# Writer memory: facts for continuity, a tiny scene-scoped prose window,
# and an "avoid these" ledger, never a rolling bank of the model's own prose.
def build_writer_memory(all_turns, scene_start_idx, cap=8):
    scene_prose = [t.prose for t in all_turns if t.idx >= scene_start_idx]
    return {
        "prior_facts":     factual_summary(all_turns[:scene_start_idx]),  # facts, not prose
        "recent_prose":    scene_prose[-cap:],   # current scene only, reset at scene cut
        "overused_imagery": ngram_ledger(all_turns, window=8),  # "vary these"
        "target_register": "grounded-warm: big feeling on a concrete floor",
    }
```

4. **Prefer subtractive fixes to added gates.** If scenes keep ending on ominous codas, don't add a rule permitting them "only when trouble is seeded," because the model finds tension everywhere. Remove the closure-foreshadowing instruction entirely and replace it with "close on the concrete; a hook enters embodied in-scene, not as a fade."

5. **Change one variable at a time, and measure long.** Stacking more tone rules on a strong model does not converge and can regress. Flip one lever (an input seed, one exemplar, one removed rule), then measure over a long run, tracking punctuation rate, opening-on-person rate, and register scored by a model judge, because drift is cumulative and short tests hide it.

See [No pink elephant](../techniques/no-pink-elephant.md) for why to demonstrate the register rather than quote the bad one, [Post-hoc validation and regeneration](../techniques/post-hoc-validation-and-regen.md) for a detect-and-regenerate backstop, and [Schema gates](../techniques/schema-gates.md) for where a reflexive gate does and does not help.

## Watch-outs

- **A reflexive gate is not enough here.** Field-tested gates for diffuse register all self-mark "ok" and miss the drift. Don't add an inert gate; it only dilutes the ones that work. Use inputs, exemplars, and memory instead.
- **Don't over-correct into exclamation spam or forced zaniness.** Re-enabling energy needs an upper alarm too. The target is a lively, grounded register, not everything in caps. Give punctuation a positive license ("one exclamation for a shout, one question mark for surprise; don't chain them") rather than swinging from silence to noise.
- **Beware suppressor rules that cross-suppress.** An anti-onomatopoeia or anti-caps rule can quietly read as "never use `!`," flattening the register further. Scope those rules to what you actually meant, and remove any literal banned example from them.
- **It migrates up the pipeline.** Fix it in the prose writer and it can reappear in the plot or premise generator, which runs the same model with the same default. Audit every stage that authors register.
- **One "sensible" character will absorb the whole gloom.** The lone straight-man starts delivering doom-poetry one-liners. The fix is ensemble-wide, with everyone eccentric in a different key, not a per-line patch.
- **Accept a residual.** Even with all layers in place, a small fraction of scenes will still cool. Measure it and hold your bar; don't chase zero by piling on rules.
- **Sibling morphs:** the same cold impulse surfaces as an [aphorism or oracle close](aphorism-and-oracle.md), a [uniform laconic voice](uniform-laconic-voice.md) across the cast, or [voice homogeneity](voice-homogeneity.md) where every character sounds equally grim.

## Related

- [The core idea](../the-core-idea.md)
- [Aphorism & oracle voice](aphorism-and-oracle.md)
- [Uniform laconic voice](uniform-laconic-voice.md)
- [Voice homogeneity](voice-homogeneity.md)
- [Forced sensory detail](forced-sensory-detail.md)
- [No pink elephant](../techniques/no-pink-elephant.md)
- [Post-hoc validation and regeneration](../techniques/post-hoc-validation-and-regen.md)
- [Schema gates](../techniques/schema-gates.md)
