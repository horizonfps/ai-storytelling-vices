_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Uniform Laconic Voice

> Every character and every moment rendered in one clipped, withholding, hardboiled register, no matter who is speaking or what the scene calls for.

**Also known as:** everyone-broods, clipped-stranger voice, fake-terse cast, monotone withholding, single-register ensemble, prestige-noir default.
**Family:** dialogue (spreads into narration)

## The tell

A whole cast talks in the same spare, weighted, tight-lipped way. A warm innkeeper, a nervous teenager, and a stone-cold enforcer all deliver short loaded lines and let silences do the work. Dialogue is withheld in favor of gestures and one-word answers, regardless of who the character is or how they should sound.

Signature patterns:

- Talkative or cheerful characters are written as brooding and monosyllabic by default.
- Answers arrive as a shrug, a glance, and three words, when the person would plainly say more.
- The narration matches, cool and flat and minimal, so the whole world feels grim and quiet even in a scene that should be loud.
- Over a long session the register keeps cooling: exclamation and question marks disappear, energy drains, and late scenes read deader than early ones.

**Quick mental test:** ask who this character actually is. If a person the brief calls warm, chatty, boastful, or frightened comes out sounding exactly like the terse loner two scenes ago, the voice is not theirs. It is the model's one register applied to everyone.

## Before / after

**High fantasy**

```text
Before (viced)
The baker set down his rolling pin. "The flood." He wiped his hands. "It came." He said nothing else, and the shop was quiet.
```

```text
After
The baker lit up like the ovens behind him. "Oh, the flood, worst in forty years! Water came up past the second step, took my whole sack of white flour, and old Marta's cat rode a barrel clean across the square. I swear it on my mother's grave, a barrel!"
```

**Space sci-fi**

```text
Before (viced)
"Reactor's warm," the deckhand said. He didn't look up. "Shouldn't be." A pause. "We take what floats. We leave."
```

```text
After
"You feel that? The reactor's warm, it's been cold since the last crew died out here, and now it's humming like it's happy to see us." The deckhand grinned, already scared, already three rungs down the ladder. "Come on, I have to know what's keeping it going. Have to."
```

The After lines are not louder for the sake of noise. They give each speaker a temperament, the baker's delight in his own story and the deckhand's reckless curiosity, so the voice belongs to a person instead of to a mood.

## Why models do this

Terse, withholding dialogue reads as cool and weighty, and models carry a strong prior that "good" fictional dialogue is spare and loaded. So the laconic register gets applied as a universal upgrade to every speaker. Cold restraint also correlates with "prestige" prose in most training text, making it the low-resistance default the model slides toward whenever nothing actively pulls it elsewhere.

Two forces make it worse:

- **A character brief that says "terse" gets over-read.** A note meant to convey "chooses words carefully" becomes "speaks only in fragments," and the model bakes clipped delivery into that character permanently.
- **The register self-poisons over a long run.** If the writer sees its own recent output as context, each slightly flatter turn becomes the example for the next, so the whole session ratchets toward one muted voice.

## Why it hurts

The point of a cast is contrast. When everyone speaks in the same key, the world loses its people:

- **Characterization collapses.** A chatty merchant and a silent assassin become interchangeable, so the reader stops tracking who is who by voice.
- **Warmth and energy drain out.** A story that should feel alive and populated reads as uniformly bleak, which quietly pushes the whole piece into a genre it never intended.
- **The register keeps decaying.** Because the flattening compounds turn over turn, a session that opened vibrant ends inert, and the drift is invisible scene to scene, so it is caught late.

## Fix it while writing with an AI

- Anchor voice to the person, not to a mood: **"Write this character to match their temperament. The innkeeper is loud and generous with words; the spy is contained. Give each speaker a distinct way of talking."**
- Make expansive the default and reserve restraint for who earns it: **"Most people talk freely. Save clipped, withholding speech for characters whose brief actually establishes it, such as a trained killer, a recluse, or a spy."**
- Fix a "terse" note at the source: describe the temperament (**"spends few words, picks them well"**) rather than the punctuation (**"speaks in fragments"**), which locks the tic in.
- Guard against long-run decay: **"Do not inherit the volume of the last few turns. Set this scene's register from who is in it and what is happening now."**
- Request a self-check you can act on: **"Read the last three characters' lines side by side. If they sound like the same person, rewrite so their voices diverge on temperament and energy."**

## Fix it while building a tool

Two levers matter here, and they sit in different places.

**1. Ground the writer in real range, and steer the character generator.** Feed the generator verbatim reference profiles drawn from real, high-quality source material that deliberately span the axes that collapse (age, warmth, loudness, formality). Set expansive as the explicit default and de-bias the generation so voices diverge on temperament, not just biography. A reflexive schema gate on the writer helps hold the line: its key names the vice, its value is an affirmative commitment the model re-reads before emitting, and the rule lives in the description, never as a copyable bad example in the enum.

```json
{
  "pre_emit_audit": {
    "type": "object",
    "required": ["voice_matches_person"],
    "properties": {
      "voice_matches_person": {
        "type": "string",
        "enum": ["i_write_dialogue_that_fits_this_characters_temperament"],
        "description": "Each speaker's register comes from their own temperament and the moment. Expansive, warm, or loud is the default; clipped withholding speech appears only for characters whose brief establishes it. Do not apply one terse register to the whole cast."
      }
    }
  }
}
```

**2. Stop the long-run decay at the memory layer.** A gate cannot beat a feedback loop. If the writer is fed its own raw prose as ongoing context, cap that window, slice it per scene, and supply factual summaries for older state instead of verbatim prose. Add an overused-imagery ledger so history teaches the model to vary rather than to imitate. This is architectural work rather than a prompt rule; see [Anti-autophagy memory](../techniques/anti-autophagy-memory.md).

For the gate mechanics, ceiling behavior, and why the description (not the enum) carries the rule, see [Schema gates](../techniques/schema-gates.md). Measure the fix in the data: track exclamation-plus-question-mark rate per thousand words and the spread of generated character temperaments, over a long session, with a lower alarm for flattening and an upper alarm so you do not overshoot into shouting.

## Watch-outs

- **Genuinely laconic characters exist.** The target is the default drift across the whole cast, not all terseness. A silent enforcer who is meant to be silent is correct; the vice is the warm baker sounding the same.
- **Do not over-correct into shouting.** Pushing every voice loud is just a new monoculture. The goal is range: some loud, some quiet, each fitting its person.
- **Measure over a long run, not a short one.** Register decay is cumulative and invisible turn to turn; an eight-to-twelve-turn playtest is what surfaces it.
- **Expect a residual ceiling.** Even with grounding and a gate, a fraction of outputs still flatten on the strongest models. Accept the residue rather than stacking more rules, which dilutes the ones that work.
- **Sibling vices it morphs into:** clipping the cast can slide into [Dialogue fragmentation](dialogue-fragmentation.md), and a lone "sensible" terse character tends to absorb the whole register and start dispensing [Aphorism & oracle voice](aphorism-and-oracle.md). Fix voice ensemble-wide, not one line at a time.

## Related

- [The core idea](../the-core-idea.md)
- [Dialogue fragmentation](dialogue-fragmentation.md)
- [Aphorism & oracle voice](aphorism-and-oracle.md)
- [Schema gates](../techniques/schema-gates.md)
- [Anti-autophagy memory](../techniques/anti-autophagy-memory.md)
