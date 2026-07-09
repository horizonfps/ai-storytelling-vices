_Part of the [AI Storytelling Vices](../README.md) field guide._

# The Core Idea: The Reflexive Forcing Function

Language models have a handful of prose habits that survive almost any instruction you throw at them. You can write "do not use the not-X-but-Y construction" in bold, in triplicate, at the top of the prompt, and the model will still hand you a scene that opens with it. This is the single most important lesson in this guide, so it gets its own document.

The durable fix is a **reflexive forcing function**: you make the model re-state, in its own output, an affirmative commitment about what it will do, *immediately before* it writes the prose. The gain comes from the act of re-reading that commitment at the moment of generation. It does not come from the model catching its own mistake, because the model almost never thinks it made one.

In field testing, forcing this re-read cut a stubborn prose vice by roughly three quarters, even though the model marked itself "clean" on essentially every single run.

## Why plain prompt rules under-deliver on prose vices

Prompt rules work well for facts and format. "Always include a scene heading," "never invent a character who wasn't in the brief," "output valid JSON": these hold, because the model can check them against a clear target and it wants to comply.

Prose vices are different for three reasons:

- **They live below the level a rule can reach.** Think of the not-X-but-Y frame, the gesture-then-gloss, and the aphorism at the end of a beat. These are deep stylistic reflexes shaped by enormous amounts of training text. A single instruction competes against that gravity and usually loses.
- **The model doesn't see them as faults.** Each individual sentence reads like competent craft. Ask the model "did you commit the interpretive-simile vice?" and it will honestly answer no, because to the model that sentence just looks like good writing.
- **A rule stated once, far from the writing, fades.** By the time the model is deep in generating a paragraph, an instruction from 3,000 tokens earlier has little pull. The vice is right there, hot and available, while the rule is cold and distant.

Stacking more prohibitions makes this worse, not better. Every extra "don't do X" adds tokens and competes for attention with the rules that matter. Worse, if you quote the bad pattern to forbid it, you actively teach the model the shape you're trying to suppress. (That failure mode has its own document: [no pink elephants](techniques/no-pink-elephant.md).)

## What a forcing function is

A forcing function is a structural requirement that makes the model *do* something before it can proceed, rather than a request it can quietly ignore. The classic engineering example is a checklist a pilot must physically complete before takeoff. The checklist doesn't detect problems on its own. It forces the pilot to look, in the moment, at the thing they'd otherwise skip.

For prose, the forcing function is a required piece of output (a field, a line, a short form) that the model must fill in with an affirmative statement of intent, positioned so it is the last thing generated before the prose itself. The model cannot get to the paragraph without passing through the commitment.

The key property is that it is not advice, and it is not a filter that runs afterward. It is a gate the model must walk through on its way to writing, every time.

## The affirmative-commitment format

The commitment has a fixed shape. The **key names the vice**, and the **value is an affirmative, snake_case pledge of what the model will do**, never a description of the bad thing.

```
"gesture_without_gloss": "i_show_the_gesture_and_stop_without_a_clause_that_interprets_it"
"direct_assertion":      "i_state_what_is_true_without_negating_a_decoy_first"
"concrete_close":        "i_end_on_a_concrete_act_not_a_noun_that_labels_the_beat"
```

Notice what is *not* in the value: no example of the vice, no "not like this," no forbidden phrase. The pledge is purely positive. This matters for two reasons. First, an affirmative target ("show the gesture and stop") gives the model something to steer toward, which works far better than a hole to avoid. Second, if you plant a copyable bad example in the value, you teach the pattern you meant to kill.

The rule itself, meaning the description of the vice and the shapes to avoid, belongs in the field's *description* (the human- and model-facing explanation attached to the field), not in the value the model echoes. Keep the value clean and affirmative, and keep the detail in the description where it informs without being parroted.

## Why the re-read, not self-detection, is the active ingredient

This is the part that surprises people. When you instrument these gates, the model marks itself compliant almost every time, including on the runs where an independent check catches the vice red-handed. The self-report is close to worthless as a signal.

And yet the vice rate drops sharply. So the mechanism cannot be "the model notices and fixes." The mechanism is the re-reading. By forcing the model to write out "I will show the gesture and stop" one beat before it writes the gesture, you prime the affirmative behavior at the exact moment it matters. The commitment is fresh, specific, and positioned right against the generation, which is everything a distant rule is not.

Think of it as loading the right intention into the model's working context at the last possible instant. The value the model types does no work on its own; the effect comes from making it generate that pledge one beat before the prose.

A practical consequence is that you should not trust the model's self-assessment as a quality gate. If you need to *measure* compliance, use an independent check (a separate reviewer pass or a targeted pattern match) rather than the model's own "ok." The commitment field is a steering device, not a detector.

## The manual version, for chat writers

You don't need a codebase to use this. If you write fiction with an AI assistant in a chat window, you can run the same forcing function by hand.

Right before you ask for a passage, or in the same message, have the model restate its style commitments as a tiny checklist, phrased as what it *will* do. Keep it to the few vices you actually struggle with. For example:

> Before you write the next scene, list your commitments for this passage as short affirmative lines, then write:
> - I show gestures and let them stand; I don't explain what they mean.
> - I state things directly; I don't set up a fake contrast to reveal them.
> - I end on a concrete action or image, not a summary of the mood.

Then let it write. You can also ask it to fill a tiny pre-write form: three keys, three affirmative pledges, then the prose. The ritual feels almost too simple, but the re-statement is doing real work. It puts the target behavior in front of the model at the moment of writing, which is exactly where a general instruction from earlier in the chat has gone cold.

Two tips. Keep the checklist short, because a wall of commitments dilutes the effect. And revisit it every few turns rather than assuming one statement covers the whole session, since over a long chat the model drifts back to its defaults and a fresh restatement pulls it back.

## The systematic version, for tool builders

If you generate prose through a structured-output or tool-call interface, make the commitment a **required field the model must fill before the prose field**.

- Add an object (call it `pre_emit_audit` or similar) to the writing tool's schema.
- Give it one required string property per stubborn vice, using the key-names-vice / value-is-affirmative-pledge format.
- Constrain each value (an enum with the single affirmative string works well) so the model can't wander off-format.
- Put the actual rule and the shapes to avoid in each property's **description**, never in the value.
- Order the schema so the audit object comes *before* the prose, so the model generates its commitments first.
- Make every field **required**. An optional field the model can skip never fires, and the whole effect depends on it firing.

The engine can discard the audit object after parsing. It is scaffolding for the generation, not data you consume. Its job is done the moment the model has written it.

For the full build-out, covering schema shape, where the gate lives, and how it interacts with caching and token budgets, see [schema gates](techniques/schema-gates.md).

## A generic schema snippet

```json
{
  "name": "write_scene",
  "input_schema": {
    "type": "object",
    "required": ["pre_emit_audit", "prose"],
    "properties": {
      "pre_emit_audit": {
        "type": "object",
        "required": ["gesture_without_gloss", "direct_assertion", "concrete_close"],
        "properties": {
          "gesture_without_gloss": {
            "type": "string",
            "enum": ["i_show_the_gesture_and_stop_without_a_clause_that_interprets_it"],
            "description": "Do not follow a gesture with a clause that names or interprets its inner meaning. Show the action and let it carry the subtext."
          },
          "direct_assertion": {
            "type": "string",
            "enum": ["i_state_what_is_true_without_negating_a_decoy_first"],
            "description": "Do not define something by first denying an alternative to stage a reveal. Assert the real thing directly. This applies whatever the punctuation."
          },
          "concrete_close": {
            "type": "string",
            "enum": ["i_end_on_a_concrete_act_not_a_noun_that_labels_the_beat"],
            "description": "Do not close on a sentence that stamps an abstract judgment onto the scene. End on a concrete image or action."
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

The `pre_emit_audit` object is listed first and marked required, so the model fills it before it reaches `prose`. Each value is a clean affirmative pledge, and each description carries the rule.

## What this looks like in practice

A quick illustration of the vice a gesture-commitment prevents. Labeled examples are for you, the reader; never copy a "before" into a prompt.

**Before** (gesture, then a clause that explains it away):
> She set the telegram face-down on the tea tray and smoothed the cloth twice, like a woman deciding how much of the truth her sister could survive.

**After** (the gesture is allowed to mean what it means):
> She set the telegram face-down on the tea tray and smoothed the cloth twice. Then she carried the tray in to her sister, and set the tea down first.

The second version trusts the reader to feel the withholding in the ordering of small acts. That trust is exactly what the commitment protects.

## The limits: ceilings, over-gating, and fusion

This technique is powerful, not magic. Three limits to hold in mind:

- **There is a model ceiling.** Even with a good gate, strong models tend to top out around 90-95% clean on the hardest vices, and the residue is stubborn. Accept it. Chasing the last few percent with ever-tighter rules costs more than it returns and often makes the prose worse elsewhere. Set your success bar at the ceiling and stop.
- **Don't over-gate.** Every commitment you add competes for the model's attention and eats into the output-token budget (the audit object is a prefix of the response). A handful of well-chosen gates works. Twenty gates become a ritual the model rushes through, and the reflexive effect dilutes. Gate the vices that actually recur in your outputs, not every vice you can name.
- **Fuse sibling vices.** Many vices share a root. Choppy fragments, obsessive triads, and verb-period-verb cadence are all one rhythm problem. Cover the family with a single commitment rather than minting one gate per surface form. Fewer, broader gates preserve the punch and save budget.

One more scoping rule: only the component that actually *writes prose* should carry prose gates. A planner or director that emits factual notes, such as who is present and what changes this turn, writes no prose, so prose commitments there are noise. Keep gates where the writing happens.

## Where to go next

- Some vices are semantic and diffuse enough that the reflexive gate only dents them, and for those you also need a check that runs *after* generation. See the [vices catalog](vices/README.md) for which vices resist gating and what to pair with them.
- For the mechanics of building the gate into a real tool schema, including caching and budget concerns: [schema gates](techniques/schema-gates.md).
- For why you must never quote the bad pattern to forbid it: [no pink elephants](techniques/no-pink-elephant.md).

The reflexive forcing function underlies every other technique in this guide. When a prose vice resists your rules, the durable move is to add a required commitment the model fills in just before the prose, so the target behavior is fresh at the moment of writing.
