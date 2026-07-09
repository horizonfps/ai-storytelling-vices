_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Speaking for the Protagonist / Reader

> The model puts words, choices, decisions, or feelings into the point-of-view character's mouth that the writer never asked for, inventing dialogue, resolve, and initiative that belong to the human. Render the declared act and stop.

**Also known as:** puppeteering the protagonist, POV voice theft, stealing agency, invented player dialogue, narrating the reader's choices, protagonist over-render, authoring the decision.
**Family:** dialogue

## The tell

You hand the model a simple action, and it hands back a whole performance. The character you declared into motion starts making speeches, weighing options in interior monologue, changing goals, or committing to positions you never chose. The input was a floor; the model treated it as a launch pad.

Signature patterns:

- **Invented dialogue where the input carried none.** You wrote "she crosses the room and opens the drawer," and the character now announces a plan out loud.
- **Authored decisions.** "You realize talking is pointless, so you shove past the guard": the protagonist's choice was made for you.
- **Bolt-on initiative.** A conversation on the way, a fresh resolve, a declared intention, a mood the character "settles into," none of it requested.
- **Committed interiority.** "He knew, then, exactly what he had to do": the character is now locked to a stance the writer never picked.

**The ceiling test.** Read the input as the *maximum* the character is allowed to say and do this beat, not the seed. If the output has the character speaking, deciding, or intending more than the input authorized, the model over-rendered. If the input carried no line of dialogue, the character should act in silence.

This is the interactive-fiction acute form, but novelists hit the same trap: a scene where a strong secondary character or the protagonist gets swept along by the narrator's momentum instead of driving from their own established will.

## Before / after

**Noir / detective** (input: *"She reads the last line of the letter and drops it in the ashtray."*)

```text
Before (viced)
She read the last line and let out a slow breath. "So that's how it
was," she murmured. "All this time. Fine. Then I know exactly where I'm
going tonight, and God help whoever's waiting." She dropped the letter
in the ashtray, already decided, already halfway out the door in her mind.
```

```text
After
She read the last line twice. Then she dropped the letter in the ashtray
and watched the corner catch, curl, and go to nothing. The match was
still in her other hand.
```

The "after" keeps the drop, adds a physical beat that lets the reader feel the weight, and, crucially, invents no line and no announced plan. Where she goes next is still the writer's to decide.

**High fantasy** (input: *"He wakes at dawn and hurries to the forge to start the day's work."*)

```text
Before (viced)
Dawn hauled him up out of sleep. "Overslept, no, no, no," he gasped,
scrambling for his boots. "The commission's due today, I have to be
ready, I have to prove I can do this on my own!" And in that moment he
resolved that he would never again let the old master's doubts weigh on
him. He ran for the forge.
```

```text
After
Dawn came through the shutter slats before he wanted it. He was up and
into his boots and out the door in the space of a breath, the cold
striking his bare arms, the forge already unlit and waiting across the
yard. He got the fire going before the birds finished waking.
```

The "after" renders exactly the declared act (waking, hurrying, starting work) with sensory texture and momentum. It invents no shout, manufactures no vow, and borrows no resolve. His feelings about the old master remain his (and the writer's) to reveal later.

## Why models do this

Instruction-tuned storytelling models are rewarded for producing complete, vivid, satisfying scenes. A terse action prompt reads to the model like an unfinished scene it should flesh out, so it fills the space with the two things it has learned make prose feel alive: dialogue and interiority. It conflates "make this vivid" with "give the character more to say, decide, and want."

Leaving a decision open also feels, to the model, like a dangling thread. Training on complete narrative arcs teaches it to close beats, to resolve the moment, so it authors the choice rather than stopping at the choice point. In second-person interactive prose the pull is even stronger, because the grammatical "you" invites the model to narrate the reader's inner life as fluently as it would any other character's.

A register instruction makes it worse in a specific way. Telling the model *how* a character talks (a child talks like a child, a soldier clips their words) governs the voice **when** the character speaks. The model frequently reads it as license to make the character speak, converting a style note into initiative it was never granted.

## Why it hurts

In any co-authored or interactive story, the protagonist's voice and choices are the one thing that belongs exclusively to the human. Inventing them is the most direct way to steal a collaborator's agency. It silently commits the character to positions the writer never chose, so the next turn either contradicts the invented stance (breaking continuity) or accepts it (surrendering authorship). Either way the writer is now cleaning up after the model instead of driving.

For interactive tools the damage is structural: every authored choice removes a decision the player came to make. The experience stops being play and becomes watching. For novelists the cost is subtler but real: a protagonist who is pushed around by narrator momentum reads as passive and out of character, and the scene loses the tension that comes from a person choosing under pressure.

## Fix it while writing with an AI

- **Set the ceiling explicitly.** "Render only the action I describe. Do not add dialogue, decisions, plans, or feelings for my character beyond what I gave you. If my input has no dialogue, my character acts silently."
- **Ask for the world, not the choice.** "End the beat at the moment a decision is required. Describe what my character sees, what the other people do, and what's at stake, then stop, so I choose the next move."
- **Separate voice from initiative.** If you gave a register note, add: "That tells you how they sound when they speak; it does not mean they speak. Only give them a line when my input contains one."
- **Request a targeted self-check:** "Before finalizing, confirm: did I invent any spoken line, decision, goal change, or resolve for the point-of-view character that the input didn't contain? If so, cut it and render only the declared act."
- **Paste it back trimmed.** When the model over-renders, delete the invented lines and decisions yourself, then ask it to rebuild the beat around the bare action with sensory and world detail instead of protagonist speech.

## Fix it while building a tool

Put a required self-audit field on the writing tool's schema, keyed to this vice, with an affirmative commitment the model re-reads before it emits. Split the check into its two independent halves, no invented speech and the input as ceiling on protagonist action, because the model can honor one while breaking the other. Keep the prohibited patterns in the description, never as a copyable example in the enum value.

```json
{
  "pre_emit_audit": {
    "type": "object",
    "required": ["player_voice", "player_agency"],
    "properties": {
      "player_voice": {
        "type": "string",
        "enum": ["i_invent_no_spoken_lines_for_the_pov_character_when_the_input_had_none"],
        "description": "If the writer's input carried no dialogue for the point-of-view character, that character acts without speaking. Do not manufacture lines, plans said aloud, or announced intentions."
      },
      "player_agency": {
        "type": "string",
        "enum": ["i_render_the_world_up_to_the_decision_point_and_do_not_author_the_pov_choice"],
        "description": "The writer's input is the ceiling on what the point-of-view character does, decides, wants, or resolves this beat. Render only the declared act; stop where a new choice would begin and let the human make it."
      }
    }
  }
}
```

Two supporting moves make the gate hold:

- **Give the writer a factual snapshot of the point-of-view character** (name, current situation, established goals) so the model can distinguish "render this person" from "invent this person." A common root cause of the worst version (the protagonist getting demoted into a generic NPC) is simply that the generator was handed the protagonist's stats but not their name, so a mention of that name in the seed text read as a new character to create. Feed the missing identity context; do not add an engine rule that overrides the model.
- **Back the gate with a reviewer pass** that reads the full in-scene state and flags protagonist-authored actions before the prose is committed. This vice is easy to detect after the fact even though the writer keeps committing it, so a cheap second read is a strong backstop.

Scope one edge case deliberately: openings and forced transitions legitimately move the protagonist without a fresh input (the scene has to start somewhere). Let those through rather than blocking all protagonist motion.

See [Schema gates](../techniques/schema-gates.md) for the required-field gate, [Post-hoc validation and regeneration](../techniques/post-hoc-validation-and-regen.md) for the reviewer backstop, and [No pink elephant](../techniques/no-pink-elephant.md) for why the forbidden phrasing stays out of the enum.

## Watch-outs

- **Hold the inverse rule at the same time.** The opposite failure is *suppressing* a voice the writer did give: refusing to write a declaration, or having an in-world character invalidate it ("no, you couldn't have seen that"). Both are agency theft; they just pull in opposite directions. Let the writer declare, let the *world* respond to the effect, and never use a character as a censor of a meta-instruction. Legitimate mechanics (setup validation, option menus, a rule that makes an attempted move come out weaker) are consequence, not censorship.
- **Register is not initiative.** When you steer *how* a character sounds, add that it does not mean the character talks more; otherwise the voice note becomes a reason to invent lines.
- **Don't trust the self-audit verdict.** The model almost always marks the gate "ok"; the value is the forced re-read, not real self-detection. Pair it with the reviewer or measure incidence on live output.
- **Accept a residual.** Even with the gate and a reviewer, the strongest models still slip on the hardest cases; there is a real ceiling. Chasing it to zero with more gates dilutes the ones that work.
- **Sibling morphs.** The same fill-the-scene impulse also drives the [recap chain](recap-chain.md) (replaying the writer's input back at them) and the [gesture gloss](gesture-gloss.md) (explaining the protagonist's inner state for the reader). Watch the family.

## Related

- [The core idea](../the-core-idea.md)
- [Recap chain](recap-chain.md)
- [Gesture gloss](gesture-gloss.md)
- [Dialogue fragmentation](dialogue-fragmentation.md)
- [Schema gates](../techniques/schema-gates.md)
- [Post-hoc validation and regeneration](../techniques/post-hoc-validation-and-regen.md)
- [No pink elephant](../techniques/no-pink-elephant.md)
