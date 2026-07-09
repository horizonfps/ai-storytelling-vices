_Part of the [AI Storytelling Vices](../../README.md) field guide._

# No Pink Elephant

> Do not name the bad thing in an instruction. Quoting the pattern you want to suppress teaches the model to produce it. State the rule affirmatively, and keep every literal bad example out of prompts and enums.

## The problem it solves

"Don't think of a pink elephant." Now you are thinking of one. Language models have the same problem, and worse: an instruction is training data for the current turn. When you write a rule that spells out the exact prose you are trying to avoid, whether by quoting a sample sentence, listing a forbidden phrase in a header, or embedding it in a tool schema, the model reads that vivid, concrete pattern and treats it as an available shape. It reproduces the thing you forbade, sometimes word for word.

This technique targets the meta-failure behind almost every stubborn prose vice: **the fix that plants the vice.** It applies whenever you try to suppress a style tic through instruction. The vices most often mishandled this way include:

- [Contrastive reveal](../vices/contrastive-reveal.md): a "don't write 'not X, but Y'" rule that quotes the frame teaches the frame.
- [Aphorism and oracle voice](../vices/aphorism-and-oracle.md): a sample maxim used as an anti-example becomes a maxim the model coins.
- [Gesture gloss](../vices/gesture-gloss.md): quoting an "as if she already knew" clause anchors that exact clause.
- [Onomatopoeia spam](../vices/onomatopoeia-spam.md): a literal sound-word in a ban list both teaches the sound-word and, in practice, cross-suppresses ordinary exclamation marks the model files under the same heading.
- [Epithet naming](../vices/epithet-naming.md) and worldbuilding sameness: a single "here's a good example" template becomes the mold every generated place or name collapses toward.

It is the discipline that makes [schema gates](schema-gates.md) safe to use, and it partners closely with [exemplar grounding](reference-real-exemplars.md), which handles the flip side. When you genuinely need a concrete anchor, use real, verified text rather than an invented sample.

## How it works

The mechanism is simple and the causal story is worth holding onto.

A model generates by continuing the most probable pattern given its context. Everything in the context, including your instructions, raises the salience of the tokens it contains. When you write a prohibition around a concrete example, you have placed that example in the context. The negation ("do not") is a weak, abstract signal; the example is a strong, concrete one. The strong signal wins. The model attends to the shape you quoted and drifts toward it, because attending-then-continuing is what it does.

Affirmative instructions dodge this entirely. "Assert the real thing directly" gives the model a target to move *toward*, and it contains none of the bad pattern. "State smells and tastes drawn from the world the character actually lives in" steers the sensory writing without ever naming the cliché you fear. There is nothing to anchor on because you never wrote it down.

Two consequences that surprise people:

- **A literal bad example can cross-suppress a legitimate feature.** In field testing, a sound-effect ban that quoted a sample sound-word did not just teach the sound-word. It also flattened ordinary exclamation marks across the prose, because the model generalized the prohibition to the punctuation it associated with the quoted token. The ban generalized past its target: the model filed exclamation marks under the same heading as the quoted sound-word and suppressed both.
- **Invented examples are "born with the vice."** When a model (or a hurried author) writes a sample paragraph to illustrate good style, that sample tends to carry the very tics you are fighting, and then leaks them into output. This is why fabricated few-shots are risky and real verified excerpts are safer, as covered in [exemplar grounding](reference-real-exemplars.md).

There is one sharp exception, and you must hold it separately (details under Pitfalls). A **single forbidden lexical token**, one specific word you must never emit, has to be named explicitly. Abstracting it ("a certain informal pronoun") makes the leak far worse. Pink-elephant discipline governs *stylistic shapes*, not a lone banned word.

## Manual version (in a chat)

You do not need a codebase to apply this. When you steer an AI writing partner, phrase your guidance as what you want, not as a quoted sample of what you dread.

- Instead of "don't write ominous one-liners like 'the dark was patient that night,'" say: **"End each scene on a concrete action or image. Let the last thing that happens carry the mood."**
- Instead of "avoid 'not X, but Y' sentences such as 'it wasn't grief, it was arithmetic,'" say: **"State feelings and facts directly. Assert the real thing on its own."**
- Instead of pasting a paragraph you wrote as "here's the voice I want," point the model at a real passage you admire and say **"study this register; do not copy its words."**

If you must reference a bad shape for clarity, describe its *structure* abstractly ("a clause after a gesture that explains what the gesture means") rather than quoting an instance. And watch your own messages: if you keep repeating "no melodrama, seriously, no melodrama," you are painting the elephant larger. Say it once, cleanly, and move on. Over a long chat, refresh the affirmative target rather than re-listing the bans.

One caveat matches the systematic exception. If there is a specific word you never want (a slur, a modern brand name in a period piece, one out-of-register pronoun), name that exact word plainly. The abstraction trap only bites on style; a single token needs to be pointed at directly.

## Systematic version (in a tool)

In a structured prompt or tool schema, enforce two rules mechanically: **affirmative values only**, and **the bad pattern lives in prose description, never in a copyable slot.**

When you build a [schema gate](schema-gates.md), the enum value the model echoes must be a clean positive pledge. The rule, including the shapes to avoid, belongs in the field's `description`, phrased abstractly. Never put a quotable bad sentence inside an `enum`, a header, or an anchor example the model reads verbatim.

```python
# WRONG: plants the vice by quoting it in a place the model imitates
audit_field = {
    "type": "string",
    "enum": ["avoid_lines_like_it_wasnt_fear_it_was_something_colder"],  # teaches the frame
    "description": "Do not write things such as: 'It wasn't fear. It was something colder.'",
}

# RIGHT: affirmative value, abstract rule in the description
audit_field = {
    "type": "string",
    "enum": ["i_state_what_is_true_without_negating_a_decoy_first"],
    "description": (
        "Assert the real thing directly. Do not define something by first "
        "denying an alternative to stage a reveal, in any punctuation."
    ),
}

# A cheap pre-ship lint: catch literal bad examples smuggled into slots the model imitates.
FORBIDDEN_IN_SLOTS = ("e.g.", "like:", "such as:", "for example")

def lint_prompt_slot(text: str) -> list[str]:
    """Flag copyable anti-examples in enum values, headers, or few-shots."""
    problems = []
    if any(marker in text.lower() for marker in FORBIDDEN_IN_SLOTS):
        problems.append("possible literal anti-example in a slot the model imitates")
    return problems
```

Apply the same discipline to two adjacent habits:

- **Execution prompts are not design history.** Strip discarded options, "we rejected approach A," phase numbers, and dated rationale from the prompt body. A discarded idea in the active context is a pink elephant; the model has been seen to reproduce it as a pattern. Keep the audit trail in design docs; the prompt should assert only the current rule.
- **Use placeholders, not proper names.** In a rule illustration, write `NPC_A` or "a newly invented character," never a concrete name. Models reproduce quoted names in scenes where they make no sense.

For the one legitimate exception, a single hard lexical token, name it explicitly in the prompt and, if it still leaks, add a deterministic output sanitizer rather than abstracting the mention. See [schema gates](schema-gates.md) for how these fields sit in a real tool.

## Pitfalls

- **The token exception is real and it inverts the rule.** For a single banned word, explicit naming is required; removing the mention to "avoid planting it" has been measured to make the leak several times worse. Reserve pink-elephant avoidance for stylistic shapes, not lone lexical items.
- **One narrow style anti-example can act as a recognition anchor.** There is a niche case where a single, clearly-marked bad *style string* in prose (not in an enum, not a name, not a design option) helps the model self-recognize and stop. This is empirical and vice-specific, so test both forms against fresh cases before trusting it, and never place it inside a required enum value.
- **Don't over-correct into total suppression.** Fear of planting a pattern can push you to ban a whole device: every exclamation mark, all sound effects, any parallelism. That is a different, often worse failure. The goal is "state the rule affirmatively," not "forbid the feature."
- **Removing a bad example is a change, so measure it.** Deleting a planted anti-example often reduces the vice on its own, but confirm on real output. Intuition about what plants a vice is unreliable; check the before-and-after rather than assuming.
- **Repetition re-teaches.** Naming a vice once gives the model a handle; repeating it many times can produce a formulaic parrot of the correction. Say it cleanly, once, in the right place.
- **Your own instruction prose can commit the vice.** If a style guide tells the writer to avoid the em-dash aside while itself using em-dash asides, the model learns from the demonstration, not the imperative. Make the prompt's own prose obey the rules it enforces.

## Related

- [The core idea](../the-core-idea.md)
- [Schema gates](schema-gates.md)
- [Ground voice in real exemplars](reference-real-exemplars.md)
- [Post-hoc validation and regeneration](post-hoc-validation-and-regen.md)
- [Separate planning from prose](separate-planning-from-prose.md)
- [Contrastive reveal](../vices/contrastive-reveal.md)
- [Onomatopoeia spam](../vices/onomatopoeia-spam.md)
- [Epithet naming](../vices/epithet-naming.md)
