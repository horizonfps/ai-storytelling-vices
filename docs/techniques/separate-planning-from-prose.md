_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Separate Planning From Prose

> Split the job of *deciding what happens* from the job of *writing it well*. The planner emits data (facts, casting, stakes, flags) and carries no prose gates. The writer turns that data into prose and carries all of them. Each half then fails in only its own way.

## The problem it solves

When one component both decides the story and writes it, two very different kinds of failure get tangled together, and neither gets fixed cleanly.

On one side, prose vices leak into structured decisions. A planning field asked for "one thing that changes in the world this turn" comes back as a paragraph of atmosphere instead of a flat fact. A casting note that should read `newcomer: dockside informant` arrives already narrated, half-written, in a voice the actual writer will then have to fight or duplicate.

On the other side, style discipline gets attached where it does nothing. You bolt a [schema gate](schema-gates.md) onto a planner that emits no prose, and it just burns output budget and dilutes the gates that matter on the writer. Worse, tuning the writer's voice does nothing when the register is already wrong in the *data it receives*: a grim premise, a somber ambient image, a trauma note phrased as a doom sermon. You can perfect the final prose stage and still ship the wrong tone, because the tone entered as a decision one stage upstream.

This technique is the structural precondition for most of the rest of the guide. It targets:

- Prose vices bleeding into factual fields, such as [tonal drift](../vices/tonal-drift.md) seeded in a premise or [register and anachronism leak](../vices/register-and-anachronism-leak.md) in a scene note.
- Wasted or misplaced style discipline, like [reflexive gates](schema-gates.md) on a non-writing component.
- Register set in the input rather than the writer, which is why [principles over rules](principles-over-rules.md) alone can't fix a grim-by-construction plan.

## How it works

The mechanism is a clean division of labor across two roles, and it works because prose vices and planning vices have different roots and different fixes.

**The planner decides; its fields are data.** Scene, present cast, outcome, what changed, which channels fired: flat, enumerable, checkable values. Because these fields are consumed by machinery downstream (a router, a state store, the writer's briefing), they benefit from *factual* discipline. Restate the inputs literally, keep control-plane vocabulary out of anything diegetic, and force a decision where one is required. They do **not** benefit from prose commitments, because the planner is not writing prose.

**The writer renders; its fields are prose.** It receives the plan as fact and turns it into a scene. This is the only place the [reflexive gates](schema-gates.md) belong, because this is the only place prose exists.

Why the split earns its keep: a vice fixed in the writer will reappear one layer up if the same model powers the planner. The planner has the same latent pull toward a cold, withholding, "prestige" register, and left unchecked it manufactures grim premises the writer then dutifully dramatizes. Chasing that only in the writer never converges. Once the roles are separate, you apply *register discipline to the plan* (honor the supplied brief, phrase a trauma note with agency in it, strip any forcer that pushes the fiction dark) and *prose discipline to the writer*, each with the tool that fits it.

There is a subtle failure the split alone does not close, and it is worth naming. A planner told to "stage the scene" or "render the moment" will drift into narration no matter how factual its other fields are. The fix is not just a rule; it is giving technical state its own home. When a planner has nowhere to put IDs, counters, and the *reasoning* behind a hook, it smuggles them into the scene-facing field as prose. Add a separate metadata field for that state, and the scene-facing field stays a clean diegetic fact.

## Manual version (in a chat)

You can run both roles yourself, in sequence, in one chat, and separating them is one of the highest-leverage habits a solo writer can build.

Do the planning pass first, and demand plain notes, not sentences you would want in the draft:

> Before writing, give me a short factual outline for the next scene. No prose. Just:
> - where it happens and who is present
> - what each present character wants right now
> - the one thing that shifts by the end
> - any hook that should enter, stated as a plain fact

Then write from that outline in a separate turn, and apply your style checklist *only* to that turn:

> Now write the scene from the outline. Hold these while you write: state things directly; show gestures and let them stand; end on a concrete action.

The reason to split the turns is that the two mindsets pull against each other. Deciding wants breadth and bookkeeping; writing wants voice and restraint. Braided together, you get outlines that are secretly purple and prose that keeps stopping to re-plan. Split, each pass does one job well.

One more manual move that pays off: when the prose keeps coming out in the wrong register even after you fix your writing instructions, look at the *outline* you handed it. If the plan says "the town is hollow, everyone is broken," the prose will be bleak no matter what you tell the writer. Rewrite the plan toward agency, for instance "the town survived a hard season and is loudly reinventing itself," and the prose follows.

## Systematic version (in a tool)

Give the two roles two tools. The planner's schema is factual fields plus, where input fidelity matters, a restatement scratchpad. The writer's schema carries the `pre_emit_audit` prose gates. Do not cross them.

```python
# PLANNER TOOL: factual only. No prose gates.
plan_turn = {
    "name": "plan_turn",
    "input_schema": {
        "type": "object",
        "required": ["scene", "present_cast", "what_changed",
                     "world_pulse", "meta"],
        "properties": {
            "scene": {
                "type": "string",
                "description": "Where the scene happens, as a plain fact. "
                               "Diegetic only: no IDs, counters, or engine terms.",
            },
            "present_cast": {
                "type": "array",
                "items": {"type": "string"},
                "description": "Character IDs on stage this turn.",
            },
            "what_changed": {
                "type": "string",
                "description": "The one thing that shifts this turn, stated flat. "
                               "A fact for the writer to stage, NOT narration.",
            },
            "world_pulse": {
                # Forcing function: the world must move or explicitly hold,
                # decided fresh every turn. 'hold' is legitimate.
                "type": "string",
                "description": "Emit an off-screen development, or hold. State which "
                               "and why in one factual line. Holding is fine and common.",
            },
            "meta": {
                # Home for control-plane state so it never leaks into `scene`.
                "type": "object",
                "description": "Technical state: event kind, counters, flags, IDs.",
            },
        },
    },
}

# WRITER TOOL: carries the prose gates. Renders the plan.
write_scene = {
    "name": "write_scene",
    "input_schema": {
        "type": "object",
        "required": ["pre_emit_audit", "prose"],
        "properties": {
            "pre_emit_audit": {
                "type": "object",
                "required": ["direct_assertion", "gesture_without_gloss",
                             "concrete_close"],
                "properties": {
                    "direct_assertion": {
                        "type": "string",
                        "enum": ["i_state_what_is_true_without_negating_a_decoy_first"],
                        "description": "Assert the real thing directly; do not define "
                                       "it by first denying an alternative.",
                    },
                    "gesture_without_gloss": {
                        "type": "string",
                        "enum": ["i_show_the_gesture_and_stop_without_interpreting_it"],
                        "description": "After an action, add no clause that reads the "
                                       "character's inner state.",
                    },
                    "concrete_close": {
                        "type": "string",
                        "enum": ["i_end_on_a_concrete_act_not_a_labeling_noun"],
                        "description": "Close on an image or action, not a maxim.",
                    },
                },
            },
            "prose": {"type": "string", "description": "The scene."},
        },
    },
}
```

Three engineering notes that decide whether the split actually holds:

- **Keep the planner's scene-facing fields diegetic, and give technical state a `meta` home.** The diegesis rule alone under-delivers; the leverage is the separate destination. Without it the planner keeps narrating the *deliberation* of a hook into the scene field because it has nowhere else to reason.
- **Instruct the planner to *signal*, never to *stage*.** "Signal the hook through the factual channels; the writer stages it" keeps it out of prose. Words like "stage" or "render the scene" invite narration.
- **Apply register discipline to the plan, subtractively.** If the premise generator runs the same model, de-grim its inputs and make it honor a supplied brief. Prefer removing a forcer that pushes the fiction dark over adding a new gate, since a planner gains nothing from prose gates, and a forced-transition rule usually collides with other rules.

## Pitfalls

**Do not gate the planner.** Attaching prose commitments to a component that emits factual notes is pure overhead: it spends output-token budget and dilutes the writer's gates without changing any prose, because there is no prose to change.

**Watch factual fields the writer copies verbatim.** A location note or a hook line that the writer renders word-for-word can carry a vice into the scene even though "the planner isn't writing prose." Hold those specific pass-through fields to the same abstract-fact discipline, and never seed them with a copyable purple example. A planner will replicate a vivid bad sample verbatim, the [pink-elephant](no-pink-elephant.md) trap.

**The diegesis rule alone is not enough.** If you forbid engine vocabulary in the scene field but give technical state no other home, it bleeds back in. The destination field is the real fix, not the prohibition.

**Separation exposes an upstream register problem; it does not create one.** After you split the roles, a still-grim tone means the *plan* is grim. That is a feature, because you now know where to look, but only if you actually go look. Fixing the writer again will not help.

**Do not over-separate a simple pipeline.** Two roles is the useful cut. Slicing planning into five micro-stages adds handoffs and latency for no gain; keep the split at the seam where "decide" and "write" genuinely diverge.

## Related

- [The core idea](../the-core-idea.md)
- [Schema gates](schema-gates.md)
- [Principles over rules](principles-over-rules.md)
- [No pink elephant](no-pink-elephant.md)
- [Ground voice in real exemplars](reference-real-exemplars.md)
- [Tonal drift](../vices/tonal-drift.md)
- [Register & anachronism leak](../vices/register-and-anachronism-leak.md)
- [Inert world](../vices/inert-world.md)
