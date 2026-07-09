_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Reflexive Schema Gates

> A required field in the writer's output that makes the model re-state an affirmative commitment right before it writes. The re-read is the whole mechanism, not the model's self-check.

## The problem it solves

A handful of prose vices survive plain instructions no matter how firmly you state them. You can write "never use the not-X-but-Y construction" in bold at the top of the prompt, and the model still opens the scene with it. The vice lives below the level a rule can reach, and (this is the surprising part) the model does not believe it committed the fault, because each sentence reads like competent craft.

Reflexive schema gates are the standard, portable fix for the *syntactically localizable* members of this family:

- [Contrastive reveal](../vices/contrastive-reveal.md): the "not X, but Y" definition-by-negation.
- [Narrate-then-unnarrate](../vices/narrate-then-unnarrate.md): assert a thing, then hedge it away.
- [Gesture gloss](../vices/gesture-gloss.md): show an action, then explain its inner meaning.
- [Aphorism and oracle](../vices/aphorism-and-oracle.md): close a beat on a portable maxim.
- [Dialogue fragmentation](../vices/dialogue-fragmentation.md): choppy hard-stop bursts and obsessive triads.

The gate is the backbone technique of this whole guide; the reasoning behind it lives in [The Core Idea](../the-core-idea.md). This document is the build-out: what the field looks like, where it goes, and how it fails when you overuse it.

One boundary up front: gates work on vices with a recognizable surface shape. For *diffuse* register problems, such as [tonal drift](../vices/tonal-drift.md) toward a grimmer or flatter voice, a gate marks itself clean and changes nothing. Those need prompt and exemplar changes plus [post-hoc validation](post-hoc-validation-and-regen.md). Reach for a gate when there is a phrase-level pattern to steer away from.

## How it works

The gate is a required field in the writer's structured output. The **key names the vice**; the **value is a fixed, affirmative, snake_case pledge** of what the model will do. The model must produce this field *before* it produces the prose, so the last thing it writes before the scene is a positive statement of intent.

The causal story is counter-intuitive, so it is worth stating plainly: **the gain comes from the act of re-reading the commitment, not from the model detecting its own vice.** When you instrument these gates, the model marks itself compliant on nearly every run, including the runs where an independent check catches the vice red-handed. The self-report is close to worthless as a signal. Yet the vice rate drops sharply, often by around three quarters.

So the mechanism is not "the model notices and corrects." It is priming. A rule stated 3,000 tokens earlier has gone cold by the time the model is deep in a paragraph; the vice is hot and available, the rule is distant. Forcing the model to type "i state what is true without negating a decoy first" one beat before it writes reloads the right intention into working context at the exact moment it matters. The value's content does no work; the effect comes from making the model generate the pledge one beat before the prose.

Three properties make this work, and each is load-bearing:

- **Affirmative, not prohibitive.** The pledge names what to *do* ("i end on a concrete act"), giving the model a target to steer toward rather than a hole to avoid.
- **Required, not optional.** An optional field the model can skip never fires, and the whole effect depends on it firing.
- **First, before the prose.** The commitment has to be fresh at the moment of writing, which means it has to be generated last-before-the-scene.

The rule and the shapes to avoid live in the field's *description*, never in the value the model echoes. If you plant a copyable bad phrase in the value, you teach the pattern you meant to kill, which is the [anti-pink-elephant](no-pink-elephant.md) failure mode.

## Manual version (in a chat)

You do not need a schema. In a chat window, you run the same forcing function by hand: ask the model to restate its commitments as a tiny affirmative checklist in the same message that requests the passage, then let it write.

Keep it to the two or three vices you actually struggle with, phrased as what the model *will* do:

> Before you write the next scene, list these commitments as short affirmative lines, then write the scene:
> - I show gestures and let them stand; I carry meaning through action or another character's reaction.
> - I state things directly; I do not set up a fake contrast to reveal them.
> - I end on a concrete action or image, not a line that names the mood.

The ritual feels almost too small to matter, and the model will breeze through it claiming full compliance. Ask for it anyway. The restatement puts the target behavior in front of the model right where a general instruction from earlier in the chat has gone cold.

Two tips. Keep the list short, because a wall of commitments dilutes the effect. And repeat it every few turns: over a long session the model drifts back to its defaults, and a fresh restatement pulls it back.

## Systematic version (in a tool)

Add an object, which you can call `pre_emit_audit`, to the writing tool's schema. Give it one required string property per stubborn vice. Constrain each value to a single-member enum holding the affirmative pledge, so the model cannot wander off-format. Put the actual rule in each property's `description`. List the audit object *before* the prose field, and mark every field required. The engine discards the object after parsing; it is scaffolding for generation, not data you consume.

```json
{
  "name": "write_scene",
  "input_schema": {
    "type": "object",
    "required": ["pre_emit_audit", "prose"],
    "properties": {
      "pre_emit_audit": {
        "type": "object",
        "required": ["direct_assertion", "gesture_without_gloss", "concrete_close"],
        "properties": {
          "direct_assertion": {
            "type": "string",
            "enum": ["i_state_what_is_true_without_negating_a_decoy_first"],
            "description": "Assert the real thing directly. Do not define something by first denying an alternative to stage a reveal, in any punctuation. This covers the sibling shapes: define-by-negation, affirm-then-negate-the-opposite, and stacked negations. Applies to reasoning and note fields too, not only the scene."
          },
          "gesture_without_gloss": {
            "type": "string",
            "enum": ["i_show_the_gesture_and_stop_without_a_clause_that_interprets_it"],
            "description": "After a physical action, do not append a clause that reads the character's inner state. Let the gesture carry the subtext through the action itself or another character's reaction. Physical similes are fine; only the psychological gloss is banned."
          },
          "concrete_close": {
            "type": "string",
            "enum": ["i_end_on_a_concrete_act_not_a_noun_that_labels_the_beat"],
            "description": "Close on a concrete image or action. Do not end on a portable maxim or a sentence that stamps an abstract judgment onto what the scene already showed."
          }
        }
      },
      "prose": {
        "type": "string",
        "description": "The scene."
      }
    }
  }
}
```

A few implementation notes that decide whether this holds up:

- **Required is not decoration.** In JSON Schema, a property the model can omit will be omitted, and the gate silently does nothing. List every audit key in `required`.
- **The enum value stays clean.** One affirmative string, no anti-example. The forbidden shapes go in the description where they inform without being parroted.
- **Cover every prose-bearing field.** Vices migrate. Close the contrastive reveal in dialogue and it reappears in a reasoning field or a relationship-change note. The gate's declared coverage should name those fields.
- **Only prose-writers carry prose gates.** A planner or director that emits factual notes writes no prose, so prose commitments there are noise that burns budget. See [planner/prose separation](separate-planning-from-prose.md).
- **Mind caching and restart.** The gate is part of the static tool schema, so keep it in the cached prefix if you cache. And a schema change is a code change: it usually needs a process restart to take effect, unlike a prompt file read fresh from disk each call.

For syntactic vices, this is often enough. For semantic ones (the psychological simile, the aphorism), pair the gate with [post-hoc validation and regeneration](post-hoc-validation-and-regen.md). The gate cleans roughly a third of those on its own, and the detector closes the rest.

## Pitfalls

**Do not trust the self-mark as a signal.** The model reports "ok" even when it just committed the vice. The value is a steering device, never a detector. If you need to *measure* compliance, use an independent reviewer pass or a targeted pattern match, never the model's own verdict.

**There is a real ceiling.** On the strongest models, a good gate tends to top out around 90-95% clean on the hardest vices; the residue is stubborn. Accept it. Set your success bar at the ceiling and stop. Chasing the last few percent with ever-tighter rules costs more than it returns and usually makes the prose worse elsewhere, and it tempts you to quietly loosen your test harness to force a green, which hides the real residue.

**Do not over-gate.** Every commitment competes for the model's attention and eats output-token budget, since the audit object is a prefix of the response. A handful of well-chosen gates keeps its punch. Twenty gates become a ritual the model rushes through, and the reflexive effect dilutes. Gate the vices that actually recur in your outputs, not every vice you can name.

**Fuse sibling vices.** Choppy fragments, obsessive triads, and verb-period-verb cadence are one rhythm problem; cover the family with a single commitment rather than minting a gate per surface form. Broaden a gate's description to enumerate the siblings it covers. Fuse only genuine siblings, though: a gate that covers ten unrelated things stops functioning as a focused re-read.

**Keep the anti-example out of the enum.** A literal bad phrase in the value teaches the shape you are suppressing. Rules and forbidden patterns belong in the description, stated as an abstract shape rather than a copyable instance. This is the [anti-pink-elephant](no-pink-elephant.md) discipline.

**Remove inert gates.** If a gate does nothing on measurement, as gates do against diffuse register drift, take it out. A dead gate is not free; it dilutes the ones that work and spends budget. When a vice resists the reflexive re-read, the next move is a different tool, not another gate.

## Related

- [The core idea](../the-core-idea.md)
- [No pink elephant](no-pink-elephant.md)
- [Post-hoc validation and regeneration](post-hoc-validation-and-regen.md)
- [Separate planning from prose](separate-planning-from-prose.md)
- [Contrastive reveal](../vices/contrastive-reveal.md)
- [Gesture gloss](../vices/gesture-gloss.md)
- [Aphorism & oracle voice](../vices/aphorism-and-oracle.md)
- [Tonal drift](../vices/tonal-drift.md)
