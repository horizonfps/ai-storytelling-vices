_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Anti-Autophagy Memory

> Never feed a writer its own raw prose back as memory. Give it distilled facts for continuity, a small scene-scoped window, and a ledger of worn-out imagery with an instruction to vary, so the model builds on what happened, not on how it phrased it.

## The problem it solves

A model that reads its own recent output as "memory" starts imitating itself. Because language models copy whatever is heaviest in their context, a rolling stack of the model's own paragraphs works like a bank of few-shot examples written in its own voice. Each small habit gets reproduced, the reproduction becomes the next turn's memory, and the loop tightens until late scenes read like faded copies of early ones.

This technique targets [self-cannibalizing repetition (autophagy)](../vices/self-cannibalizing-repetition.md) directly, and with it a cluster of surface symptoms that are really the same loop wearing different masks:

- [Tonal drift](../vices/tonal-drift.md): the register ratchets one direction (quieter, grimmer, flatter) because the writer keeps matching its own last few pages.
- [Voice homogeneity](../vices/voice-homogeneity.md) and [uniform laconic voice](../vices/uniform-laconic-voice.md), where the cast converges as every character absorbs the model's flattening default.
- [Aphorism and oracle](../vices/aphorism-and-oracle.md) closing tics, where the model keeps ending scenes on the same shape it just rewarded itself for.

It is worth naming the boundary clearly: this is an **architecture** fix, not a prompt fix. No wording of "keep it varied" beats a large window of concrete demonstration in the model's own hand. If the loop is running, you change what the writer is *fed*, not what it is *told*.

## How it works

The memory a long-form writer receives has three jobs: keep continuity, keep the voice alive, and keep it from repeating itself. Raw-prose memory does the first job badly and actively sabotages the other two. Anti-autophagy memory splits the jobs across three channels that each do one thing well.

**1. Continuity comes from facts, not prose.** Replace the pile of past paragraphs with distilled factual notes: who did what, where things stand, what changed. Facts anchor "what happened" without handing the model a voice to copy. There is nothing to imitate in a plain note like "she freed the prisoner and took the north road."

**2. Recent prose is windowed and scene-scoped.** Keep only the current scene's raw text, capped small, and reset it at scene boundaries so prior-scene prose never leaks forward. A short window preserves in-scene coherence (pronoun targets, the thread of a conversation) without accumulating a style bank across the whole session.

**3. A ledger names what is worn.** Track repeated phrases across a sliding window and hand them back with an instruction to vary or retire them. This turns the model's own history into a *negative* signal, a list to move away from, instead of a positive template to reproduce.

Why this works comes down to one fact about the model: demonstration outweighs instruction. When the writer's own prose fills its context, that demonstration overpowers any rule you wrote. Anti-autophagy memory removes the demonstration channel entirely. The model can no longer copy a voice that isn't there to copy. Continuity survives on facts, which carry no style; freshness survives because the ledger points away from the ruts; and the base prompt's energy stops being outvoted by pages of accumulated self-echo.

There is a subtle guarantee in the ledger design: it must stay *descriptive*. It counts repetition and flags it. It never decides what the writer says next. That keeps the fix from trading a repetition problem for a determinism problem, since the writer still chooses every word; it just chooses away from the worn ones.

## Manual version (in a chat)

You control the context window by hand in a chat, so you can run all three channels yourself. The rule is the same: do not paste your own generated prose back in as a style to match.

- **Recap in plain notes, then ask for fresh prose.** Instead of pasting the last three scenes, summarize them: "So far: the detective traced the payoffs to a shell company, the witness recanted, and the commissioner warned her off. Write the next scene fresh from here." Facts carry the story forward; they give the model nothing to echo.

- **Re-anchor to an *early* strong passage, never the latest one.** If the voice has gone flat, paste a vivid scene from early in the session and say: "This is the range and energy I want. Match this, not the last few turns." You are deliberately reaching past the decayed material.

- **Keep a running vary-these list.** Note the images and pet words the model leans on, and feed them back as things to retire: "You've used 'the wind off the moor' three times and closed two scenes on someone watching a light go out. Retire both. Find new images."

- **Ask for an honest decay check.** "Compare this scene's energy to the opening scene. Has the vocabulary narrowed or the register flattened? If so, rewrite to restore it." The model is unreliable at spotting this unprompted but decent at it when asked directly.

Repeat the recap-and-vary ritual every several turns. Over a long chat the model drifts back toward its defaults, and refreshing the factual recap plus the worn-imagery list pulls it back.

## Systematic version (in a tool)

Build the writer's memory from the three channels rather than from a raw transcript. The core discipline is in `build_writer_memory` below: current-scene prose only, capped and reset at scene cuts; distilled facts for everything older; and a repetition ledger extracted across a sliding window.

```python
def build_writer_memory(turns, scene_open_idx, cap=10, window=8, min_repeat=2):
    """Assemble writer memory that cannot feed the autophagy loop.

    - recent_prose: current scene only, capped, resets at each scene cut
    - prior_facts: distilled factual summaries of everything older (no prose)
    - overused_imagery: repeated n-grams to steer away from (descriptive only)
    """
    scene = [t for t in turns if t.idx >= scene_open_idx]
    recent_prose = scene[-cap:]  # small window; older prose never leaks forward

    grams = extract_bigrams_trigrams(
        [t.prose for t in turns[-window:]],
        protect_proper_nouns=True,   # don't flag recurring character/place names
        include_structural=True,     # catch 'the rail'/'the deck', not just metaphors
    )
    overused = [g for g, count in grams.items() if count >= min_repeat]

    return {
        "prior_facts": factual_summaries(turns[:scene_open_idx]),  # facts, not prose
        "recent_prose": recent_prose,   # label as CONTINUITY, not a style template
        "overused_imagery": overused,   # 'these are worn; vary or retire them'
    }
```

And in the writer prompt, the memory is introduced with intent, so the model treats each channel as its job demands:

```text
CONTINUITY (facts only, this is what has happened, not a style to copy):
{prior_facts}

CURRENT SCENE SO FAR (for coherence within this scene; do not extend its rhythms
into a template):
{recent_prose}

WORN IMAGERY (you have leaned on these; retire them and reach for new ones):
{overused_imagery}
```

Implementation notes that decide whether this holds up:

- **Size the cap to your summarization cadence, with slack.** Too small and long scenes lose factual continuity mid-scene; too large and you start re-accumulating a style bank. Cover roughly one summarization cycle, then trim.
- **Widen the ledger past metaphor.** A figurative-only ledger lets literal structural nouns repeat freely. Track plain vocabulary and durable states that should not be re-staged, and protect proper names so recurring characters aren't flagged as repetition.
- **Keep the ledger descriptive.** It counts and flags; it must never dictate. The instant it prescribes content, you've introduced determinism.
- **Keep the writer separate from the planner.** Factual state should travel as facts, not as prose the writer must re-narrate. See [planner/prose separation](separate-planning-from-prose.md).
- **When you need to anchor voice, use real exemplars.** If the writer needs a target register, ground it in verbatim high-quality reference text, never in the model's own past output. See [reference real exemplars](reference-real-exemplars.md).

## Pitfalls

**Don't starve continuity.** The most common failure of this technique is over-trimming: a window so small that a long scene forgets what just happened in it. Facts cover the long arc, but the current scene still needs enough recent text for coherence. Size to the cadence, then trim carefully.

**Raw prose sneaks back in through plumbing.** This loop reopens invisibly through any code path that re-injects the model's output, whether a "recent context" helper, a debug field, or a memory builder that quietly grew. If tics return after a clean fix, audit the memory-construction code, not the prompt. The prompt can be perfect while a function three layers down is pasting the last thirty turns back in.

**Stale entries keep reproducing dead habits.** After you fix the generator, old turns already sitting in the window can keep echoing a mannerism you removed. To validate a fix honestly, rewind or clear that history first, or you'll be measuring the residue of the old behavior.

**Labeling matters.** A prose window introduced as "here is the recent style" re-invites the exact drift you're killing. The same text labeled "current scene, for continuity only" behaves differently. The frame is part of the fix.

**Don't over-generalize the ledger into censorship.** Flagging every repeated bigram will bury legitimate recurrence, such as a character's name, a motif you *want*, or ordinary connective words. Keep the threshold sane, protect proper nouns, and treat the ledger as a nudge, not a ban list.

**Facts must actually be factual.** If your "summaries" are lightly compressed prose rather than plain notes, you've just re-created the problem at lower resolution. A summary that reads like a paragraph of the model's voice is still a voice to copy.

## Related

- [The core idea](../the-core-idea.md)
- [Self-cannibalizing repetition](../vices/self-cannibalizing-repetition.md)
- [Tonal drift](../vices/tonal-drift.md)
- [Voice homogeneity](../vices/voice-homogeneity.md)
- [Aphorism & oracle voice](../vices/aphorism-and-oracle.md)
- [Separate planning from prose](separate-planning-from-prose.md)
- [Ground voice in real exemplars](reference-real-exemplars.md)
- [Schema gates](schema-gates.md)
