_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Inert World

> The setting never changes unless the protagonist touches it. Off-screen, nothing moves: no weather turns, no faction shifts, no rumor spreads, no shop opens or closes on its own. The world holds its breath between the hero's actions, waiting to be acted upon, and everything in frame orbits the point-of-view character as if the rest of the place were painted scenery.

**Also known as:** static world, protagonist-only gravity, dead ambient life, world-on-pause, reactive-only setting, no off-screen clock, scenery that waits.
**Family:** worldbuilding

## The tell

The world only ever responds; it never initiates. Every change traces back to something the protagonist just did, and the moment the protagonist stops, the setting freezes. Places have no economy the hero isn't standing in, factions have no plans the hero isn't blocking, and time passes without anything happening off-page.

Signature patterns:

- **Reactive-only causality.** Ask "what changed in the world this scene that I didn't cause?" and the honest answer is *nothing*. Rivals don't advance their schemes, prices don't drift, seasons don't turn unless the protagonist prompts it.
- **Ambient death.** Crowds are backdrops that don't do anything. NPCs stand in position until spoken to, then return to stillness. A market has no smell, no haggling, no theft happening at the next stall over.
- **Protagonist-only gravity.** Every named character exists to react to the hero. Their wants, feuds, and errands begin and end at the edge of the hero's attention.
- **No off-screen clock.** Return to a place a month later and it is exactly as you left it: same people, same problems, frozen at the timestamp of your last visit.
- **World changes only when asked.** A living-world feature exists but only fires when the writer explicitly requests it: "tell me a rumor," "what's the news." Left alone, it produces silence forever.

**The empty-room test.** Write a beat in which the protagonist does nothing but observe, sitting in a tavern, waiting on a platform, watching a street. If nothing in the passage moves on its own (no one crosses the frame with their own errand, no distant thing shifts), the world is inert; it is scenery, not a place.

**The come-back test.** Have the protagonist leave a location and return after in-world time has passed. If it is unchanged, the setting has no life of its own.

## Before / after

**Contemporary / domestic** (the protagonist waits in a diner)

```text
Before (viced)
Dana slid into the corner booth and waited. The diner was quiet. The
waitress stood behind the counter. The fluorescent lights hummed. She
watched the door and waited for her brother to arrive.
```

```text
After
Dana slid into the corner booth. Two truckers were arguing about a
delivery over by the pie case, and the waitress kept refilling their
coffee without being asked, one ear on their bet about the weather. A
kid pressed sticky handprints to the window and got hauled off by a
grandmother mid-sentence. Somewhere in back a radio lost its station and
came back as gospel. Dana watched the door, but the room went on without
her.
```

The "after" fills the room with lives that don't depend on Dana. The world is happening whether or not she looks, which is what makes waiting feel like waiting *somewhere real*.

**High fantasy** (the protagonist returns to a port town after a season away)

```text
Before (viced)
The harbor was as he'd left it. The same fishmonger called the same
prices. The same beggar sat by the same wall. The tavern keeper greeted
him by name and asked where he'd been, and the town settled back into
the shape it always held for him.
```

```text
After
The season had not waited for him. Half the fish stalls were gone,
driven upriver by a salt tax, and a rope-maker's guild had taken
the empty row. The beggar's wall was a notice board now, thick with
bounties in three hands of writing. The tavern had a new keeper who
didn't know his name and didn't care to, busy short-pouring a table of
guardsmen who were new in from a war nobody here had asked to join.
```

The "after" lets the town live on its own timeline. It has moved through events the hero never saw, so returning feels like re-entering a world, not reloading a save.

**Space sci-fi** (a quiet transit hub)

```text
Before (viced)
The station concourse was empty and still. Ships were docked. The
departure board listed times. Rell waited by the airlock for clearance,
and nothing happened until the light finally turned green.
```

```text
After
The concourse never went quiet. A hauler crew was losing a cargo dispute
at the customs desk, loud enough that a maintenance drone rerouted around
them. The departure board flickered a delay and a hundred strangers
groaned as one; somewhere a courier broke into a run to make a gate that
had just moved. Rell waited by the airlock, one small still point in a
place that had a hundred urgent errands of its own, none of them hers.
```

The "after" gives the hub competing agendas and momentum. The protagonist's wait is framed against a place that is busy being itself.

## Why models do this

Two forces combine.

First, the model treats the protagonist's input as the only source of causality. A generation call is conditioned on what the user just did, so the model reaches for the most probable continuation: a *reaction* to that action. Producing off-screen change that the input didn't ask for feels, to the model, like going off-script, so the path of least resistance is to leave the world untouched until prompted. In interactive tools this is sharpened by architecture: if the only channel that can introduce a world event on its own is optional, the model will almost never volunteer to use it, because emitting nothing is always the cheapest valid output.

Second, narrative training data centers protagonists. Scenes are built around the point-of-view character's beats, so the model learns that everything on the page should serve the hero's moment. Background life, subplots that don't touch the protagonist, and the passage of off-screen time are exactly what gets compressed out of most training prose, so the model under-produces them.

The result reads like a diagnosis error, too. A builder who sees a static world often blames the model's creativity when the real cause is a **wiring gap**, a living-world mechanism that was never given a forcing function, so it fired zero times across a whole campaign. Measure the mechanism's real fire-rate before concluding the model simply "won't."

## Why it hurts

A world that only moves when touched stops feeling like a world and starts feeling like a stage set struck between the protagonist's cues. The illusion the reader or player came for, that they are inside a place larger than themselves, collapses. Stakes deflate: if rivals never advance while the hero rests, there is no cost to resting and no pressure driving any choice.

For interactive fiction the damage is acute. Players explore precisely to discover that the world has been busy without them; an inert setting makes exploration pointless because nothing is ever new. Coming back to an unchanged town, meeting NPCs who have done nothing since you left, quietly teaches the player that their attention is the only thing that matters, which is the opposite of immersion.

For novelists the cost is subtler: prose starves for texture. Scenes that should breathe with incidental life read thin and airless, and the protagonist floats through a vacuum rather than pushing against a living current.

## Fix it while writing with an AI

- **Ask for off-screen motion by default.** "In every scene, include at least one thing happening in the world that my character didn't cause: a background action, a distant event, someone with their own errand crossing through."
- **Give the world its own clock.** "Time passes for everyone. When I return to a place after a gap, show what changed while I was gone: who left, what shifted, what's new, what's worse."
- **Populate with agendas, not props.** "Background characters have their own reasons for being here. Let a few of them start, continue, or abandon something of their own within the scene, whether or not I interact."
- **Advance the pressures.** "The factions and rivals have plans that move forward on their own. Between my scenes, let those plans progress a step, so the situation is never exactly as I left it."
- **Request a targeted self-check.** "Before finalizing, answer: what moved in the world this beat that my character did not cause? If the answer is 'nothing,' add one concrete off-screen change or a piece of ambient life."
- **Paste back with a nudge.** When a scene comes back static, reply: "This room is holding still. Keep my action exactly the same, but let the place be busy being itself around me: competing errands, a small event I only glimpse."

A caution: ask for ambient life and off-screen change, not for *spectacle* every beat. A quiet scene can still be alive; you want a place that breathes, not one that stages a fresh crisis on every page.

## Fix it while building a tool

Diagnose first, then wire. Before touching prompts, measure whether your living-world channel is even firing. A very common root cause is that the mechanism exists but is optional, so it is used zero times, a wiring gap masquerading as a creativity problem. See [Separate planning from prose](../techniques/separate-planning-from-prose.md) for keeping this decision in the planner, and [Schema gates](../techniques/schema-gates.md) for the forcing-function pattern.

The core fix is a **required forcing-function field** on the planning step that makes the model consider off-screen change *every turn* and decide, explicitly, to advance the world or to hold. Holding must be a legitimate, common answer. You are forcing the *decision*, not auto-generating an event, so nothing is materialized on a fixed schedule and the world's variety stays in the model's hands. This is a factual planner field, not prose, so it carries no prose-style gates.

```json
{
  "world_pulse": {
    "type": "object",
    "required": ["last_change_summary", "current_pressures", "decision"],
    "properties": {
      "last_change_summary": {
        "type": "string",
        "description": "One line: what has moved in the world off-screen since the protagonist last engaged it."
      },
      "current_pressures": {
        "type": "string",
        "description": "The independent agendas in play right now (a rival's plan, an economic drift, a season, a faction goal) and where each one stands."
      },
      "decision": {
        "type": "string",
        "enum": ["advance_the_world_this_turn", "hold_and_reconsider_next_turn"],
        "description": "Decide whether to move the world on its own this turn. Holding is a normal, frequent choice. Advancing means one concrete off-screen change, not spectacle."
      }
    }
  }
}
```

Supporting moves:

- **Give places their own texture at generation time.** When you generate a location, generate its economy, its ongoing conflicts, and who has business there, not just its look. A place with an authored economy and a running conflict produces ambient life for free, because the writer has something to draw on. Ground this in real, varied source material rather than a single in-prompt template, or every place converges on the same mold. See [Ground voice in real exemplars](../techniques/reference-real-exemplars.md).
- **Reconcile state on return.** Keep an off-screen timeline so that when the protagonist re-enters a location, the model is handed *what changed while away* and renders the current state, not the stale snapshot. Feed the reconciled facts; do not hardcode the changes.
- **Do not solve it with determinism.** A scheduled tick that auto-spawns an event every N turns replaces silence with sameness. The world now changes on a metronome, which reads as fake. Keep the engine to *signal, channel, and validation*; let the model decide the content. The forcing function above forces the choice without dictating the outcome.
- **Route the pressures as facts, not prose.** The planner emits *what* moved (a flat note); the prose writer stages it. Mixing the two makes the planner narrate, which produces inconsistent voice and duplicated authoring.

## Watch-outs

- **Don't overcorrect into churn.** The failure of the fix is a world that convulses every beat: a new crisis, a new faction reveal, a new disaster on every page. That is exhausting and drains meaning from real turning points. "Hold" must stay a frequent, legitimate output; ambient life is usually small.
- **Register can leak in through the world.** When you push for off-screen events, watch the *tone* of what you generate. A grim seed ("the town is paranoid, every stranger is a threat") gets amplified downstream into a bleak register even if your prose prompt asked for warmth. Phrase living-world inputs toward agency and variety, not doom.
- **Beware template convergence.** If your prompt teaches one vivid example of "a lived-in place" (a fishing village, say), every generated town drifts into that mold. Keep concrete option lists in the generator that needs choices, and make the prose writer's rule purely structural: *render what this place actually is, and vary it.*
- **Measure, don't assume the fix worked.** Check the channel's real fire-rate and the actual variety of what it produces on live runs. A forcing function that the model always answers with "hold" is no better than the gap you started with; if that happens, your pressures aren't concrete enough to act on.
- **Sibling morphs.** The same protagonist-centered gravity feeds [speaking for the protagonist](speaking-for-the-protagonist.md) (the hero absorbs all agency in a scene). A world of identical, mean-regressed towns and factions is [voice homogeneity](voice-homogeneity.md) at the setting level. And a grim living-world seed tends to cool the whole register toward bleakness, a [tonal drift](tonal-drift.md) problem worth watching separately.

## Related

- [The core idea](../the-core-idea.md)
- [Speaking for the protagonist](speaking-for-the-protagonist.md)
- [Voice homogeneity](voice-homogeneity.md)
- [Schema gates](../techniques/schema-gates.md)
- [Separate planning from prose](../techniques/separate-planning-from-prose.md)
- [Ground voice in real exemplars](../techniques/reference-real-exemplars.md)
- [No pink elephant](../techniques/no-pink-elephant.md)
