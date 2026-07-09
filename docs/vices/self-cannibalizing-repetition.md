_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Self-Cannibalizing Repetition (Autophagy)

> The model feeds on its own prior prose. Fed its recent output as context, it imitates and amplifies its own habits: images recur, vocabulary narrows, punctuation flattens, and energy decays across a long session until late scenes read like faded photocopies of early ones.

**Also known as:** prose autophagy, self-conditioning loop, model-autophagy (MAD) loop, register decay, voice ratchet, self-imitation spiral, raw-prose memory feedback.
**Family:** structural

## The tell

The signature is *drift over time*, not a single bad sentence. Any one passage may read fine; the failure only shows up when you compare early scenes against late ones.

- **Imagery calcifies.** A striking phrase the model coined early, such as a certain metaphor for the sea or a favorite word like "resonant" or "hollow," returns again and again, sometimes verbatim, long after it stopped being fresh.
- **Vocabulary narrows.** The palette shrinks toward a handful of nouns and structural moves. Once a setting is established, the same fixtures ("the rail," "the deck," "the lantern") get re-named every scene instead of the story moving past them.
- **Punctuation flattens.** Exclamation and question marks quietly disappear; lively early lines give way to uniform, muted declaratives. Energy leaks out turn by turn.
- **Register ratchets one direction.** Once a recent stretch goes quiet or grim, the writer stays quiet or grim even when the scene should be loud, because it is matching the tone of its own last few pages.
- **Cadence homogenizes.** Every scene starts to open with the same rhythm and close on the same kind of beat, because the model keeps modeling its own most recent shape.

**Quick mental test.** Put a scene from early in the session next to one from late in the session and read them back to back. If the late one is measurably flatter, more repetitive, or narrower in vocabulary, and nothing in the story justifies the change, you are watching autophagy. A useful instrument is to count exclamation-plus-question marks per thousand words across the session; a curve sliding toward zero is the fingerprint.

## Before / after

The "before" here is a *pair* of turns far apart in one session, to show the decay. The "after" shows the same late turn once the feedback loop is broken.

**Space sci-fi**

```text
Before (viced)
Turn 4:  "A whole garden, in vacuum!" Mara had both gloves flat on the
         cracked glass, breath fogging it. "Something in there is keeping
         it warm. Something big. Tell me you see it too."

Turn 41: The garden. The glass. The frost on the glass. Mara looked at
         the garden. The glass was cold. She did not say anything.
```

```text
After
Turn 41: Mara pressed her helmet to the seal and felt the warmth bleed
         through the plating, still warm, forty years abandoned. "Whatever's
         tending this," she said, "it's home right now. Cut the lights."
```

The late turn recovers its verbs and its curiosity instead of re-cataloguing "the glass" it already described twenty scenes ago.

**Quiet horror**

```text
Before (viced)
Turn 6:  The house breathed around her, a slow settling of old timber, and
         somewhere upstairs a door she had not opened clicked once and held.

Turn 38: The house breathed. The timber settled. A door clicked. The house
         breathed again. She listened to the house breathing.
```

```text
After
Turn 38: She had stopped hearing the house. That was the thing that frightened
         her: how ordinary the settling timber had become, how she'd learned
         to sleep through the door that opened itself at three.
```

The "before" late turns are not just repetitive; they are the model imitating its own earlier prose so hard it loops. Each "after" moves the moment forward and lets the established detail recede instead of re-staging it.

## Why models do this

Language models are strong imitators, and they imitate whatever is in their context most heavily. When a system feeds the writer its own recent raw prose as "memory," meaning the last N turns refreshed every call, that history acts like a rolling stack of few-shot examples written in the model's own voice. Whatever small habit is present in recent output becomes the target the model reproduces, and its reproduction becomes the next turn's memory. That is a feedback loop: each pass slightly amplifies the last, and the whole thing grinds toward a single mode.

Two forces make it inevitable rather than occasional:

- **Demonstration outweighs instruction.** A static rule like "keep the prose varied and energetic" cannot outvote a large, fresh bank of concrete demonstration. Once the model's own text dominates the window, the base instructions lose. A line in the prompt that says "do not treat this as a style template" is no match for pages of actual style.
- **It gets worse with scale and length.** The stronger the model's imitation and the longer the session, the more its own output crowds out the original instructions. Early turns still carry the prompt's energy; late turns carry mostly the model's accumulated self-echo.

This is the same mechanism that makes synthetic few-shot examples plant vices, reopened accidentally through memory plumbing. The cause lives in the memory-construction code that assembles the writer's context, upstream of the prose itself.

## Why it hurts

The story deadens exactly where you most want it alive: the deep, long sessions a reader or player has invested hours in. Early scenes sparkle; late scenes go gray, repetitive, and tonally stuck. The reader notices the loop even if they can't name it: the same three images, the same closing rhythm, the world shrinking to a handful of re-described fixtures.

For interactive fiction the cost is sharper. A player who stays for forty turns is your most engaged player, and autophagy punishes them specifically: the longer they play, the more procedurally generated the prose feels. It also silently defeats every other fix in your stack, because the demonstration channel overpowers the rule channel. You can perfectly tune an anti-repetition instruction and still lose to the model's own history.

## Fix it while writing with an AI

You control the context window by hand in a chat, so the fix is direct: **do not feed the model its own prose back as a style to match.**

- **Summarize facts, don't paste prose.** When you continue a long piece, give the model a short factual recap of what happened, who did what and where things stand, not your last several paragraphs to imitate. "Here's what has happened so far, in plain notes. Now write the next scene fresh."
- **Re-anchor to an early strong passage.** If the voice has drifted flat, paste a vivid *early* excerpt, not the most recent one, and say: "This is the energy and range I want. Match it. Do not match the last few turns."
- **Keep a running "already used" list.** Track the images, metaphors, and pet words the model has leaned on, and hand them back as a *vary-these* instruction: "You've already used 'resonant,' the sea-as-a-held-breath image, and closed three scenes on the weather. Retire those and find new ones."
- **Ask for the decay check directly.** "Compare this scene's energy and vocabulary to scene one. Be honest: has the register flattened? If so, rewrite to restore it."
- **Watch the punctuation.** If everything has settled into flat statements, re-license it plainly: "You can use an exclamation for a real shout and a question mark for genuine surprise. The prose has gone monotone, so bring the energy back."

## Fix it while building a tool

This is an **architecture** fix, not a prompt fix. More rules will not beat a large window of the model's own prose. Change what the writer is fed.

**1. Cut the source.** Give the writer only the *current scene's* raw prose, capped to a small window, and reset it at scene boundaries so prior-scene prose never leaks forward. Size the cap to cover your summarization cycle with slack; too small and long scenes lose factual continuity.

**2. Invert the memory.** Provide long-term continuity as *distilled factual summaries*, not verbatim prose. Facts anchor continuity without handing the model a voice to copy.

**3. Add an overused-imagery ledger.** Track repeated n-grams across a sliding window and feed them back with a *vary-these* instruction. Cover figurative language *and* mundane structural vocabulary (the recurring "rail"/"deck" nouns), and protect proper names so recurring characters aren't flagged. The ledger is pure bookkeeping: it counts repetition and decides no content, so it introduces no determinism.

```python
# Writer memory: facts + a small, scene-scoped prose window + a vary-these ledger.
# NOT: the model's last 30 turns of raw prose re-injected every call.

def build_writer_memory(turns, scene_open_idx, cap=10, window=8, min_repeat=2):
    scene = [t for t in turns if t.idx >= scene_open_idx]
    recent_prose = scene[-cap:]                 # current scene only, capped, resets at cut

    grams = extract_bigrams_trigrams(
        [t.prose for t in turns[-window:]],
        protect_proper_nouns=True,              # don't flag recurring names
        include_structural=True,                # catch 'the rail'/'the deck', not just imagery
    )
    overused = [g for g, n in grams.items() if n >= min_repeat]

    return {
        "prior_facts": factual_summaries(turns[:scene_open_idx]),  # distilled, not prose
        "recent_prose": recent_prose,            # label this FACTUAL continuity, not a template
        "overused_imagery": overused,            # 'these are worn: vary or retire them'
    }
```

Two wiring notes from the field:

- **Label the prose window as factual continuity, not a tone template**, in the prompt itself. A window fed as "here is the recent style" re-invites the drift you are trying to kill.
- **Audit memory-construction code, not just prompts.** This loop reopens invisibly through any plumbing that re-injects raw output. If tics creep back after a fix, check what the memory builder is actually handing the writer.

For the full pattern, meaning a bounded window, factual crystals, and an imagery ledger, see [Anti-autophagy memory](../techniques/anti-autophagy-memory.md). Keep the *writer* separate from the *planner* so factual state doesn't have to travel as prose ([Separate planning from prose](../techniques/separate-planning-from-prose.md)), and prefer real, verbatim exemplars over the model's own past output when you need to anchor voice ([Ground voice in real exemplars](../techniques/reference-real-exemplars.md)).

## Watch-outs

- **Don't starve continuity.** If the current-scene window is too small, long scenes lose track of what just happened. Size it to cover your summarization cadence with slack, then trim.
- **A figurative-only ledger misses the mundane.** Tracking only metaphors lets literal structural nouns repeat freely, so "rail," "deck," and "the door" show up thirty times a session. Widen the ledger to plain vocabulary and to durable states that shouldn't be re-staged.
- **The ledger must stay descriptive.** It counts repetition and flags it; it must never *decide* content. The moment it dictates what the writer may say, you've traded a repetition problem for a determinism problem.
- **Stale entries keep reproducing dead habits.** After you fix the generator, old turns still sitting in the memory window can keep echoing a mannerism you already removed. To validate a fix cleanly, rewind or clear that history first.
- **This underlies several surface vices.** Register decay, repeated signature images, ambient-beat refrains, and over-cataloguing a single prop are all *symptoms* of the same loop. Fixing the memory architecture addresses them at the root; patching each surface tic with its own rule does not.
- **Sibling morphs.** As the loop tightens, the flattening often surfaces as [voice homogeneity](voice-homogeneity.md) across the cast, a drift into [uniform laconic voice](uniform-laconic-voice.md), or a habit of closing every scene on the same [aphorism or oracle-voice](aphorism-and-oracle.md) beat. Break the feedback loop and these lose their fuel.

## Related

- [The core idea](../the-core-idea.md)
- [Voice homogeneity](voice-homogeneity.md)
- [Uniform laconic voice](uniform-laconic-voice.md)
- [Aphorism & oracle voice](aphorism-and-oracle.md)
- [Anti-autophagy memory](../techniques/anti-autophagy-memory.md)
- [Separate planning from prose](../techniques/separate-planning-from-prose.md)
- [Ground voice in real exemplars](../techniques/reference-real-exemplars.md)
