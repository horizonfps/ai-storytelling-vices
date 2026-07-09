_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Voice Homogeneity

> Every character sounds the same. The dockworker, the queen, and the child all speak in one flat, well-behaved register, with no idiolect, no distinct rhythm, and no personal vocabulary.

**Also known as:** same-voice cast, flat dialogue palette, one-note ensemble, single-register drift, idiolect collapse, everyone-broods.
**Family:** dialogue

## The tell

Read three characters' lines with the speaker tags removed. If you can't guess who said what, the voices have collapsed into one.

The signature patterns:

- **A shared default register.** Everyone lands in the same band, usually quiet, clipped, measured, and faintly weary. Vocabulary and backstory come out varied, but *loudness, pace, and warmth* narrow to a single setting.
- **No lexical fingerprints.** No character owns a word, a filler, a way of dodging a question, a running joke. Nobody interrupts, over-explains, or trails off differently from anyone else.
- **Amplitude compression.** A brief that calls for a bellowing cook or a fast-talking hustler still produces someone who "speaks low and even" and "chooses their words." Loudness gets flattened toward restraint.
- **The cast absorbs one tone.** In a lively ensemble, only one figure stays vivid and everyone else reverts to a naturalistic straight-man. Worse, everyone can converge on a single "sensible" cadence and take turns delivering the same weighty one-liners.

**The tag-removal test.** Strip the "she said" / "he muttered" attributions from a page of dialogue. If the reader can still tell the speakers apart by word choice and rhythm alone, the voices are distinct. If the lines are interchangeable, you have voice homogeneity.

**The amplitude test.** Scan a freshly generated cast's voice notes. If more than half "speak low," "never raise their voice," or "keep a flat affect," the intensity axis has collapsed and the model has pinned everyone to the same quiet setting.

## Before / after

**High fantasy**

```text
Before (viced)
"You seek the pass through the mountains," the innkeeper said quietly.
"Few return." The blacksmith set down her hammer. "The road is closed,"
she said, low and even. "You would do well to turn back." The stableboy
nodded slowly. "It is not safe," he murmured. "Not for anyone."
```

```text
After
"The pass?" The innkeeper laughed into his tankard. "Sure, if you fancy
freezing standing up. Talk to Mirren, she shoes half the fools who try."
Mirren didn't look up from the anvil. "Road's shut. Rockfall. Go home."
The stableboy tugged the traveler's sleeve, breathless. "Did you really
come all the way from the coast? Is it true the sea moves? All of it?"
```

**Noir / detective**

```text
Before (viced)
"You're asking about the Kessler case," the clerk said. "That was a long
time ago." The witness looked away. "I don't remember much," she said.
"It was a long time ago." The retired detective nodded. "Some things you
don't forget," he said. "But that was a long time ago."
```

```text
After
"Kessler." The clerk pulled the drawer without being asked. "Everybody
wants Kessler lately. You're the third this month. Sign here." The witness
lit a cigarette off the last one. "Ask me what I saw. Go on. I'll tell you
nothing, same as I told the last two." The retired detective just poured a
second glass and slid it across. "Sit. This one takes a while."
```

In each "after," the register spreads out: a loud innkeeper, a two-word blacksmith, a wide-eyed kid; a brisk clerk, a chain-smoking stonewaller, a slow-pouring veteran. Nobody shares a cadence, and no line could be swapped into another mouth without sounding wrong. The swap test failing is what makes each speaker legible as an individual.

## Why models do this

When asked to "describe a voice" or to write dialogue, models drift toward a prestige register they associate with quality: restrained, understated, faintly literary. It reads as tasteful, so it becomes the low-resistance default. Crucially, this is a mode-collapse on one specific axis, vocal amplitude and warmth, while other dimensions (biography, vocabulary, mood) spread out normally. Diversity that varies a character's history leaves their volume pinned to the same quiet attractor.

Two structural causes make it worse:

- **Demonstration beats instruction.** A prompt that *states* "give each character a distinct voice" but *shows* only calm, clipped example lines teaches the calm clipped voice. Strong models imitate the concrete demonstration over the abstract rule, so the examples win.
- **A single anchor becomes the template.** If a character-generation schema carries one voice-note example, say a terse one, the model reuses that register for the whole cast. One demonstrated voice quietly becomes the default voice.

There is also a pipeline trap: the flattening is often baked in *upstream*. A character generator writes bland, same-register voice notes into a saved profile, and the downstream writer faithfully renders them. Fixing the writer's prompt does nothing, because the sameness entered as data one stage earlier.

## Why it hurts

A cast that sounds like one person is a cast the reader can't hold in their head. Distinct voices are how readers track who is speaking, who wants what, and who to trust; collapse them and every exchange turns to mush. It also drains the world of life, since a monoculture of quiet, measured speakers makes a bustling port or a raucous court feel embalmed. For a story meant to be warm, funny, or high-spirited, the flat register silently swaps the intended genre for a muted, prestige-drama one nobody asked for.

In interactive fiction the cost compounds. Players meet dozens of characters across a session; if the innkeeper, the smuggler, and the rival captain all talk alike, the world stops feeling populated and starts feeling procedurally generated, which it is, and which the player should never notice.

## Fix it while writing with an AI

- **Set the range explicitly, and make big the default.** "Give each character a distinct voice. Vary loudness, pace, warmth, and vocabulary. Expansive and talkative is the default; use restraint only for someone the scene establishes as guarded, like a spy or a recluse."
- **Ask for one fingerprint per character.** "For each speaker, give them one verbal habit no one else has: a filler word, a way of dodging, a joke they keep making, a subject they can't stop returning to."
- **Ground on real voices you admire.** Paste two or three short excerpts of dialogue from published work whose range you want, and say: "Draw the spread of voices from these, with different volumes and rhythms. Don't copy the wording, match the variety."
- **Request the tag-removal check.** "Strip the speaker tags from this scene and tell me honestly whether you could still tell the characters apart. If not, rewrite so each voice is unmistakable."
- **Watch the amplitude specifically.** If everyone came out quiet, say so plainly: "These all speak in the same low, measured register. Make one loud, one fast, one halting, and keep them that way."

## Fix it while building a tool

The lever is almost never "add more voice rules." Voice homogeneity is a demonstration-and-grounding problem, so the fixes are structural.

**Ground the generator on real, varied exemplars, not invented samples.** Feed a curated set of verbatim reference passages chosen to span the axis that collapses (a loud one, a terse one, a fast one, an elderly eccentric). Invented samples tend to carry the very flatness you're fighting; real ones anchor range without teaching a tic. Put the reference block in the cached static prefix so it's affordable on every call.

```python
# Character generator: ground range on real, verbatim references.
def build_generator_prompt(base_prompt: str) -> str:
    reference = load_file("reference_voices.md")  # verbatim, span loud/terse/fast/warm
    # Reference goes in the STATIC, cached prefix (before any per-call state).
    return base_prompt + "\n\n--- REFERENCE VOICES (for range only, do not copy) ---\n" + reference
```

**Make amplitude its own generation dimension, defaulting to expansive.** Don't let vocal loudness emerge from a vague "describe the voice" field; ask for it directly, and vary the priming example away from the quiet default.

```python
# Voice-note field: name the axis, default to expansive, vary by disposition.
"speaking_voice": {
    "type": "string",
    "description": (
        "Describe how THIS person sounds: loudness, pace, warmth, and one "
        "verbal habit unique to them. Default to expansive and talkative; "
        "reserve restraint for a character the brief establishes as guarded. "
        "Characterize the voice itself, not the room it fills."
    )
}
```

**Add a per-character self-audit gate on the writer.** The key names the vice; the value is an affirmative commitment the model re-reads before emitting. Keep the rule in the description, never a copyable bad line in the enum.

```json
{
  "pre_emit_audit": {
    "type": "object",
    "required": ["voice_matches_person"],
    "properties": {
      "voice_matches_person": {
        "type": "string",
        "enum": ["i_write_dialogue_that_fits_this_specific_character_not_a_default_register"],
        "description": "Each speaker's word choice, pace, and volume fit their own temperament and brief. Do not default every character to a quiet, measured register; a warm character talks more and louder, a guarded one holds back."
      }
    }
  }
}
```

**Fix the source, not just the renderer.** If a saved character profile already holds flat voice notes, re-rendering won't help, because the sameness is frozen in the data. Regenerate or patch the profiles, and audit the *generator's* priming example and disposition palette (a cool-only palette produces a cold-only cast). Prefer deriving voice fresh each turn from identity plus current emotion plus scene, rather than storing a fixed style recipe that replays identically forever.

See [Ground voice in real exemplars](../techniques/reference-real-exemplars.md) for grounding on real references, [Schema gates](../techniques/schema-gates.md) for the reflexive gate, [Anti-autophagy memory](../techniques/anti-autophagy-memory.md) for the feedback loop that flattens voices over a long session, and [Separate planning from prose](../techniques/separate-planning-from-prose.md) for keeping the generator and writer honest.

## Watch-outs

- **Don't over-correct into staccato.** An anti-monologue or "everyone talks less" rule, applied without a floor, collapses every voice into clipped shards, which is just voice homogeneity in a different key. Keep an expansive default with commas and conjunctions; make fragmentation the exception. This can morph into [dialogue fragmentation](dialogue-fragmentation.md).
- **Genuinely laconic characters exist.** The target is the *default* drift, not all terseness. A written-to-be-quiet assassin should still be quiet; the fix is that they're quiet *differently* from the quiet grandmother.
- **Beware the terse anchor example.** The single voice-note sample in a schema is usually the culprit: if it's terse, the whole cast copies terse. Change the example, not just the surrounding prose.
- **Amplitude, not just vocabulary.** Models will happily vary a character's *words* while leaving *volume* pinned. If the cast still feels same-y after a vocabulary fix, check loudness and pace specifically.
- **Register decay over long sessions.** If the writer is fed its own recent output as memory, voices flatten cumulatively: early scenes sparkle, late scenes go monotone. That is an autophagy problem, and the durable fix is bounded, factual memory, not another voice rule. See [Anti-autophagy memory](../techniques/anti-autophagy-memory.md).
- **Accept a residual.** Even a well-grounded generator won't spread the range perfectly every time; there's a real model ceiling. Push the default toward variety and stop chasing the last few percent with ever more rules.
- **Sibling morphs:** when one "sensible" character absorbs the whole cast's gravitas, they tend to start dispensing [aphorisms and oracle-voice](aphorism-and-oracle.md) one-liners. Fix the ensemble spread and that vice loses its host.

## Related

- [The core idea](../the-core-idea.md)
- [Dialogue fragmentation](dialogue-fragmentation.md)
- [Aphorism & oracle voice](aphorism-and-oracle.md)
- [Ground voice in real exemplars](../techniques/reference-real-exemplars.md)
- [Schema gates](../techniques/schema-gates.md)
- [Anti-autophagy memory](../techniques/anti-autophagy-memory.md)
- [Separate planning from prose](../techniques/separate-planning-from-prose.md)
