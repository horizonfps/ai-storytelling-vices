_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Register & Anachronism Leak

> The wrong formality, pronoun, idiom, or modern turn of phrase slips into an established voice or period, and the register drifts over a long session instead of holding steady.

**Also known as:** register bleed, tonal leak, anachronism slip, modern-slang intrusion, formality drift, period-voice break, pronoun-register leak.
**Family:** dialogue (bleeds into narration)

## The tell

A voice that was set at one register keeps sliding out of it. A medieval steward suddenly says "no worries." A hardboiled 1940s detective calls something "a whole vibe." A courtly elder drops into casual contractions and back again inside one speech. The setting establishes a world with no telephones, yet a character "calls someone up." The tone was specified (formal, archaic, clinical, streetwise), and the model honors it most of the time, then leaks the opposite in a phrase or two.

Signature patterns:

- **Modern idiom in a period mouth.** "Okay," "kids," "guys," "deal with it," "at the end of the day" surface in a world that predates them.
- **Formality flip-flop.** A character alternates between elevated diction and offhand casual within the same scene, with no in-story reason for the shift.
- **Pronoun or address-register leak.** In languages and settings that distinguish formal from familiar address, the model reaches for the familiar form exactly in the voices it should not: the deferential servant, the reverent acolyte, the distant official.
- **Concept anachronism.** Not just the word but the idea is out of place: a pre-industrial farmer thinks in terms of "energy levels," a Victorian clerk talks about "processing" his feelings.
- **Slow session-wide drift.** Early scenes hold the register; twenty turns in, the whole cast has migrated toward a flatter, more contemporary, more casual default.

**Quick mental test:** could this exact word or idiom have existed in this world, in this character's mouth, at this moment? If a Bronze Age warrior or a cloistered nun could not plausibly have said it, it leaked in. And read the first scene against the twentieth: if the voice has quietly modernized, you have session-wide drift, not a one-off slip.

## Before / after

**Historical / period**

```text
Before (viced)
The abbess folded her hands. "Look, I hear you, and honestly? The
harvest situation is a total disaster. We need to, like, regroup and
figure out our next move before winter."
```

```text
After
The abbess folded her hands. "I hear you well, and I will not soften
it: the harvest has failed us. We must take counsel before the first
snow, and take it soon."
```

**Space sci-fi (a courtly, formal culture)**

```text
Before (viced)
The envoy bowed to the throne. "So basically the border worlds are
freaking out, Your Radiance. My bad for the delay. Traffic through
the relay was insane."
```

```text
After
The envoy bowed to the throne. "The border worlds are in open fear,
Your Radiance. Forgive the delay; the relay was choked with refugee
ships, and I would not trust my message to a lesser channel."
```

**Noir / detective**

```text
Before (viced)
"The dame? Yeah, she ghosted me, left me on read." The detective
tapped his cigarette. "Whatever. Her loss. I'm not gonna stress
about it."
```

```text
After
"The dame? She skipped town without so much as a note." The
detective tapped his cigarette against the glass tray. "Fine by me.
I've been stood up by classier trouble than her."
```

In each "after," the register holds all the way through: the abbess stays grave and plain-spoken, the envoy stays formal even while delivering bad news, the detective keeps his period-appropriate hardboiled cool. The fix isn't just deleting the anachronism; it's replacing it with a line that carries the same meaning in the right voice.

## Why models do this

Models are trained overwhelmingly on contemporary, casual, standardized text. That modern register is the deep default, the place the model returns to whenever nothing actively holds it somewhere else. A period or highly formal voice is a constrained region the model has to stay inside on purpose, and constraints slip, especially on common connective phrases ("okay," "look," "at the end of the day") that are so frequent in training data they surface reflexively.

Three forces make it worse:

- **The default has gravity.** Holding an unusual register costs the model attention on every token. A long or low-information stretch (small talk, a transition, an aside) is where attention wanders and the default leaks back in.
- **Familiar-address bias.** For pronoun and address-register systems, the "folksy wisdom" or "warm elder" pattern the model associates with certain characters pulls the familiar form in, precisely the voices a strict setting most wants to keep formal.
- **Self-conditioning drift.** If the writer sees its own recent prose as context, each slightly-more-casual turn becomes the example for the next. The register ratchets toward the contemporary default across a session, and the drift is invisible turn to turn.

A note on lexical tokens: this is the one vice where naming the exact forbidden word helps rather than hurts. For a single stubborn token (a specific pronoun, a specific modern word), name it literally and explicitly. Abstracting it into "a certain casual register" measurably makes the leak worse, not better. That is the deliberate exception to the usual anti-pink-elephant discipline, which still applies to *stylistic* targets.

## Why it hurts

Register is most of what sells a world. A single modern idiom in a period mouth punctures the illusion instantly, and the reader feels the author (or the machine) leaning through the character. It is one of the most immersion-breaking failures because it is so legible: even a casual reader catches "no worries" in a knight's mouth, where they might miss subtler prose tics.

In interactive fiction the damage compounds. The player is co-authoring a world and testing whether it holds together; a leaked anachronism tells them it doesn't, that the setting is a thin skin over a chatbot's default voice. And because the drift is cumulative, a session that opened perfectly period-accurate can slide, scene by scene, into generic contemporary chatter. So the longer a player stays, the more the world dissolves, exactly when you most want it to deepen.

## Fix it while writing with an AI

- **Name the register concretely, with a floor and a ceiling.** "Write this steward in formal, period-appropriate English: no contractions, no modern slang, no anachronistic concepts. Elevated but plain: grave, not flowery."
- **Name the specific forbidden tokens.** If a particular word keeps leaking, say it outright: "Never use the words 'okay,' 'guys,' or 'vibe' anywhere in this character's speech." Being literal about a lexical item is correct here; don't hedge it into "avoid casual words."
- **Anchor the period.** "This world has no electricity, no telephones, no modern psychology vocabulary. Characters cannot reference concepts that wouldn't exist yet: no 'processing feelings,' no 'energy levels,' no 'stress.'"
- **Re-anchor against drift.** Every several turns, paste back an early line you liked: "Match the register of this earlier passage. Do not let the voice modernize."
- **Request a targeted self-check.** "Before you finalize, scan every line of dialogue for any word or idea that couldn't exist in this setting or this character's mouth. List what you found and rewrite it in the right register."
- **Fix by replacement, not deletion.** When you catch a leak, ask for the same beat re-voiced: "Keep what she means, but say it the way this character actually would."

## Fix it while building a tool

Register leak is partly a stubborn-token problem and partly a drift problem, and the two need different layers.

**1. State the register affirmatively in the writer's instructions, and name hard tokens literally.** Describe the target register positively (what the voice *is*) and, for a specific forbidden lexical item, name it explicitly. This is the token exception to anti-pink-elephant.

```python
# Writer instructions: positive register + literal named tokens for the hard cases.
register_rule = (
    "Voice: formal, period-appropriate. Elevated but plain diction; "
    "full words, not contractions; no modern idiom or modern concepts.\n"
    "Forbidden words in this setting: 'okay', 'guys', 'kids', 'vibe', 'stress'."
)
# Naming the exact tokens is correct here. Do NOT abstract them into
# 'avoid casual language'; for a single hard token, abstraction makes the leak worse.
```

**2. Add a reflexive self-audit gate on the writer.** The key names the vice; the value is an affirmative commitment the model re-reads immediately before emitting. Keep the rule in the description; keep any literal forbidden token in the instructions layer above, not baked into the enum value.

```json
{
  "pre_emit_audit": {
    "type": "object",
    "required": ["register_holds"],
    "properties": {
      "register_holds": {
        "type": "string",
        "enum": ["i_keep_the_established_register_and_period_in_every_line"],
        "description": "Every line of dialogue and narration stays in the setting's register and period. No modern idiom, no anachronistic concept, no formality flip-flop, and formal address stays formal for the characters the world marks as formal."
      }
    }
  }
}
```

See [Schema gates](../techniques/schema-gates.md) for gate mechanics, the required-field rule, and the model ceiling.

**3. For a single stubborn token, layer a redundant reminder and a deterministic backstop.** Rule text alone often does not fully converge on a hard pronoun or word. Keep the named token in the main prompt *and* add a short reinforcement in a second high-priority channel (a preamble immediately before the writer's instructions, or a system parameter). As a final safety net, add a deterministic post-generation rewrite for the forbidden token, and sanitize any line before it re-enters memory, because a contaminated line stored as context re-teaches the leak.

```python
def sanitize_register(text: str, forbidden: dict[str, str]) -> str:
    # Deterministic backstop: rewrite hard forbidden tokens on the way out,
    # and before any line is stored back into context.
    for bad, good in forbidden.items():
        text = replace_word(text, bad, good)
    return text
```

**4. Stop the session-wide drift at the memory layer.** A gate cannot beat a feedback loop. If the writer is fed its own raw prose as context, the register ratchets toward the contemporary default. Cap that window, slice it per scene, and feed factual summaries for older state instead of verbatim prose, so history informs continuity without teaching a flatter voice. This is architectural. See [Anti-autophagy memory](../techniques/anti-autophagy-memory.md).

Measure the fix in real output over a long run: sample dialogue at the start, middle, and end of a session and count anachronisms and register breaks per scene, rather than trusting a short test.

## Watch-outs

- **The token exception is real; don't over-apply anti-pink-elephant.** For *stylistic* drift, describe the shape and avoid copyable bad examples. But for a single hard lexical token, name it literally and repeatedly; abstracting it multiplies the leak. Keep the two rules straight.
- **Watch for self-contradicting rules.** A rule that bans a familiar pronoun while listing that pronoun's own object or possessive forms among the *approved* ones commands the leak it forbids. The model obeys the letter. Audit register rules for internal contradiction.
- **Some register mixing is legitimate.** A character code-switching on purpose (a streetwise informant putting on formal airs to mock an official) is characterization, not a leak. The vice is *unmotivated* drift, not every departure from a single register.
- **A residual leak may be the user's own input.** If a modern word appears once and it came straight from the player's text passing through, that is not a rule violation to chase. Distinguish the model's drift from the human's contribution.
- **Measure over a long session.** Drift is cumulative and invisible scene to scene. An eight-to-twelve-turn playtest surfaces it where a single call will not.
- **Expect a residual ceiling.** Even with a named token, a gate, a reinforcement, and a sanitizer, a fraction of outputs still slips on the strongest models. Accept the residue rather than stacking rules that dilute the ones that work.
- **Sibling vices it morphs into:** a register that flattens toward one contemporary default across the whole cast becomes [Uniform laconic voice](uniform-laconic-voice.md) or [Voice homogeneity](voice-homogeneity.md); a character over-corrected into portentous archaic diction starts dispensing [Aphorism & oracle voice](aphorism-and-oracle.md).

## Related

- [The core idea](../the-core-idea.md)
- [Voice homogeneity](voice-homogeneity.md)
- [Uniform laconic voice](uniform-laconic-voice.md)
- [Aphorism & oracle voice](aphorism-and-oracle.md)
- [Schema gates](../techniques/schema-gates.md)
- [No pink elephant](../techniques/no-pink-elephant.md)
- [Anti-autophagy memory](../techniques/anti-autophagy-memory.md)
