_Part of the [AI Storytelling Vices](../../README.md) field guide._

# Post-Hoc Validation and Regeneration

> Some vices slip past every pre-write gate; catch them with a check AFTER the model writes, then regenerate the offending passage.

## The problem it solves

A [reflexive schema gate](schema-gates.md) works by making the model re-read a commitment right before it writes. That works beautifully on vices with a recognizable *surface shape*: the "not X, but Y" reveal, the em-dash aside, the maxim tacked onto the end of a beat. The model can be steered off a phrase-level pattern.

But a whole class of vices is *semantic*, not syntactic. The model does not perceive them as faults, so priming it with a commitment changes almost nothing. It marks itself clean and writes the vice anyway. These are the ones you catch after the fact:

- [Psychological simile](../vices/gesture-gloss.md), a gesture followed by an "as if" or "like someone who" clause that reads the character's mind. A gate against it clears maybe a third of cases; the rest need a detector.
- [Aphorism and oracle](../vices/aphorism-and-oracle.md), a portable maxim standing in for a specific observation. The model believes it wrote something wise, not something canned.
- [Onomatopoeia spam](../vices/onomatopoeia-spam.md) and other *excess* vices, where the failure is quantity, not presence. A pre-write suppressor over-corrects into a total ban; a downstream reviewer that flags "too many" is the right tool.
- [Self-cannibalizing repetition](../vices/self-cannibalizing-repetition.md), a pet word or image recurring across passages. You only see it by counting across the whole output, which a single-pass gate cannot do.

Post-hoc validation is also where you put continuity checks, such as stale descriptions or a protagonist's choice authored for the player, which a pattern match or a second model can catch by reading the finished text against the current state. The pre-write gate *prevents*; this technique *catches the residue*. Layer detection behind prevention.

## How it works

The mechanism is a loop: generate, check the output, and on a hit either regenerate or hand the passage to a lightweight reviewer that flags what to fix. The check is deterministic where it can be, a regular expression matching the tell-tale construction, and a model-judge where the pattern is too fuzzy to pin down.

Why it works when the gate does not: the gate depends on the model *recognizing* the vice at write time, and for semantic faults it simply does not. The detector removes the model from the judging seat. A regex does not care whether the model thinks "grief is love with nowhere to go" is profound; it sees the copula-plus-abstract-noun shape and rejects it. You have moved the decision from the model's flawed self-perception to an external rule you control.

Two things make the loop trustworthy. First, scope the detector tightly to the actual surface forms so it does not nuke legitimate prose. A physical simile ("gasping like a diver breaking the surface") is good craft, and only the *psychological* one is the target. Second, cap the retries and accept the residue. The detector plus one or two regenerations closes most of the gap; chasing the last few percent burns tokens and latency for little gain.

One more move belongs here, and it is easy to forget: **remove the upstream invitation.** If the prompt asks for "the character's inner voice," a detector fighting that instruction is swimming upstream. Strip the phrase that invites the vice, then let the validator mop up what remains.

## Manual version (in a chat)

You cannot wire a regex into a chat window, but you can run the loop by hand, and the discipline transfers cleanly. Do a dedicated second read whose *only* job is to hunt for one named tell.

After the model gives you a passage, do not accept it in the same breath you read it for story. Read it again looking for a single specific pattern, then ask for a targeted rewrite. For example:

> Reread the scene you just wrote. Find every place a gesture is followed by a clause that interprets it, such as "as if," "like someone who," or "the look of a person who." Rewrite each so the gesture stands alone and the meaning lands through the action or another character's reaction. Change nothing else.

The narrowness is the whole point. A vague "make it better" invites the model to rewrite the parts that were already fine and re-introduce fresh vices. Naming one pattern and forbidding collateral edits keeps the fix surgical.

If a tic keeps returning turn after turn, keep a short personal list of your known tells and run this second-read pass as a habit before you accept any passage, the same way a copy editor reads once for sense and again for a single class of error.

## Systematic version (in a tool)

Wrap generation in a validate-and-retry loop. Match the known surface forms; on a hit, re-request the same unit, optionally with a short affirmative nudge. Keep the detector curated, because a blunt pattern catches false positives and rejects clean prose.

```python
import re

# High-confidence surface forms of the psychological-simile gloss.
# Note the affirmative framing of the regen hint: no copyable bad example.
GLOSS_PATTERNS = [
    r"\bas if\b",
    r"\blike someone who\b",
    r"\bthe (?:look|voice|expression) of (?:a|an|someone)\b",
    r"\bthe way a (?:person|man|woman) (?:does|would) when\b",
]
# Exclude bare "of someone"/"of whom" so "the name of whoever opened it" is safe.

def has_gloss(text: str) -> bool:
    return any(re.search(p, text, re.IGNORECASE) for p in GLOSS_PATTERNS)

REGEN_HINT = (
    "Show each gesture and stop. Let the meaning land through the action itself "
    "or another character's reaction."
)

def write_scene_validated(model, prompt, max_tries=3):
    text = model.write(prompt)
    tries = 1
    while has_gloss(text) and tries < max_tries:
        text = model.write(prompt + "\n\n" + REGEN_HINT)
        tries += 1
    return text  # accept the residue after the cap
```

For fuzzier vices where no regex is honest, such as a maxim that is not lexically fixed or "the cast all sound the same," swap the pattern check for a **second-pass reviewer**: a separate, cheap model call whose only job is to read the finished passage and return a short list of flags. Give that reviewer the full state it needs (complete character records fresh from your store, the protagonist's record, recent events), not a thin summary, or it will miss stale-description and continuity faults. Then feed its flags back as a targeted regen.

Notes that decide whether the loop pays off:

- **Budget for the cost.** A regeneration roughly doubles the tokens for that unit. Reserve the loop for vices that matter and that prevention cannot close; do not validate everything.
- **Count rejections.** Track how often the detector fires. That number is your real vice rate, far more honest than the model's self-report from a [schema gate](schema-gates.md).
- **Pair with prevention.** The gate and the detector are complementary. The gate cuts the volume cheaply at write time; the detector guarantees the floor. Semantic vices want both.

## Pitfalls

**A blunt regex over-matches.** If your pattern is a bare fragment, it will reject good sentences and loop forever. Scope each pattern to the true surface form and add explicit exclusions for the legitimate uses. Test the detector against a set of *clean* passages, not only dirty ones, and confirm it stays quiet.

**Do not confuse "shorter" with "fixed" on regen.** A model told to strip a clause will sometimes gut the whole gesture. Frame the regen hint affirmatively, saying what to keep and how to carry the meaning, so the rewrite is better prose, not just a deletion.

**Cap the retries.** An uncapped loop can spin on a vice that sits at the model's ceiling. Set a small limit, accept the residue past it, and move on. There is a real floor you will not cross by regenerating harder.

**A too-broad reviewer homogenizes voice.** If a second-pass model is told to "improve" freely, it sands off the very eccentricity you want and imposes its own defaults. Keep its remit to the named vices you are hunting. It is a fault-finder, not a co-author.

**Some vices are tail events of rich context.** A few faults, such as inventing off-canon props or over-specifying a vague hook, barely reproduce in a bare test harness, because an upstream stage disarms the trap before the writer sees it. Validate these in the full pipeline or in real play, not in an isolated fixture, or you will build a detector for a problem that only appears when the context is thin.

**Prefer the cheaper tool first.** If a vice has a clean surface shape, a [reflexive gate](schema-gates.md) at write time is cheaper than a regenerate loop. Reach for post-hoc detection when the gate measurably fails, for semantic and excess vices, not as a first resort for everything.

## Related

- [The core idea](../the-core-idea.md)
- [Schema gates](schema-gates.md)
- [No pink elephant](no-pink-elephant.md)
- [Principles over rules](principles-over-rules.md)
- [Gesture gloss](../vices/gesture-gloss.md)
- [Aphorism & oracle voice](../vices/aphorism-and-oracle.md)
- [Onomatopoeia spam](../vices/onomatopoeia-spam.md)
- [Self-cannibalizing repetition](../vices/self-cannibalizing-repetition.md)
