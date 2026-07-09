_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Narrate-then-un-narrate

> The narrator states something and then walks it back in the same breath ("maybe it was the wind; it was not the wind"), hedging or self-correcting as if discovering the scene alongside the reader instead of authoring it.

**Also known as:** assert-then-retract, desnarration, the "actually / or rather" hedge, discovering-alongside-the-reader, self-correcting narrator, false-mystery take-back.
**Family:** prose

## The tell

The narrator commits to a fact and then un-commits, spending words to affirm and then negate the same content. It shows up in a few recognizable shapes:

- **The false-mystery bait.** A plausible explanation is floated only to be denied a beat later: "Perhaps a shutter had come loose. No — the shutters were latched, all of them."
- **The mid-sentence hedge.** A connective ("or rather," "in truth," "no, that wasn't it," "not quite") reverses the clause it just wrote: "The house was empty. Or not empty — occupied by a stillness that had weight."
- **The graded retraction.** A word is offered, then swapped for a "more precise" one that carries the real meaning: "She was afraid. Not afraid, exactly. Braced."
- **Live discovery.** The narrator hesitates, wonders, and revises as though it does not already know what happens, an authored voice cosplaying as a reader turning the page.

Quick mental test: **read the passage and ask which version of the fact is true.** If the prose gives you two and cancels the first, keep the one that carries the meaning and delete the setup. Distinct from a *character* correcting themselves out loud in dialogue, which can be genuine: this vice lives in the narrator's own voice.

## Before / after

**Quiet horror**

```text
Before (viced)
A branch tapped the glass. Maybe it was only a branch. It was not a
branch — there was no tree on that side of the house, and the tapping
kept a rhythm no wind would keep.
```

```text
After
The tapping came from the north window, where no tree grew, and it kept
an even four-beat rhythm, patient as knuckles. She counted three rounds
before she made herself cross the room.
```

**Contemporary / domestic**

```text
Before (viced)
The kitchen felt wrong. Or not wrong, exactly — too clean, maybe, the way
a room is clean when someone has spent the afternoon deciding what to say.
```

```text
After
The kitchen was too clean. The dish rack was empty, the counters wiped to
a shine, and her husband was sitting at the table with his hands folded,
already dressed to leave.
```

The *After* versions commit. The horror one turns a canceled guess into concrete, load-bearing detail (no tree, an even rhythm, three counted rounds) that builds dread by accumulation rather than by teasing and retracting. The domestic one drops the "or not, exactly" waffle and lets the too-clean room deliver its unease through specifics the reader can see.

## Why models do this

The self-correction mimics a searching, literary consciousness. A narrator that seems to think on the page reads as sophisticated, so the model reaches for the move to signal depth. The "maybe X; not X" bait is also a cheap way to manufacture suspense: naming a mundane explanation and denying it feels like a reveal, even when the reader never believed the mundane version.

The urge spikes when the source material is thin or ambiguous. Given a vague prompt, the model hedges rather than commits, because asserting one concrete rendering feels riskier than gesturing at several and canceling the ones it doesn't want. So the model defaults to the hedge unless something forces it to commit to a single rendering.

## Why it hurts

It undermines authorial confidence and wastes words affirming then negating the same content. The reader gets jerked back and forth, asked to hold a picture and then to erase it, and the scene loses its footing. As a habit it flattens every moment into the same tentative register, so a story that should feel authored instead feels like it is being drafted in front of you. The false-mystery form is worse than mere padding: it trains the reader to distrust the narrator's first statement, so real setups lose their power too.

## Fix it while writing with an AI

- **Ask for commitment, affirmatively.** "Write the narrator as certain. State each fact once and stand behind it. Choose one rendering of the scene and commit to it." This works better than a list of banned connectives.
- **Redirect false mystery into accumulation.** "Build unease from concrete details that stack, not from floating a normal explanation and then denying it. Don't name the boring version at all; just show the specifics that don't add up."
- **Request a self-check.** "Before finishing, scan for any place where you state something and then take it back ('or rather,' 'no —,' 'not X, exactly'). Rewrite each as a single committed statement."
- **Paste back the surviving half.** When a line reads "maybe X; it was not X," keep the true half and ask the model to rebuild the sentence around it, adding one concrete detail in the space the retraction used to fill.
- **Give it more to stand on.** If the hedging comes from ambiguity, feed a bit more grounding (who is present, what is physically in the room) so the narrator has something specific to commit to.

## Fix it while building a tool

For a prose-writing tool, add a required self-audit field to the writer's output schema. The key names the vice; the value is an affirmative snake_case commitment the model re-reads right before emitting. The gain comes from the act of re-reading the commitment, not from reliable self-detection, so keep the banned pattern in the field *description*, never as a literal example inside the enum (that plants it).

```json
{
  "pre_emit_audit": {
    "type": "object",
    "required": ["commit_to_the_frame"],
    "properties": {
      "commit_to_the_frame": {
        "type": "string",
        "enum": ["i_state_the_fact_once_and_stand_behind_it"],
        "description": "The narrator authors the scene; it does not discover it live. State each fact once. Do not float an explanation and then deny it, and do not reverse a clause with a hedging connective. Build effect from committed, concrete detail."
      }
    }
  }
}
```

Because the retracting connectives are syntactically localizable, you can back the gate with a post-generation regex check on high-confidence markers ("or rather," "no, that wasn't it," "not X, exactly," "maybe it was … it was not") that triggers a regeneration. This vice shares a root with [contrastive reveal](contrastive-reveal.md) (both spend words on a negated decoy), so fuse them into one gate rather than spawning several. See [Schema gates](../techniques/schema-gates.md) for the full pattern, [No pink elephant](../techniques/no-pink-elephant.md) for why the anti-example stays out of the enum, and [Post-hoc validation and regeneration](../techniques/post-hoc-validation-and-regen.md) for the regex-and-regenerate backstop.

Two upstream moves matter as much as the gate. First, the hedging is amplified by ambiguous input, so give the writer more concrete grounding to commit to rather than hoping the rule alone holds. Second, if the writer is fed its own recent prose as memory, the tentative register self-reinforces over a session, so feed distilled factual summaries instead and keep any raw-prose window small. See [Anti-autophagy memory](../techniques/anti-autophagy-memory.md). Keep the gate on the component that writes prose; a planner emitting factual notes should not carry prose gates. See [Separate planning from prose](../techniques/separate-planning-from-prose.md).

## Watch-outs

- **A character can legitimately self-correct.** "I saw him at, no, it was Thursday" in dialogue is a real person revising in real time and can stay. Hold the *narrator's* voice to a stricter bar than a speaker's.
- **Don't flatten all mystery.** The fix is to stop asserting-and-retracting, not to strip suspense. Genuine uncertainty rendered as concrete unexplained detail is good craft; the vice is specifically naming an explanation only to cancel it.
- **Cover every text field.** Fix it in narration and it can reappear in reasoning notes, scene summaries, or an internal-state field that later gets rendered. Any field carrying prose can carry the hedge.
- **Accept a residual.** Even strong models slip on a small fraction of passages. Chasing zero with ever-narrower rules dilutes your other gates and eats output-token headroom.
- **Sibling morphs.** The same performance-of-depth impulse can surface as a [contrastive reveal](contrastive-reveal.md), an [aphoristic closer](aphorism-and-oracle.md), or a [gesture gloss](gesture-gloss.md). Watch the whole family.

## Related

- [The core idea](../the-core-idea.md)
- [Contrastive reveal](contrastive-reveal.md)
- [Aphoristic label at the close](aphorism-and-oracle.md)
- [Psychological simile](gesture-gloss.md)
- [Recap chain](recap-chain.md)
- [Schema gates](../techniques/schema-gates.md)
- [No pink elephant](../techniques/no-pink-elephant.md)
- [Post-hoc validation and regeneration](../techniques/post-hoc-validation-and-regen.md)
- [Anti-autophagy memory](../techniques/anti-autophagy-memory.md)
- [Separate planning from prose](../techniques/separate-planning-from-prose.md)
