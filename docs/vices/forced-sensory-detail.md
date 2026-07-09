_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Forced Sensory Detail

> Sensory description arrives as reflex filler, most often a chemistry-set smell or taste ("ozone," "copper," "iron," "sulfur," "acid in the air"), pasted onto a beat to signal intensity, whether or not the point-of-view character would ever perceive it that way.

**Also known as:** ozone-and-copper reflex, chemistry-as-scent, periodic-table smells, metallic-taste tic, lab-report senses, sensory padding.
**Family:** prose

## The tell

A moment gets tense, strange, or violent, and the prose reaches for the same small cluster of chemical words to prove it. The detail is not anchored to what the character could perceive; it is applied from outside the scene to signal intensity.

- **The chemistry cluster.** Ozone, copper, iron, sulfur, acid, "the taste of metal," "a coppery tang," all used to mean "something big is happening" regardless of setting.
- **One smell per beat.** Every escalation gets its scent, mechanically, like a checkbox: fear smells of copper, magic smells of ozone, danger smells of iron.
- **Out-of-world vocabulary.** A character in a world with no chemistry, no batteries, no ozone layer nonetheless "tastes copper," a modern clinical word in a mouth that has no frame for it.
- **Reflex over relevance.** The detail does no work: cut it and the scene loses nothing, because it was never anchored to what this character in this place would actually notice.

**Quick mental test.** Ask two questions of any smell or taste you find: *Would this specific character, in this specific world, name it in these terms?* and *If I delete it, does the scene lose information?* If the answer to both is no, it is forced sensory padding. A related tell: if you could swap the same smell into three unrelated scenes without editing, it is interchangeable filler rather than something the character actually observed.

## Before / after

**Quiet horror**

```text
Before (viced)
The cellar door swung open on its own. The air went sharp with ozone, and
she tasted copper at the back of her throat, acid burning behind her teeth.
```

```text
After
The cellar door swung open on its own. The cold came up the steps first,
a wet, mineral cold that smelled of turned earth and something older under
it, like meat left too long in a cold room. She did not go down.
```

**High fantasy**

```text
Before (viced)
The sorcerer raised both hands and power crackled off him. The whole hall
filled with the stink of ozone and hot iron, and the guards tasted metal.
```

```text
After
The sorcerer raised both hands, and the air went heavy. A pressure
pushed the breath back down your throat and set the torch flames leaning
flat toward him. Two guards nearest the dais dropped to their knees before
they knew their legs had folded.
```

Each "after" trades the reflex chemistry for what the moment would actually deliver. In the cellar, that is a specific mineral cold and an animal wrongness the character can name from her own life; in the hall, it is the *physical effect* of overwhelming force (pressure, leaning flame, buckling knees), which reads as far more powerful than a list of fumes. The senses now come from inside the world.

## Why models do this

The chemistry cluster is a heavily trained shortcut. In a great deal of source text, "ozone," "copper," and "the taste of iron" mark tension, adrenaline, blood, and the uncanny, so the model has learned them as a cheap, high-probability way to sound vivid and clinically "realistic." It is reaching for the surface texture of intensity without checking whether the point-of-view character has any way to perceive or name it.

There is a compounding cause worth naming for tool builders: register drift. When a prompt or a batch of examples nudges a model toward a cool, grounded, "gritty" voice, this cluster comes along for free, because it belongs to the same clinical palette. So the padding is often not a lone tic but one symptom of a wider tonal pull toward lab-report prose. And when a writer is fed its own recent output as context, a smell it coined once can calcify into a recurring stamp across many later scenes.

## Why it hurts

It breaks the point of view. Sensory writing is supposed to put us inside a specific body in a specific place; a stock chemical smell that nobody in the scene could actually identify does the opposite, exposing the writing engine instead of the world. In a pre-industrial, fantastical, or simply non-clinical setting, "ozone" and "a metallic taste" are anachronisms that yank the reader out.

It is also filler that pretends to be craft. Because the detail is unearned and interchangeable, it dilutes the real sensory beats around it, and repeated across a work it becomes an unmistakable machine signature. Worst of all, it substitutes for the harder, better move: rendering force through physical *effect*, through weight, silence, cold, people fainting, which is what actually lands.

## Fix it while writing with an AI

- **Give an affirmative instruction:** "Draw every smell and taste from this character's own world: smoke, rot, tar, brine, old blood, tallow, wet wool, a specific food. For an overwhelming force, describe the physical effect on bodies and objects, not its chemical make-up."
- **Anchor to the point of view:** ask, "What would *this* character notice here, given what they've smelled before in their life?" A fisher and a court scribe register a room differently, so make the detail theirs.
- **Request a targeted self-check:** "Before finishing, list every smell and taste. For each, confirm it belongs to this world and this character. Replace any clinical or chemical term with something diegetic, or cut it."
- **Paste it back trimmed:** when you see "the air smelled of ozone / she tasted copper," delete the clause and ask the model to rebuild the beat around a concrete effect (a sound stops, the cold arrives, a hand shakes) instead.
- **Curate an allowed palette per setting.** For a given story, hand the model a short list of smells that genuinely belong to it, and let it draw from there.

## Fix it while building a tool

Treat this as a semantic register vice, which means a self-audit gate helps but rarely closes it alone. Two layers work best.

First, a reflexive pre-emit field that makes the writer re-read an affirmative commitment before it drafts. Keep the banned cluster in the *description* and never as literal tokens inside the enum value, which would plant the exact words you want gone.

```json
{
  "pre_emit_audit": {
    "type": "object",
    "required": ["sensory_from_the_world"],
    "properties": {
      "sensory_from_the_world": {
        "type": "string",
        "enum": ["i_draw_smells_and_tastes_from_this_world_and_render_force_as_physical_effect"],
        "description": "Every smell or taste must be one this point-of-view character could perceive and name in this setting. Do not reach for a fixed clinical cluster to signal intensity. Convey overwhelming force through physical effect on bodies and objects, not through invented chemistry."
      }
    }
  }
}
```

Second, a post-generation check plus regeneration for the residue, since the model will mark itself "clean" while still emitting the stamp. Match the high-frequency tokens as *whole words* (so you don't flag a character literally named for a metal, or a coppersmith who belongs in the scene), and regenerate on a hit with a note to source the detail from the world instead.

```python
import re

CHEMISTRY_STAMP = re.compile(
    r"\b(ozone|sulfur|sulphur)\b"
    r"|\b(copper|iron|metal|metallic|acid)\b\s+(taste|tang|smell|scent)"
    r"|(taste|smell|tang|scent)\s+of\s+\b(copper|iron|metal|blood-metal|acid)\b",
    re.IGNORECASE,
)

def emit_with_check(generate, max_tries=3):
    for _ in range(max_tries):
        prose = generate()
        if not CHEMISTRY_STAMP.search(prose):
            return prose
    return prose  # accept the residual rather than looping forever
```

The deeper fix is often upstream. If the writer is drifting cold, rebalance the exemplars and register cues toward the intended tone rather than piling on more "don't" rules, and make sure you aren't feeding the model its own past prose as a tone template (which lets one coined smell calcify). See [Schema gates](../techniques/schema-gates.md) for the reflexive field, [No pink elephant](../techniques/no-pink-elephant.md) for keeping the banned tokens out of the enum, [Post-hoc validation and regeneration](../techniques/post-hoc-validation-and-regen.md) for the detect-and-regenerate backstop, and [Anti-autophagy memory](../techniques/anti-autophagy-memory.md) for breaking the self-imitation loop that entrenches a repeated smell.

## Watch-outs

- **Don't ban sensory detail; relocate its source.** The goal is world-appropriate perception, not sparse, senseless prose. Keep smells and tastes; just make them diegetic.
- **Chemical words can be legitimate.** In a contemporary lab, an electrical fire, or a modern battlefield, ozone and cordite belong. Scope the check to settings where the vocabulary is an anachronism, and let a coppersmith's forge smell of hot copper on purpose.
- **The gate under-delivers on its own.** This is a semantic/register vice; the self-audit catches some, the post-hoc detector closes most, and register rebalancing prevents the rest. A gate without the other layers leaves a residue.
- **Watch the pendulum.** A hard ban can over-correct into prose that names no smells at all, which is its own flatness. Aim for "from the world," not "none."
- **Accept a residual.** Even strong models slip occasionally. Chasing zero with ever-narrower rules dilutes the gates that work and burns output budget.
- **Sibling morphs.** The same reach-for-a-shortcut impulse also shows up as the [aphorism-and-oracle](aphorism-and-oracle.md) closer and as [psychological simile](gesture-gloss.md); when the register drifts cold, expect these together.

## Related

- [The core idea](../the-core-idea.md)
- [Gesture gloss](gesture-gloss.md)
- [Aphorism & oracle voice](aphorism-and-oracle.md)
- [Schema gates](../techniques/schema-gates.md)
- [No pink elephant](../techniques/no-pink-elephant.md)
- [Post-hoc validation and regeneration](../techniques/post-hoc-validation-and-regen.md)
- [Anti-autophagy memory](../techniques/anti-autophagy-memory.md)
