_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Onomatopoeia Spam

> Comic-panel sound words scattered through prose as decoration, one per action, the same effect repeated on isolated lines, where the sentence should carry the impact itself.

**Also known as:** SFX spam, sound-effect overload, decorative onomatopoeia, action-word litter, isolated-SFX-line habit.
**Family:** prose

## The tell

A sound word lands on nearly every beat, often alone on its own line, doing work the prose should do:

- One effect per action, mechanically: a bang for the door, a crack for the board, a thud for the body.
- The same word repeated across a scene as a rhythm rather than a chosen moment.
- Sound words stacked with exclamation marks so the page reads as a storyboard, not a paragraph.

The defining feature is **excess, not presence**. A single well-placed sound word can be excellent craft. The vice is the density: when every impact gets its own capitalized shout, none of them hit, and the passage stops reading like prose and starts reading like a comic with the pictures removed.

**Quick mental test:** count the standalone sound words in a passage. If there is more than roughly one per paragraph, or if the same effect repeats within a few lines, you are looking at spam. Then ask of each one: is this sound word telling the reader something the surrounding sentence does not already convey? If not, it is decoration.

## Before / after

**Space sci-fi**

```text
Before (viced)
BOOM! The airlock blew. HISS! Atmosphere tore past her helmet. CLANG! A loose panel hit the deck. THUD! She slammed into the bulkhead.
```

```text
After
The airlock blew and the atmosphere tore past her helmet in one long shriek, dragging a loose panel end over end down the deck until she and the panel hit the bulkhead together (BANG) and everything went still.
```

**High fantasy**

```text
Before (viced)
CRACK! The staff split. WHOOSH! Flame rushed out. ROAR! The dragon answered. BOOM! The tower shook.
```

```text
After
The staff split down its length and the flame it had been holding rushed out all at once, so bright the guards below threw up their arms. The dragon answered from inside the smoke, and the tower shook hard enough to drop dust from the rafters.
```

Notice the After versions are not merely quieter. They braid the actions into a single moving sentence so cause leads to effect, and they spend one sound word at the beat that earns it rather than stamping every action with a label.

## Why models do this

In genres where stylized sound words are idiomatic, such as action, adventure, and comics-influenced prose, a model learns that onomatopoeia signals energy, and it treats the device as free intensity. Reaching for a sound word is cheaper than choreographing the moment, so the model sprinkles them wherever it wants kinetic feel, mistaking quantity for force.

There is also a pendulum failure worth naming. A model that was earlier pushed to curb the spam can swing to the opposite error and suppress sound words entirely. That over-correction often bleeds outward: because the model over-generalizes what it thinks was forbidden, it starts flattening ordinary exclamation marks and draining energy from the punctuation too. Both poles are wrong. The target is measured use, not zero.

## Why it hurts

Sound words work in comics because they are visual: lettered, colored, part of the drawing. On a text page they have no picture to belong to, so at high density they read as parody, and they cost the reader real things:

- **Impact deflates.** When every blow has its own capitalized effect, the reader stops registering any of them; the one hit that should land has no quiet around it to land against.
- **Choreography goes missing.** The sound word substitutes for the actual motion, for who moved and what gave way and what it cost, so the action turns abstract even as it looks loud.
- **The register reads as juvenile.** A wall of shouted effects pulls a scene toward cartoon, which is jarring when the surrounding story is playing for real stakes.

But total suppression is the opposite error for a genre where a measured sound word belongs, and it collaterally deadens legitimate punctuation and energy. Hold the line at "no spam," not "none."

## Fix it while writing with an AI

- Give an affirmative instruction: **"Use a sound word only at an impact that earns it, at most one per beat, and fold it into the sentence rather than putting it alone on a line. Carry the rest of the action with verbs and physical detail."**
- Name the goal as parsimony, not prohibition: **"Sound effects are allowed but rare; when one appears, it should tell me something the sentence doesn't already."**
- If the model has swung to the flat extreme, re-license energy directly: **"You can still use an exclamation for a genuine shout or impact. I only want fewer isolated sound words, not flatter prose."**
- Paste an over-loud paragraph back with one line of direction: **"Rewrite this so the action flows as movement. Keep one sound word, at the strongest moment, and turn the others into what actually happened."**

## Fix it while building a tool

SFX spam is a **quantity** vice, and quantity vices are the wrong shape for a pre-emit suppressor gate on the writer. A gate that says "restrain sound effects" tends to harden into a total ban and drag exclamation marks down with it. Handle excess **after** generation instead, with a lightweight reviewer or heuristic pass that flags too-many sound words per unit of prose and, on a hit, requests a targeted rewrite of that passage.

Keep the writer's instruction affirmative and permissive ("allowed sparingly, integrated into the sentence"), and let the downstream reviewer catch the overflow. This split keeps the writer energetic and moves the "is this too much?" judgment to a place that can actually count.

```python
# Downstream reviewer / heuristic backstop, NOT a suppressor gate on the writer.
import re

# Match standalone, shouted sound-effect lines and inline capitalized SFX bursts.
SFX_LINE = re.compile(r"^\s*[A-Z]{2,}[!.]*\s*$", re.MULTILINE)
SFX_INLINE = re.compile(r"\b[A-Z]{2,}!")

def sfx_density(prose: str) -> float:
    hits = len(SFX_LINE.findall(prose)) + len(SFX_INLINE.findall(prose))
    paragraphs = max(1, prose.count("\n\n") + 1)
    return hits / paragraphs

def review(prose: str, ceiling: float = 1.0) -> list[str]:
    flags = []
    if sfx_density(prose) > ceiling:
        flags.append("sfx_excess")  # -> regenerate this unit with a parsimony note
    return flags
```

Two rules from the field when wiring this:

- **Never write "no SFX" or "none by default" into a prompt or an enum.** That both over-corrects into a ban and, worse, anchors punctuation as forbidden so the model deadens legitimate exclamation marks. See [No pink elephant](../techniques/no-pink-elephant.md).
- **Never plant a literal sound word as a copyable anti-example in a gate.** Citing a specific effect teaches the exact token and can drag related punctuation down with it. Describe the shape (capitalized standalone sound words at high density), not an instance.

For the reviewer-and-regenerate loop and how to scope it narrowly, see [Post-hoc validation and regeneration](../techniques/post-hoc-validation-and-regen.md). If you keep any reflexive writer gate for sibling prose tics, see [Schema gates](../techniques/schema-gates.md), and keep this vice out of it.

## Watch-outs

- **Beware the pendulum.** A rule meant to curb spam can calcify into a total ban, which is a distinct and worse failure for genres where onomatopoeia is idiomatic. Frame every instruction as restraint, never as prohibition.
- **The ban bleeds into punctuation.** When a model over-generalizes "no sound words," it often flattens exclamation marks and overall energy too. If your prose went gray after an SFX fix, check whether you suppressed too broadly.
- **This is the rare vice where post-hoc beats pre-emit.** A suppressor gate at the writing step causes the very pendulum swing you are trying to avoid; prefer the downstream flag that counts and asks for a rewrite.
- **Keep the reviewer narrow.** A too-broad reviewer homogenizes voice. Its only job here is catching excess sound words, not editing energy out of the scene.
- **Sibling drift.** After you thin the sound words, watch that the writer does not replace lost energy with a different tic, such as a closing aphorism to signal weight or a burst of hard-stop fragments to fake impact. Re-read the passage for a compensating tic, whether a maxim tacked onto the end of a beat or clipped fragments standing in for the lost bang, and cut those the same way.

## Related

- [The core idea](../the-core-idea.md)
- [Dialogue fragmentation](dialogue-fragmentation.md)
- [Aphorism & oracle voice](aphorism-and-oracle.md)
- [Post-hoc validation and regeneration](../techniques/post-hoc-validation-and-regen.md)
- [No pink elephant](../techniques/no-pink-elephant.md)
- [Schema gates](../techniques/schema-gates.md)
