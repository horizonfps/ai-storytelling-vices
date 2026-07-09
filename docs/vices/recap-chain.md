_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Recap Chain

> Opening a beat by re-listing what already happened: "having done X, you now Y" throat-clearing that summarizes the story back to the reader instead of moving it forward.

**Also known as:** recap-chain enumeration, milestone recap, beat-list recitation, "as you know" narration, progress-ladder, playback of the prompt, input echo.
**Family:** structural

## The tell

The prose stops to inventory prior events before it lets the current one happen. It shows up in a few recognizable shapes:

- **The throat-clear.** A beat opens with a participial wind-up that restates the last beat: "Having freed the prisoner and taken the map, you turn to the door."
- **The ladder.** A short list of past milestones stacked as escalation: "First the theft. Then the fire. Then the missing witness. And now this."
- **The playback.** A character answers by re-listing what the other person just did or said, sometimes echoing their exact words: "So you walked in, demanded the ledger, and now you want my silence too."
- **The single echo.** A softer version, where the reply just parrots one keyword the other party used, handing it straight back.

Quick mental test: **strike the first sentence (or clause) of the beat. If nothing is lost, if the scene starts fine one line later, it was recap.** A second test for dialogue: does the character react to what the other person *wants*, or to a transcript of *how they phrased it*? If it's the transcript, it's playback.

## Before / after

**Noir**

```text
Before (viced)
Having tracked the accountant across three boroughs, bribed the night
clerk, and talked her way past the doorman, Reyes finally stood outside
apartment 9C. Everything had led here. She knocked.
```

```text
After
Reyes knocked on 9C. Somewhere inside, a chair scraped, then went still,
someone deciding whether to be home. She knocked again, softer, the way
you knock when you already know the answer is yes.
```

**High fantasy (dialogue playback)**

```text
Before (viced)
"Let me get this straight," the toll-keeper said. "You crossed the Ash
Marsh, you lost two of your company, you carried a wounded man on your
back for a day and a night, and now you're asking me to open the gate
for free."
```

```text
After
The toll-keeper looked at the mud caked to their knees, the wounded man,
the empty coin-purse, and didn't reach for the gate. "Everyone who comes
to me this side of ruin thinks the crossing already bought them
something." He spat. "It didn't."
```

The *After* versions don't just cut words. The noir one replaces summary with a live, tense detail (the chair, the second knock) that carries the whole chase implicitly. The fantasy one lets the toll-keeper react to the *ask* through his own worldview, which characterizes him and raises the stakes in one move.

## Why models do this

Two training pressures converge here.

First, the model is rewarded for looking attentive. Playing the input back, re-listing the player's actions and echoing their nouns, is a cheap way to demonstrate "I registered everything you said." It reads to the model as grounded, responsive writing, but it's really the assistant reassuring itself (and you) that it was paying attention.

Second, enumerated escalation is a stock dramatic rhythm ("first this, then that, then *this*"), so the model reaches for it whenever it wants a beat to feel weighty. When the model is also holding a lot of tracked state in context, including recent events, milestones, and a running plot, it tends to surface that state as narration rather than let it stay implied.

There is also a self-feeding version: when a tool feeds the model its own recent prose as "memory," any recap habit gets mirrored and amplified turn over turn. That loop is covered in [anti-autophagy memory](../techniques/anti-autophagy-memory.md).

## Why it hurts

Recap stalls forward motion at the exact moment a beat should start moving. It also condescends: the reader just lived those events, so recounting them signals the narrator doesn't trust them to remember. In interactive fiction the playback form is worse, because it turns a character into an echo of your own turn instead of an agent with their own agenda, and the scene reads as summary rather than response. Repeated across a session, the ladder rhythm makes every climax sound identical, and the whole piece takes on the cadence of a "previously, on…" segue.

## Fix it while writing with an AI

- **Name the ceiling, not the recap.** Tell the assistant: "Start each beat from the new event. Assume I remember what just happened; do not summarize prior turns." Affirmative framing works better than a list of banned phrases.
- **For dialogue, redirect to want.** "When a character replies, have them react to what the other person *wants* or to the stakes, not by repeating the words or actions I described. A pointed question, a refusal, a worldview reframe, or an action all work."
- **Request a self-check.** Ask the model to confirm before it writes: "First line starts on the new event; no restatement of prior beats; the reply doesn't echo my keywords."
- **Paste back a targeted fix.** If a beat opens with "Having done X, you now…", paste that line and say: "Cut this opener and begin on the first fresh action instead."
- **Watch the single echo.** Even one repeated noun counts. If you asked for a "map" and the shopkeeper opens with "A map, is it?", nudge: "Have them respond to why I'd want it, without repeating the word."

## Fix it while building a tool

Recap is partly a prose vice and partly a *memory-architecture* problem, so treat it on two fronts.

**On the prose side**, add a required self-audit field to the writing tool's schema keyed to this vice. The key names the vice; the value is an affirmative snake_case commitment; the actual rule lives in the field description, never as a copyable bad example in the enum. The gain comes from the model re-reading the commitment before it emits, not from reliable self-detection. See [schema gates](../techniques/schema-gates.md).

```jsonc
{
  "pre_emit_audit": {
    "type": "object",
    "required": ["reply_mode", "no_recap_chain"],
    "properties": {
      "no_recap_chain": {
        "type": "string",
        "enum": ["i_advance_from_the_new_event_without_reciting_prior_beats"],
        "description": "Open each beat on the current event. Do not summarize prior turns, stack a ladder of past milestones, or start with a 'having done X' wind-up."
      },
      "reply_mode": {
        "type": "string",
        "enum": ["the_speaker_reacts_to_the_want_not_a_replay_of_the_input"],
        "description": "A character reacts to what the other party wants or to the stakes, not by re-listing their actions or echoing their keywords."
      }
    }
  }
}
```

**Scope by output kind.** Some roles legitimately summarize, such as an epilogue, a "previously" recap screen, or a time-skip opener. Gate by a declared `prose_kind` so the rule fires only on live scene prose and leaves by-design summaries alone.

**Back it with detection.** For the playback form, a cheap post-generation check flags high keyword overlap between the player's last line and the reply, then triggers a targeted regeneration. See [post-hoc validation and regeneration](../techniques/post-hoc-validation-and-regen.md).

**On the memory side**, don't feed the writer its own raw recent prose as context, because that self-conditioning loop amplifies recap and other tics. Feed distilled factual summaries for continuity and keep any raw-prose window small and scene-scoped. See [anti-autophagy memory](../techniques/anti-autophagy-memory.md).

**Keep gates on the writer only.** A planner that emits factual notes should not carry prose gates; route the audit to the component that actually writes prose. See [planner/prose separation](../techniques/separate-planning-from-prose.md).

## Watch-outs

- **Don't over-correct into amnesia.** The goal is to *not restate* what the reader saw, not to drop continuity. A single grounding detail that implies the past ("she knocked the way you knock when you already know") is fine; a bulleted history is not.
- **The single echo hides.** Teams often write an anti-recap rule and still let a lone keyword slip through; the model has even echoed a keyword inside the "good example" meant to illustrate the fix. Treat one repeated noun as the whole vice.
- **Residual ceiling.** Even with a gate plus detection, strong models still slip on a small fraction of turns. Accept the residue rather than stacking ever-narrower rules, which dilutes every gate and eats output-token headroom.
- **It morphs into siblings.** The ladder form is one step from rule-of-three parallelism, and a recap that ends on a summarizing verdict becomes an [aphoristic label at the close](aphorism-and-oracle.md), so watch for both when you suppress the recap.

## Related

- [The core idea](../the-core-idea.md)
- [Aphoristic label at the close](aphorism-and-oracle.md)
- [Dialogue fragmentation](dialogue-fragmentation.md)
- [Schema gates](../techniques/schema-gates.md)
- [Post-hoc validation and regeneration](../techniques/post-hoc-validation-and-regen.md)
- [Anti-autophagy memory](../techniques/anti-autophagy-memory.md)
- [Planner/prose separation](../techniques/separate-planning-from-prose.md)
