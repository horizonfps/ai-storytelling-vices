# AI Storytelling Vices

A portable field guide to the prose failure modes of language models. It covers the habits that survive every "please stop doing that," along with the fixes that actually hold.

If you have ever asked an AI for a scene and gotten back writing that is technically fine yet somehow all the same, you have met these vices. Characters who all speak in the same clipped murmur. Every gesture followed by a clause that explains what it meant. Beats that end on a tidy little maxim. A world that never moves unless the hero pokes it. These patterns are consistent, recognizable, and, once you know the trick, fixable. This repo names them, shows you how to spot each one, and gives you concrete fixes for both writing in a chat and building a tool.

> ### The one idea, in a sentence
>
> Stubborn AI prose vices are not killed by piling on more "don't do X" rules; the durable fix is to make the model re-state an affirmative commitment (what it *will* do) in its own output, right before it writes. The gain comes from that reflexive re-read at the moment of generation, not from the model detecting its own mistake.
>
> This is the spine everything else hangs on. Read it first: **[The Core Idea: The Reflexive Forcing Function](docs/the-core-idea.md)**.

## Who this is for

This guide serves two audiences, and every document speaks to both.

- **Writers who generate fiction with an AI in a chat window:** novelists, short-story writers, worldbuilders, game masters, and hobbyists. You don't need a codebase. Each vice comes with a plain-language tell so you can recognize it, and a chat-ready fix you can paste into your next message.
- **Developers building AI narrative tools, games, or engines.** Each vice also comes with a systematic fix: schema-level gates, post-generation checks, memory architecture, and the testing discipline to know whether a fix actually worked. The techniques section is your build manual.

The vices are the same whoever is at the keyboard. Only the lever differs: a message for the writer, a schema field or a pipeline stage for the builder.

## How to navigate

- **[The Core Idea](docs/the-core-idea.md)** covers the reflexive forcing function, why plain rules under-deliver on prose, and both the chat and tool versions. Start here regardless of which audience you're in.
- **[Cheatsheet](docs/cheatsheet.md)** puts the whole catalog on one page: each vice, its one-line tell, and its fastest fix. Keep it open while you write or debug.
- **[Vices catalog](docs/vices/)** is the field guide proper, with one document per vice: the tell, a quick mental test, original before/after examples across varied genres, the root cause, and both fixes.
- **[Techniques](docs/techniques/)** collects the reusable methods the vice docs draw on: reflexive schema gates, post-hoc validation, principles over rules, grounding voice in real exemplars, anti-autophagy memory, separating planning from prose, and how to test fixes honestly.
- **[Glossary](docs/glossary.md)** holds the shared vocabulary (vice, tell, forcing function, autophagy, pink elephant, register, model ceiling, and more), so every doc reads the same way.

## Start here

**If you write fiction with an AI in a chat:**

1. Read [The Core Idea](docs/the-core-idea.md); the manual, by-hand version of the fix is near the end.
2. Skim the [Cheatsheet](docs/cheatsheet.md) and find the two or three vices that bother you most.
3. Open those entries in the [vices catalog](docs/vices/) and use the quick-fix section. Good places to start: [Contrastive Reveal](docs/vices/contrastive-reveal.md), [Gesture Gloss](docs/vices/gesture-gloss.md), and [Aphorism & Oracle Voice](docs/vices/aphorism-and-oracle.md).

**If you build AI narrative tools:**

1. Read [The Core Idea](docs/the-core-idea.md), then [Reflexive Schema Gates](docs/techniques/schema-gates.md) for the mechanics.
2. Read [No Pink Elephant](docs/techniques/no-pink-elephant.md) and [Principles Over Rules](docs/techniques/principles-over-rules.md), the two mistakes that undo most attempts.
3. Pick the vices you actually see in your outputs from the [catalog](docs/vices/) and apply their systematic fixes. For diffuse or semantic vices, pair the gate with [Post-Hoc Validation and Regeneration](docs/techniques/post-hoc-validation-and-regen.md), and measure honestly with [Testing Vices and Accepting Ceilings](docs/techniques/testing-vices-and-ceilings.md).

## How to use these fixes

A few principles carry across the whole guide, so it helps to hold them from the start:

- **State fixes as what to do, not what to avoid.** Quoting the bad pattern to forbid it tends to make the model reproduce it. Prefer affirmative instructions and keep literal bad examples out of anything you feed the model. The labeled "before" examples in these docs are for *you*, the human reader; never copy one into a prompt or schema.
- **Match the lever to the vice.** Syntactic vices (a recognizable phrase shape) respond to a reflexive gate and a targeted post-generation check. Semantic and diffuse vices (a whole tone cooling over a session) are architecture and grounding problems, not one-rule problems.
- **Accept a ceiling.** Even a good fix leaves a small residue on the hardest vices. Set your bar at what the model can actually reach and stop; over-tightening usually costs more than it returns.
- **Change one thing and check.** After a fix, re-read against an early passage or re-run your full scenario set. A pattern you thought you eliminated can reappear in a passage you weren't checking, so after each change re-scan broadly, not only the spot you meant to fix.

## Contributing

Contributions are warmly welcome: a new vice you keep hitting, a sharper tell, a cleaner original example, a technique that worked for you, or a fix for one that didn't.

Two house rules keep the guide portable and honest:

- **Keep it general.** Every lesson must stand alone as craft advice. No references to specific projects, games, or franchises; replace any source-specific example with an original, neutral one.
- **Don't commit the vices you document.** Write in plain, warm English, use affirmative instructions, rotate examples across genres, and follow the vocabulary in the [glossary](docs/glossary.md).

Open an issue to propose or discuss a change, or send a pull request. If you're adding a vice, mirror the structure of an existing entry in the [catalog](docs/vices/).

## License

Prose and documentation are licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/); code snippets, schema fragments, and configuration examples are licensed under the MIT License. See [LICENSE](LICENSE) for the full text.
