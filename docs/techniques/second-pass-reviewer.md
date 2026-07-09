_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Second-Pass Reviewer

> A separate reading pass that checks the whole work for continuity breaks, agency theft, and residual vices before anything is committed. It stays grounded in the full current state, not a thin summary, and it judges with the right heuristics at the right temperature.

## The problem it solves

Some failures cannot be caught at the moment of writing. A reflexive [schema gate](schema-gates.md) fires before the prose exists, so it can steer voice and cadence, but it is blind to whether the sentence contradicts something three scenes back, or whether the writer just seized a choice that belonged to the reader. Those defects are only visible once you can read the finished passage against everything around it.

That is the reviewer's job. It targets the failure modes that need context to see:

- **Stale-state description**: the prose keeps rendering a detail that events already changed, because the writer trusts a saved record over the live scene.
- [Speaking for the protagonist](../vices/speaking-for-the-protagonist.md), where the narrator authors the point-of-view character's decisions or dialogue in interactive fiction.
- Residual [onomatopoeia spam](../vices/onomatopoeia-spam.md) and repetition that a suppressor gate at the writer would over-correct into a total ban.
- Diffuse semantic vices, such as the [psychological simile](../vices/gesture-gloss.md) and the [aphorism](../vices/aphorism-and-oracle.md), that the gate's re-read only partly closes.

The reviewer is the backstop that lets the writer's gate list stay small. Push every hard-to-detect check onto a downstream pass, and the writer keeps its focus on the phrase-level vices it can actually re-read its way out of.

## How it works

A separate model call reads the writer's output plus the current facts, and either passes it or flags issues for a targeted regeneration. Three design choices decide whether it works.

**It reads the whole work in scene, freshly loaded.** The single most common way to break a reviewer is to feed it a thin catalog (an id, a name, a type) instead of the complete current record of every entity on stage. A catalog cannot catch a stale description, because the contradicting detail lives in the full field the catalog dropped. Load the complete card for every character present, plus the point-of-view character, straight from current state after the turn resolves, not the values as they stood when the turn began. Trim deep history to fit the budget, never the descriptive fields the prose actually renders.

**Its heuristics are category-aware.** A global text rule produces brutal false positives. "Usually" and "always" are a vice in an authorial statement of fact and perfectly fine in a line of reported dialogue where a character asserts something. A present-tense verb is a problem in past-tense narration and expected in a ledger field that logs facts. Pass each item's category into each rule and scope the rule to the categories where it applies, or the reviewer drowns real signal in noise and tempts you to loosen the bar just to make it stop shouting.

**It runs at the right temperature for its role.** A reviewer doing qualitative-categorical work, meaning it sorts output into labeled buckets, tags, and routes, behaves best around 0.7, which is counter-intuitive. Drop it to 0.5 expecting a more careful, conservative pass and it commits harder to a single top-1 reading that is sometimes the wrong one; the run-to-run variety at 0.7 absorbs boundary cases without breaking schema discipline. A reviewer that only has to pick one action can sit lower.

Why a second pass at all, rather than a smarter writer? Because generation and judgment pull in opposite directions. The writer is deep in the flow of one paragraph and cannot also hold the whole continuity in view; the reviewer does nothing but hold the whole continuity in view and has no paragraph to protect. Splitting the roles gives each a clean job.

## Manual version (in a chat)

You already do this when you edit, but do it as a *named, separate pass* with one job, not as a vague "make it better."

After the model drafts a scene, open a fresh message and give it a single lens:

> Now read that scene only as a continuity editor. Here are the current facts about everyone in it: [paste the up-to-date descriptions]. Check the prose against these facts. Flag anything that contradicts them, anything where you decided something on my character's behalf, and any image or sound effect you leaned on twice. List the issues. Do not rewrite yet.

Two things make this work. First, paste the *current* facts, not your memory of them, because the reviewer can only catch a stale shackle or a healed wound if it can see that the character was freed or bandaged. Second, keep the lens narrow. A pass hunting for three specific problems finds them; a pass told to "improve the writing" wanders and rewrites voice you liked.

Run it as its own turn so the model is reading, not composing. When it composes and edits in the same breath, it protects its own choices.

## Systematic version (in a tool)

Build the reviewer's context from a fresh reload of full records, run category-scoped checks, and on any hit, hand the writer the specific issues for a targeted regeneration.

```python
def build_review_context(scene, store):
    # Full current cards for everyone on stage, reloaded after the turn.
    # Truncate deep logs for budget; keep the descriptive fields intact.
    return {
        "scene_cards": [store.full_card(cid, log_tail=6)
                        for cid in scene.present_ids],
        "player_card": store.full_card(scene.player_id),
        "recent_events": store.event_summary(scene.id),
        "prose": scene.draft,
    }

# Category-aware rule: the item carries its own category,
# and each rule declares the categories it applies to.
def rule_no_authorial_generalization(item):
    if item.category not in {"world_fact", "character_trait", "relationship"}:
        return None  # "usually/always" is fine in reported dialogue
    if is_generalized(item.text):
        return Issue("authorial_generalization", item)
    return None

def review(ctx, temperature=0.7):
    # Qualitative-categorical work: 0.7 is the sweet spot, not 0.5.
    verdict = judge_model(ctx, temperature=temperature)
    issues = [i for rule in RULES for i in [rule(x) for x in ctx["items"]] if i]
    return verdict.issues + issues

def commit_or_regen(scene, store, max_passes=2):
    for _ in range(max_passes):
        ctx = build_review_context(scene, store)
        issues = review(ctx)
        if not issues:
            return scene
        scene.draft = writer.regenerate(scene, issues)  # targeted, not blind
    return scene  # accept residual at the ceiling
```

Notes that decide whether it holds:

- **Full cards, reloaded post-turn.** The reviewer audits the state as it is *now*. Reading turn-start values re-introduces the drift you are trying to catch.
- **Scope every rule.** A rule with no category filter is a false-positive factory. Isolate the main verb for tense checks (cut at subordinators and dashes); validate a cited attribute against a known set rather than attributing it to the nearest name.
- **Narrow the remit.** A reviewer told to catch named vices catches them; one told to "improve quality" homogenizes voice and over-edits. List the checks it owns.
- **Give it the whole scene for excess vices.** Sound-effect density and repeated imagery can only be judged across the passage, which is exactly why they belong here rather than in a suppressor gate at the writer.

## Pitfalls

**The judge has a ceiling, and it is noise near the boundary.** When a check hinges on a near-homograph, such as a natural-language word versus a forbidden system label spelled almost the same, the judge will flip run-to-run on identical outputs. That flipping is calibration noise, not signal. Refine the judge's definition once, anchored to the literal spec, confirm it still fails a real violation, and then close the criterion with a triad: a structural pattern match against the actual forbidden token, a declared human spot-check, and an explicit design decision. Do not keep tuning the judge until failures hit zero. That is overfitting the judge, and it often means the model under test was right while the judge was the unreliable party.

**Do not relax the bar to force a green.** A noisy reviewer tempts you to lower the success threshold so the numbers look clean. Accept the documented residual instead. On the hardest vices the true ceiling on strong models sits in the low nineties; a green obtained by loosening the harness hides real defects.

**A thin catalog blinds it.** This is worth repeating because it is the default mistake: an id-and-name list cannot catch descriptive drift or full-record contradictions. Give the reviewer the complete current state of everything on stage.

**Scope the reviewer by output kind.** A recap, an epilogue, or a "previously" opener is *supposed* to summarize prior beats. If the reviewer flags legitimate summary as a recap vice, gate its rules by the declared kind of the passage.

**Keep the writer's gate for what it does well.** The reviewer is a backstop, not a replacement. Syntactic vices are cheaper to steer at the writer with a re-read gate than to detect and regenerate afterward. Use the reviewer for the context-dependent failures (continuity, agency, excess, semantic residue), and leave the phrase-level shapes to the gate.

## Related

- [Schema gates](schema-gates.md)
- [Post-hoc validation and regeneration](post-hoc-validation-and-regen.md)
- [Separate planning from prose](separate-planning-from-prose.md)
- [Speaking for the protagonist](../vices/speaking-for-the-protagonist.md)
- [Register & anachronism leak](../vices/register-and-anachronism-leak.md)
- [Gesture gloss](../vices/gesture-gloss.md)
- [Onomatopoeia spam](../vices/onomatopoeia-spam.md)
