_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Principles Over Rules

> Teach the model the *why* behind a constraint, not a long list of forbidden cases. A short principle transfers to situations you never listed; an exhaustive rule-list overfits and leaks through every gap.

## The problem it solves

When a prose vice keeps showing up, the instinct is to add another rule. Then you add another for the variant you just saw, and a third for the variant the second rule pushed the model into. The prompt swells into a rule-list, and the vice keeps finding the case you didn't enumerate.

This technique targets that whole failure pattern, not one vice. It is the sane default for any subjective craft constraint, such as voice, register, cadence, restraint, and closing shape. In field testing, a bloated rule-list prompt hit 100% on the exact cases it was tuned against but collapsed to well under half on fresh out-of-envelope inputs; the same intent rewritten as lean principles held its ground on both. The rule-list version overfit its tuned cases and missed the fresh ones; the principle version held on both because it steered toward the operation instead of memorizing instances.

It applies most directly to the vices that come in many surface forms:

- The [contrastive reveal](../vices/contrastive-reveal.md) has a binary form, a triadic form, a fragmented form, and a compacted-into-one-clause form. Ban the ones you noticed and the model slides into the one you didn't.
- The [gesture gloss](../vices/gesture-gloss.md) rides a family of connectors ("as if," "like someone who," "the way a person does when"). Drop one from the ban and it becomes the new drain.
- [Aphorism and oracle voice](../vices/aphorism-and-oracle.md) can be a narration closer or a character's minted proverb; a rule about one leaves the other open.

It also names the boundary where the technique stops. Some problems are not craft at all but a **missing forcing function**, a needed output that no field requires. A structural gap cannot be principled or rule-listed away; it needs a required channel. Diagnosing that boundary is part of the technique.

## How it works

A principle states the *operation* to avoid or perform, phrased affirmatively, so the model can derive the right behavior for a case nobody wrote down. A rule-list states specific *instances*, so the model learns exactly those instances and nothing adjacent.

Here is the causal story, and it has two halves.

**Why rule-lists overfit.** A concrete instance in a prompt is a strong attractor. The model treats each listed case as the shape it must dodge (or copy), and generalizes narrowly around it. Enumerate twelve romance-flag triggers and the model learns those twelve; a thirteenth situation that clearly should count sails through, because it wasn't on the list. Worse, a long list dilutes attention across many low-value tokens and eats headroom, and if any listed "bad" example is quoted literally, it plants the very pattern you meant to forbid (see [anti-pink-elephant](no-pink-elephant.md)).

**Why principles transfer.** A principle names the underlying operation: "do not define a thing by first negating an alternative," "expertise shows in specific practical detail, not portable maxims." The model can apply that to any surface it produces, including forms you never anticipated. It steers toward a target instead of memorizing a blocklist.

There is one crucial refinement, and skipping it is where naive attempts fail. For strong models, a bare principle often under-delivers, because the model anchors on whatever concrete locutions you *did* mention and lets unmentioned siblings through. The reliable shape is **principle plus a complete list of the common surface faces as anchors**: the operation stated once, then a compact enumeration of the forms it wears. The principle carries the generalization; the anchor list closes the specific drains you have actually observed. Build that list from vices you have measured in real output, not from preventive guessing, and never silently drop a face when you revise it, since the dropped face is exactly where the vice reappears.

The two also fail differently, which tells you which tool fits. A syntactically localizable vice (the negation frame, an em-dash aside) responds well to a tight principle plus a [schema gate](schema-gates.md). A diffuse register vice (tone drifting cold and grim, prose flattening over a long session) is barely touched by any rule and needs the prompt's *demonstrations* and inputs fixed, not more instructions.

## Manual version (in a chat)

You do not need a codebase to work this way; in fact chat writers hit the rule-list trap constantly, one correction at a time.

When the assistant commits a vice, resist the urge to add a narrow "don't do that" and instead state the principle behind it, phrased as what to do:

> Two habits to hold for everything you write from here on:
> - State things directly. Don't define something by first denying what it isn't.
> - Show a gesture and let it stand. Don't add a clause that tells me what it meant.

That covers far more ground than a list of banned phrases, and it survives into scenes you haven't described yet.

Two working tips. First, if you must name specific forms, because the model keeps slipping into "as if" and "the way someone does when," list the common ones together under the principle, rather than mentioning one and hoping it generalizes. Second, when you catch yourself piling on the fourth or fifth correction and the prose is getting *worse*, stop and undo. Revert to the version that read best, then change one thing. Stacked corrections on a strong model often oscillate rather than converge, and you can no longer tell which instruction helped.

One honest limit for chat: if the problem is that the model keeps *omitting* something the story needs, so the world never changes on its own and a required beat never arrives, no principle fixes that. You have to ask for the missing element explicitly, every time, because there is no field forcing it. That is the manual analogue of a missing forcing function.

## Systematic version (in a tool)

Default your prompts to principles. When you must give a rule, make it one affirmative sentence at the level of the operation, and put the observed surface faces in the field's description as anchors, never as copyable literal prose. Keep concrete option-lists (economies, conflict types, name shapes) inside the generator nodes that genuinely need to pick from a menu, not in the prose writer, where a menu just becomes a new mold.

A generic gate showing the principle-plus-anchors shape:

```python
# One gate, principle-level, covering the whole negation family by operation.
pre_emit_audit = {
    "type": "object",
    "required": ["direct_assertion"],
    "properties": {
        "direct_assertion": {
            "type": "string",
            # Affirmative pledge only: the value the model re-reads.
            "enum": ["i_state_what_is_true_without_negating_a_decoy_first"],
            # Principle + anchor faces live in the DESCRIPTION, not the value.
            "description": (
                "Operation to avoid: defining a thing by first denying an "
                "alternative to stage a reveal. Common faces (all covered): "
                "binary negate-then-affirm; a triad of negations; a fragmented "
                "negation tail; the same move compacted into one comma or dash "
                "clause; and 'X, but not Y'. State the real thing directly."
            ),
        }
    },
}
```

When you rewrite a bloated rule-list into principles, keep the anti-vice core that came from real failures, and cut the accreted debris: numeric caps you invented for one worry, encyclopedic lore the model already knows, and cross-references by position ("see rule 7") that desync every time you renumber. Use named references instead.

Then prove the generalization before you ship it. Keep your original tuned cases as a non-regression baseline, and add a handful of **blind, out-of-envelope scenarios authored without the prompt in view**. A large gap between in-sample and blind-fresh scores is the overfit signature; when you see it, the answer is usually a structural change, not another round of wording. Validate against the real pipeline, not an arid harness, because empty test scenes provoke fabrication the model would never produce in live play.

Finally, classify before you fix. For each recurring defect, decide which of three it is:

```python
def route_fix(defect):
    if defect.kind == "prose_vice":        # a style tic
        return "principle + anchors, backed by a reflexive gate"
    if defect.kind == "missing_channel":   # a needed element no field requires
        return "add a required output field so it must be produced"
    if defect.kind == "stale_state":       # a description events have outdated
        return "reconcile state; do not prompt around it"
```

Treating a missing forcing function as a prose problem can burn days of prompt tuning that adding the one required field would resolve in a single edit.

## Pitfalls

- **A bare principle leaks on strong models.** Without the anchor list, the model honors the operation loosely and lets a specific unmentioned face through. Principle *and* faces, together.
- **Dropping a face when you revise re-opens it.** If a form was in the anchor list and you trim it, that form comes back. Treat the list as append-mostly, pruned only with evidence.
- **Principles do not fix diffuse register drift.** Cold, grim, flattening tone is set by what the prompt *shows* (its examples, its few-shots, the model's own re-fed output) far more than by what it states. Rewrite the demonstrations and clean the inputs; see [tonal drift](../vices/tonal-drift.md) and [anti-autophagy memory](anti-autophagy-memory.md).
- **Over-slimming loses the load-bearing bans.** The anti-vice rules earned from real failures are not clutter, so keep them. Cut invented caps and lore, not the constraints that came from watching the model fail.
- **Not every gap is a principle problem.** If the fix is "make this required," it is a forcing-function gap, not a wording gap. If it is "describe the current state, not the saved one," it is stale state. Misrouting these to the prompt is the most expensive mistake here.
- **Prove generalization, don't presume it.** In-sample success hides overfit. Test blind-fresh cases and accept a residual near the model ceiling instead of chasing zero with new rules, which only dilutes the gates that work.

## Related

- [The core idea](../the-core-idea.md)
- [No pink elephant](no-pink-elephant.md)
- [Schema gates](schema-gates.md)
- [Post-hoc validation and regeneration](post-hoc-validation-and-regen.md)
- [Ground voice in real exemplars](reference-real-exemplars.md)
- [Separate planning from prose](separate-planning-from-prose.md)
- [Contrastive reveal](../vices/contrastive-reveal.md)
- [Gesture gloss](../vices/gesture-gloss.md)
- [Aphorism & oracle voice](../vices/aphorism-and-oracle.md)
- [Tonal drift](../vices/tonal-drift.md)
