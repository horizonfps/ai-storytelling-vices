_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Epithet / Archetype Naming

> A character's name or title spells out their theme instead of naming a person: "the Brooding Swordsman," "Marked by Sorrow," "Grief-Eater." The label does the characterization the scene should do.

**Also known as:** trait-as-name, telegraphed naming, over-thematic naming, LARP epithet, invented poetic title, archetype label.
**Family:** worldbuilding

## The tell

The model, asked to name or title a character, reaches past ordinary names for a phrase that announces the character's function, mood, or backstory. It shows up in several shapes:

- **Trait-as-name**, where the name *is* the theme: "the Brooding Swordsman," "the Weeping Widow," "the Grieving King." A role plus an adjective, standing in for a person.
- **Invented poetic epithet**, a tabletop-flavored title with no real-world footing: "Marked by Rumor," "Feels the Storm on His Skin," "Kissed by Misfortune," "Bearer of the Long Silence."
- **Meteorological / cosmic metaphor label:** "Stormheart," "Ashfall," "the Tempest," "Nightbringer," which map weather and doom onto a person.
- **Vague single-word trait with no anchor:** "Trembler" (from cold? fear? age?), "the Quiet One," "the Broken." Evocative but unattached to any concrete quirk.
- **Needless clinical qualifier:** "Chronic Prevaricator" where "Liar" would do; "the Perpetually Bereaved" where "Sad-Eyed" would do.

**The street test.** Read the name aloud and ask: *would a real person actually call someone this out loud, in this world, without a straight face?* "Big-Hearted," "Good with Faces," "Deaf in One Ear," "Cold-Blooded," "Bad Back": yes, those are how people label each other. "Sorrow-Wreathed" and "the One Who Listens to Rain" work only in a rulebook. If it fails the test, the fix is usually to change the *idea* behind the name, not to force a prettier phrasing.

A second tell: the name front-loads the reveal. If a mysterious figure is introduced as "the Betrayer" on page one, the story has already told you the ending in the character sheet.

## Before / after

**High fantasy**

```text
Before (viced)
At the crossroads waited the Sorrow-Bound Knight, a man whose every
step seemed weighed down by the grief he carried, known to all the
villages as He Who Mourns the Fallen.
```

```text
After
At the crossroads waited a knight named Aldric Vane. He'd buried three
lords in as many winters, and it showed in how carefully he counted the
travelers on the road, as if tallying who he'd have to bury next.
```

**Noir / detective**

```text
Before (viced)
The informant they called the Whisper-Man slid into the booth, a
figure defined by secrets, a creature of shadow and half-truths.
```

```text
After
The informant slid into the booth. People called him Sandback, after
the neighborhood he never left, and he talked the way a man talks when
he's already sold the same tip to two other buyers.
```

In each "after," the character has a plain, worn name and the theme arrives through what they *do*: the knight counting travelers, the informant double-selling. The reader assembles "grief-worn" and "untrustworthy" on their own, which is more convincing than a label that pre-announces it.

## Why models do this

Asked for an "evocative" name, a model pattern-matches "evocative" to the poetic-compound and epithet forms that saturate fantasy, tabletop, and mythology text in its training. Those forms read as high-flavor for near-zero effort, so they surface as a strong default whenever the name field is open-ended.

The same instinct that produces the [gesture gloss](gesture-gloss.md) is at work. The model wants the name to *be legible*, to carry its meaning on its face, rather than trusting the character's actions to reveal it. It also hedges toward specificity that sounds authoritative, which yields the clinical qualifier, and toward drama that sounds meaningful, which yields the weather metaphors. A bare name feels, to the model, like an under-delivered answer.

## Why it hurts

A telegraphed name flattens a character into a placard before they have done anything. It kills surprise (the "Betrayer" cannot betray you), and it reads as costume-drama LARP rather than a lived-in world, which breaks immersion the moment a reader scans a cast list or a menu.

The damage does not stay in the name. Once a character is titled "the Grief-Eater," that label leaks into every scene that references them: the narrator quotes it, dialogue leans on it, and the over-thematic register infects the surrounding prose. In an interactive story, a menu of characters or traits named this way makes the whole thing feel authored-by-template, and it pushes the tone toward a uniform, portentous gray. A cast where everyone carries a doom-epithet has no ordinary people left in it, so nothing reads as ordinary and the striking names stop standing out.

## Fix it while writing with an AI

- **Ask for plain names, theme through action:** "Give characters ordinary, lived-in names. Let their personality and history come out in what they do and say, not in their name or title."
- **Invoke the street test:** "Before you settle on any name or nickname, check whether a real person would actually call someone that out loud. If it only works in a fantasy rulebook, pick something plainer."
- **Prefer the natural forms.** Tell the model which shapes you want: a natural single word ("Unlucky," "Shaky-Hands"), an established idiom ("Head in the Clouds"), a real-speech compound ("Good-with-Faces," "One-Ear"), or, only when it's earned, a short label anchored to a concrete, recognizable quirk (a scar, a habit, a trade), never a weather or doom metaphor.
- **Keep the reveal offstage:** "Do not put the character's secret, twist, or defining wound into their name. Introduce them plainly and let the reader discover the rest."
- **Fix the idea, not the phrasing.** If a name keeps coming out as an epithet, the underlying concept is probably too on-the-nose. Ask the model to give the character a mundane specific instead, such as a job, a place, or a tic, and name them from that.

## Fix it while building a tool

Handle this at the naming/generation step, and encode the accepted forms rather than a list of banned ones. Keep concrete example names out of the prompt entirely, because a sample name is the thing a model most reliably copies, so a "bad example" plants exactly what you're trying to avoid (see [No pink elephant](../techniques/no-pink-elephant.md)).

A generic schema field that steers naming affirmatively:

```json
{
  "name": {
    "type": "string",
    "description": "An ordinary, lived-in personal name. Accepted forms: a natural single word; an established real-speech idiom; a real-speech compound nickname; or a short label anchored to a concrete, recognizable quirk (a scar, a habit, a trade). Personality and backstory belong in the character's actions and dialogue, not in the name. Do not name a character for their theme, mood, secret, or fate. No weather, cosmic, or doom metaphors as names."
  }
}
```

Add a lightweight review pass (a heuristic or a second model) that flags any generated name matching epithet shapes, such as a "the [Adjective] [Role]" template, a "[Verb-er] of [Noun]" construction, or a bare abstract-noun title, and requests a re-name. Because this is a worldbuilding decision rather than diffuse prose, a targeted review is cheap and reliable here.

Two structural notes:

- **Feed the generator variety, not a mold.** If a single vivid example name lives in the prompt, every generated cast rhymes with it. Ground the generator on a *range* of real, varied naming conventions instead, so it spreads across the space rather than converging on one attractor. See [Ground voice in real exemplars](../techniques/reference-real-exemplars.md).
- **Only new names are fixed.** Names already written into saved records keep contaminating output. If you change the rule, clear stale epithet-names from persisted state too, or the old labels re-surface downstream.

If you also run a prose writer that renders these characters, the naming rule keeps the [aphoristic register](aphorism-and-oracle.md) from leaking in through a quoted title; see [Schema gates](../techniques/schema-gates.md) for the writer-side gate pattern.

## Watch-outs

- **Real nicknames look like epithets, and that's fine.** "Lefty," "Four-Eyes," "Old Salt" are real-speech compounds people actually use; they pass the street test. The vice is the *invented poetic* title with no footing in how people talk, not the short descriptive nickname.
- **Vague single words fail for lack of an anchor, not for being short.** "Liar" is a good plain name; "the Broken" is not, because it names a mood with nothing concrete behind it. Ask "broken how?" and if there's no answer in the fiction, the name is empty.
- **Don't over-correct into sterile clinical terms.** Swinging from "Grief-Eater" to "Subject with Bereavement Disorder" trades one wrong register for another. Plain and human, such as "Sad-Eyed" or "Never Smiles," beats both.
- **Don't ban the striking name entirely.** A genuinely earned title, anchored to a real world-quirk and used sparingly, is good craft. The failure is the *default* reach for epithets across a whole cast, and the *telegraphed* reveal-in-the-name.
- **Sibling morphs.** The same "make the meaning legible on its face" impulse drives the [gesture gloss](gesture-gloss.md) and the [aphoristic closer](aphorism-and-oracle.md). If your cast is over-epitheted, check whether the prose is over-glossed too; they usually travel together, and a laconic character named for their theme tends to speak in doom-maxims as well.

## Related

- [The core idea](../the-core-idea.md)
- [Gesture gloss](gesture-gloss.md)
- [Aphorism & oracle voice](aphorism-and-oracle.md)
- [No pink elephant](../techniques/no-pink-elephant.md)
- [Ground voice in real exemplars](../techniques/reference-real-exemplars.md)
- [Schema gates](../techniques/schema-gates.md)
