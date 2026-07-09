_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Everything Is a Crisis

> Every scene defaults to threat, conflict, or extortion. The model can't let a place just be a place or a moment just be a moment, so quiet, texture, and stakes variety all vanish under one relentless key of danger.

**Also known as:** conflict-default drift, crisis monoculture, extortion-plot reflex, stakes flattening, tension saturation, loss of slice-of-life, one-note-danger world.
**Family:** structural

## The tell

Look across several generated scenes at once, not one at a time. A single tense scene is good writing; a world where *every* scene is tense is the vice. The failure is in the aggregate.

The signature patterns:

- **The conflict engine repeats.** New town, same trouble: someone is being extorted, someone is missing, a strongman wants tribute, a threat looms over the harbor. Swap the proper nouns and it's the same plot mold every time.
- **Ambient danger with no off switch.** Idle moments get seeded with menace. A character buying bread is really being watched; a friendly greeting hides a warning. The model can't write a beat that is simply pleasant or simply dull.
- **Escalation as reflex.** Stakes only ratchet up. Nothing resolves into calm, no scene lets the pressure drop, no beat exists just to breathe, celebrate, rest, or wander.
- **Tragic backstory becomes a doom sermon.** Any sad detail in a character's history gets amplified into an omen. A hard season in a town's past turns into "the place no one returns from."
- **Texture starves.** Because every scene is about the threat, places lose their economy, their food, their weather, their gossip: all the specific non-plot detail that makes a world feel lived-in.

**The variety test.** List the last five scenes in one line each: what is the actual engine of each? If the answer is "danger / danger / danger / danger / danger," you've collapsed the stakes axis. A living story rotates: a negotiation, a quiet reunion, a market haggle, a festival, a genuine threat.

**The slice-of-life test.** Ask whether any recent scene could exist with *no* menace at all: two friends arguing about a recipe, a long uneventful crossing, a small kindness. If the model can't produce one, it has lost the register entirely.

## Before / after

**Historical / period**

```text
Before (viced)
The market at dawn was thin and watchful. Vendors kept their eyes low as
the reeve's men passed, and every price whispered across the stalls
carried the weight of a debt that could not be paid. Somewhere a child
was crying, and no one dared to comfort it.
```

```text
After
The market at dawn smelled of hot fat and river mud. The pie-woman was
already three sales ahead of her rival across the lane and made sure he
heard about it. A boy hawked cures for a cough nobody had; two farmers
argued the going rate for a goose until they were both laughing.
```

**Space sci-fi**

```text
Before (viced)
The relay station hung silent against the dark. Something had gone wrong
here: the crew felt it the moment they docked. The corridors waited.
Whatever had emptied this place was still close, and it would find them
if they lingered.
```

```text
After
The relay station smelled of burnt coffee and machine oil, and the
docking clamps still worked, which the pilot took as a personal victory.
A maintenance drone bumped politely past their boots on its rounds. The
duty log showed the last crew had left in a hurry. Payday, it turned
out, two systems over, and they'd taken the good chairs with them.
```

Each "after" refuses the default menace and lets the place have a texture of its own: rivalrous vendors, a haggle over a goose, stolen chairs and a payday. Nothing bad has to be happening for a scene to be alive, since the specificity carries it. A threat dropped into a place that already has its own small routines then reads as a genuine break in a living world.

## Why models do this

Conflict is the most reinforced story-shape in the training data. "Every scene needs conflict" is near-universal writing advice, and dramatic, high-stakes prose is over-represented in the fiction models learned from, so when the model reaches for "make this interesting," danger is the path of least resistance. Calm, textured, low-stakes writing is rarer and quieter in the corpus, so the model treats it as absence rather than as a register in its own right.

Three structural triggers make it worse:

- **A single positive example becomes a mold.** If a prompt illustrates "a good tense scene," the model treats that as the canonical shape and reproduces its stakes level everywhere. The example teaches the crisis, not just the craft.
- **The register lives in the input, not just the writer.** In a multi-stage pipeline, an upstream planner often invents the premise. If it hands the writer a grim brief such as "extortion racket, paranoid townsfolk," the writer faithfully dramatizes exactly that. Fixing the prose prompt does nothing, because the crisis entered as data one stage earlier.
- **Sparse context invites fabrication.** When a scene has little to work with, the model fills the vacuum with the most dramatic thing it can imagine. Emptiness gets read as a cue to manufacture threat.

This is a diffuse register-and-structure problem, not a single bad sentence, which is why a reflexive self-check on the prose alone barely touches it.

## Why it hurts

A world where everything is a crisis is exhausting and, paradoxically, low-stakes. When every scene is an emergency, none of them feel like one, because the reader stops believing the danger when it never varies or resolves. Tension only registers against calmer beats; when every scene is already at maximum, a new threat has nothing to rise above, and the reader stops telling danger from the baseline.

It also flattens the world into a single mood. Places stop having economies, cultures, weather, and small pleasures, so they all rhyme. Characters stop having lives outside the plot. For a story meant to feel adventurous, warm, or wondrous, the crisis-default silently swaps the genre for a grim one the author never chose.

In interactive fiction the cost compounds. A player who came to explore, trade, befriend, and wander instead gets marched from threat to threat with no room to breathe. The world stops feeling like a place they inhabit and starts feeling like a gauntlet, and the variety that makes a long session worth playing evaporates.

## Fix it while writing with an AI

- **Name the register you want, affirmatively.** "Most scenes should have their own texture, whether a market, a meal, a quiet crossing, or a small rivalry, and vary what drives each one. Save real danger for when it's earned, and let it land because it's rare."
- **Ask for a stakes rotation.** "Across the next several scenes, deliberately vary the engine: one negotiation, one moment of rest or celebration, one bit of everyday friction, one genuine threat. Never run the same conflict type twice in a row."
- **Request texture directly.** "Tell me what makes *this* place specific, its work, its food, its weather, what people gossip about, before you decide whether anything is wrong here. Some places are just places."
- **Correct a grim brief at the source.** If the premise itself is bleak, rewrite it: "Reframe this town so it survived a hard season and is loudly rebuilding, with humor and stubbornness, rather than a cursed place everyone fears."
- **Ask for a slice-of-life beat on purpose.** "Give me one scene with no menace at all, just two people and an ordinary problem." If the model can't, that's your diagnosis.

## Fix it while building a tool

This is largely a diffuse register-and-wiring problem, so a self-audit gate on the prose writer is inert against it. The real levers are the input, the upstream stages, and a forcing function for variety.

**Apply register discipline to the planner, not just the writer.** The stage that invents premises usually runs the same model with the same crisis-default, so it manufactures grim briefs the writer then dramatizes. Steer it toward varied, textured, agency-forward premises and rephrase trauma fields toward living-with-and-rebuilding rather than doom.

```python
# Upstream premise/plot generator: steer the register at the source.
PLACE_BRIEF_RULE = (
    "Describe this place by what it actually is: its economy, culture, "
    "food, weather, and daily friction. Not every place has a problem; "
    "vary the driver of each location. When there is trouble, make its "
    "kind different from recent locations, never a repeat of extortion "
    "or a missing person. Render hard history as living, with people "
    "rebuilding in their own way, not as an omen."
)
```

**Add a forcing function so variety is a required decision.** If the only channel that varies stakes is optional, the model rarely uses it. Make the planner state, every scene, what drives this beat, and let "calm / texture / rest" be a legitimate, frequent answer. Nothing is auto-scheduled; the model still chooses, but it must choose consciously.

```python
# Planner schema: a required field forces an explicit stakes-and-driver choice.
"scene_driver": {
    "type": "string",
    "description": (
        "State what actually drives this scene and why. Legitimate and "
        "frequent options include rest, celebration, trade, reunion, "
        "everyday friction, or wonder; danger is one option among many, "
        "not the default. Vary it against the recent scenes."
    )
}
```

**Wire anti-repetition history into every generation path.** Pass the recent conflict types and economies so the generator can actively diverge; a path that silently receives an empty history will drift back to the mold. Keep concrete option lists (economy types, conflict axes) *only* in the generator nodes that need to make a choice, never in the prose writer's prompt, where an enumerated list becomes a new mold of its own.

**Keep the crisis out of your prompts.** Don't seed the prose writer with a vivid literal example of a tense scene, since the model reproduces the stakes level it's shown. Use positive, structural instructions and, if you need grounding, real varied source passages rather than an invented dramatic sample. See [No pink elephant](../techniques/no-pink-elephant.md).

**Diagnose before theorizing.** Before blaming the model, measure the real distribution in a saved session: what fraction of recent scenes are danger-driven, and did the variety channel ever fire? A dead channel is a wiring gap, not a design choice. See [Separate planning from prose](../techniques/separate-planning-from-prose.md) for keeping planner and writer honest, and [Ground voice in real exemplars](../techniques/reference-real-exemplars.md) for grounding texture on real references.

## Watch-outs

- **Don't over-correct into a world with no stakes.** The target is variety, not the removal of danger. A story that is all festivals and haggling is as flat as one that is all threat. Keep genuine tension in the rotation, just make it earn its place.
- **Don't force a fixed schedule.** Mandating "a calm scene every third beat" is the deterministic-content trap in a new costume. Give the model the choice and require it to choose; don't make the choice for it.
- **Texture lists belong in the planner, not the writer.** An enumerated menu of economies or conflict types placed in the prose prompt becomes a fresh mold, and every place turns into the same three options. Keep such lists only where a concrete choice is being made.
- **Watch the input, not just the output.** If crisis persists after you fix the writer, suspect the upstream brief. A lone grim detail in a scene seed gets amplified into the whole scene's mood.
- **Accept a residual.** Even with good wiring, the model's pull toward conflict never fully disappears; there's a real ceiling. Push the default toward variety, measure the distribution, and stop chasing perfection with more rules.
- **Sibling morphs:** the same performance-of-gravity impulse leaks out as an [aphorism or oracle line](aphorism-and-oracle.md) at a scene's close, as grimdark-flavored word choice, or as a [narrate-then-unnarrate](narrate-then-unnarrate.md) hedge when the model can't commit to a calm frame. Fix the stakes rotation and these lose much of their pull.

## Related

- [The core idea](../the-core-idea.md)
- [Aphorism & oracle voice](aphorism-and-oracle.md)
- [Narrate-then-un-narrate](narrate-then-unnarrate.md)
- [Voice homogeneity](voice-homogeneity.md)
- [Separate planning from prose](../techniques/separate-planning-from-prose.md)
- [No pink elephant](../techniques/no-pink-elephant.md)
- [Ground voice in real exemplars](../techniques/reference-real-exemplars.md)
- [Schema gates](../techniques/schema-gates.md)
