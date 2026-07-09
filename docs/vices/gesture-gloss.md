_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Gesture Gloss

> A concrete gesture is shown, then a clause tacked on to explain what it means, as in "she clenched her fist, a sign of her resolve." Show the gesture and stop.

**Also known as:** psychological simile, smuggled gesture gloss, annotated action, show-then-explain, "as if / like someone who" clause.
**Family:** prose

## The tell

A physical action lands, and then the sentence keeps going to translate it for the reader. The gesture stops being something the reader interprets and becomes something the narrator has already interpreted. The gloss usually arrives on a small set of connectors:

- `as if` / `as though`: "he set the cup down, as though the day had already beaten him."
- `like someone who` / `the way a person does when`: "she signed her name the way someone signs a thing they have stopped believing in."
- `with the air of someone who` / `the look of a person who`: "he laughed, with the air of a man rehearsing it."
- `a sign of` / `a mark of`, or a bare emotion-noun apposition: "his jaw tightened, a flash of the old anger."

The load-bearing content is always the clause after the gesture, not the gesture itself. It masquerades as concrete description because it names a physical action, but the meaning is spoon-fed.

**The would-it-survive test.** Delete everything after the gesture and read what remains. If the action alone still carries the moment, the gloss was caption, so cut it. If the action feels empty without the explanation, the problem is the action: change *what the character does* so the meaning is in the doing, rather than propping a weak gesture up with a label.

## Before / after

**Contemporary / domestic**

```text
Before (viced)
She set the wine glass down without a sound, her knuckles pale against
the stem, as if some old grief had come back to sit beside her at the
dinner table.
```

```text
After
She set the wine glass down without a sound. Her knuckles stayed pale
against the stem a moment longer than they needed to. Then she asked,
lightly, whether anyone wanted dessert.
```

**Space sci-fi**

```text
Before (viced)
The pilot flicked the burnt fuse into the recycler and wiped her hands
on her coveralls, like someone who had long since stopped expecting the
ship to reward her care.
```

```text
After
The pilot flicked the burnt fuse into the recycler, wiped her hands on
her coveralls, and logged it as the fourth this cycle. She didn't file
the maintenance request. She'd filed the last three.
```

In each "after," the meaning still arrives. The held knuckles and the deflecting question carry the grief; the unfiled request carries the resignation. The reader does the connecting. The gesture keeps its charge because it isn't immediately discharged by a label.

## Why models do this

Instruction-tuned models are rewarded for being helpful and legible, so after implying something they reflexively complete the thought and state it plainly. Leaving a gesture un-explained reads, to the model, like a gap it failed to fill. The "as if" and "like someone who" frames are also dense in literary training text, where they signal a certain contemplative register, so they surface as a high-probability continuation whenever the model has just described a body.

This is a *semantic* vice, not a syntactic one, which makes it stubborn. Each glossing clause reads as fine craft on its own, so a model asked to self-check its own prose usually marks it clean; it does not perceive the fault. The gloss is also an overflow channel: once you close the more obvious ways to state emotion (naming feelings outright, evaluative abstraction), the same interpretive impulse escapes here, disguised as concrete description. Expect it to spike right after you fix its siblings.

## Why it hurts

It steals the reader's inference and flattens subtext into caption. The gesture already did the work, and the gloss repeats that work in plainer words, which is redundant and, worse, signals that the author trusts neither the image nor the reader. At any density it becomes a recognizable tic, and it quietly drags the whole piece toward a slow, explanatory, literary-contemplative tone, often the opposite of the register a scene wants. In an interactive story it is especially costly: every glossed gesture is a small lecture inserted between the player and their own reading of the moment.

## Fix it while writing with an AI

- **Give an affirmative instruction:** "Show gestures and actions from the outside. Let the meaning land in the physical detail or in another character's reaction. Do not add a clause that interprets a gesture you just described."
- **Ask for the reaction, not the reading:** "Where you're tempted to explain what a gesture means, give a second character's response instead: a line, a look, a countermove." Meaning carried by another person on the page reads as drama; meaning stated by the narrator reads as summary.
- **Request a targeted self-check:** "Scan for any gesture followed by 'as if,' 'as though,' 'like someone who,' 'the way a person does when,' or a bare emotion-label. Cut the interpreting clause and end on the action."
- **Paste it back with the cut applied:** when you see the pattern, delete the trailing clause yourself and ask the model to rebuild the beat so the physical action alone lands, changing the action if it can't stand on its own.
- **Keep physical similes.** "Gasping like a diver who has surfaced too fast" is good, concrete craft. Only the *psychological* comparison, the one that reads a character's inner state, is the target.

## Fix it while building a tool

Two layers, because a reflexive self-audit field alone only cleans part of this one (the model marks itself "ok" without seeing the fault).

**Layer 1: a required schema gate on the writer.** The key names the vice; the value is an affirmative commitment the model re-reads immediately before emitting. Keep the banned connectors in the *description*, never as a literal example inside the enum, since a copyable bad phrase there teaches the pattern.

```json
{
  "pre_emit_audit": {
    "type": "object",
    "required": ["gesture_without_gloss"],
    "properties": {
      "gesture_without_gloss": {
        "type": "string",
        "enum": ["i_show_the_gesture_and_stop_without_a_clause_that_interprets_it"],
        "description": "After a physical action, do not append a clause that reads the character's inner state (connector families to avoid: 'as if', 'as though', 'like someone who', 'the way a person does when', 'with the air of someone who', a bare emotion-noun apposition). Let the gesture stand; carry meaning through the action or another character's reaction. Physical similes are fine; only the psychological gloss is banned."
      }
    }
  }
}
```

**Layer 2: post-generation validation.** Because the reflexive gate is weak against semantic vices, back it with a regex pass over high-confidence markers (`\bas if\b`, `\bas though\b`, `\blike someone who\b`, `the (look|air|expression) of (a|someone)`) that triggers a regeneration, with a slightly raised retry budget. Exclude bare `of whom` / `of someone` fragments to avoid false positives on ordinary prose. Also remove any upstream prompt language that asks for "inner voice" or "internal monologue"; that instruction is what invites the gloss in the first place, so a validator alone will keep fighting your own prompt.

See [Schema gates](../techniques/schema-gates.md) for the gate pattern, [Post-hoc validation and regeneration](../techniques/post-hoc-validation-and-regen.md) for the detect-and-regenerate backstop, and [No pink elephant](../techniques/no-pink-elephant.md) for why the forbidden phrase stays out of the enum.

## Watch-outs

- **Do not ban all similes.** Concrete, physical comparisons are good writing. Scope the rule to the psychological gloss, the clause that names or reads inner state, or you strip the prose of legitimate imagery.
- **Don't trust the self-audit verdict.** The model almost always marks this gate "ok"; the value is the forced re-read, not the model's judgment. Pair the gate with the regex backstop, or measure incidence directly on real output.
- **Watch the migration.** Close the gloss in dramatic scenes and it can reappear in transitions, openings, or reasoning fields. Cover every prose-bearing field, and expect a spike here right after you fix emotion-naming and abstraction elsewhere.
- **Don't word-hunt every residue.** A single named emotion near a gesture now and then is not worth a regeneration; there is a real model ceiling (roughly one slip in ten on the strongest models) and chasing it to zero dilutes your other gates.
- **Sibling morphs:** the same explain-it-for-the-reader impulse also drives the [contrastive reveal](contrastive-reveal.md), the [aphoristic closer](aphorism-and-oracle.md), and the [narrate-then-unnarrate](narrate-then-unnarrate.md) hedge. Watch the family, not just this member.

## Related

- [The core idea](../the-core-idea.md)
- [Contrastive reveal](contrastive-reveal.md)
- [Aphorism & oracle voice](aphorism-and-oracle.md)
- [Narrate-then-un-narrate](narrate-then-unnarrate.md)
- [Schema gates](../techniques/schema-gates.md)
- [Post-hoc validation and regeneration](../techniques/post-hoc-validation-and-regen.md)
- [No pink elephant](../techniques/no-pink-elephant.md)
