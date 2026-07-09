_Part of the [AI Storytelling Vices](../README.md) field guide._

# Glossary

Shared vocabulary for the repo. Terms are alphabetized. Where a term has a fuller treatment, a relative link points to it.

Two audiences use these words: writers steering an AI in a chat window, and developers wiring an AI into a narrative engine. Most terms serve both.

---

### Affirmative commitment

The value you put inside a [reflexive gate](techniques/schema-gates.md): a short, positive, snake_case phrase that states what the model WILL do (`i_show_the_gesture_and_stop`), rather than what it must avoid. Phrasing it as a positive promise sidesteps the [pink-elephant](#pink-elephant) problem and gives the model a clean commitment to re-read before it writes.

### Anti-example

A sample of bad output shown to illustrate a vice. Safe and useful for a human reader in a document (clearly labeled "Before"). Dangerous inside a prompt or schema the model reads, where a literal bad example tends to get reproduced. See [pink elephant](#pink-elephant).

### Auditor

See [second-pass reviewer](#second-pass-reviewer--auditor).

### Autophagy (self-cannibalizing prose)

The decay that sets in when a model is fed its own recent raw output as context and imitates it, amplifying its own tics turn after turn. Vocabulary narrows, imagery repeats, and register flattens over a long session. Fix by feeding factual summaries instead of raw prose, capping the recent-prose window, and keeping an [imagery ledger](#imagery-ledger). Fuller treatment: [autophagy and memory](techniques/anti-autophagy-memory.md).

### Before / After

The paired examples in a vice doc: the flawed version and the repaired one. A good "After" is better writing, not just shorter: it trusts the image, varies its rhythm, and keeps the concrete detail that was doing the work.

### Contrastive reveal

The theatrical "not X, but Y" construction, where the prose denies a foil to make the real thing land as a twist. A common and recognizable machine tell. Fuller treatment: [Contrastive reveal](vices/contrastive-reveal.md).

### Diegetic

Belonging inside the fiction. A diegetic detail is one a character could perceive or think within the story world. The opposite is [system-vocabulary bleed](#system-vocabulary-bleed), where engine terms leak into the prose.

### Exemplar grounding

Steering a model's voice or range by showing it real, high-quality, verbatim reference passages, rather than samples you invented. Invented examples tend to carry the vices you are fighting; real ones anchor register without teaching tics. Fuller treatment: [Ground voice in real exemplars](techniques/reference-real-exemplars.md).

### Few-shot planting

The failure where an in-prompt example teaches the model to reproduce the example's flaws. A mediocre invented sample becomes the quality ceiling, and every rewrite of it risks seeding a fresh tic. Related to [pink elephant](#pink-elephant); the fix is to prefer principles or real exemplars over synthetic samples.

### Forcing function

Any required element that makes the model produce or reconsider something it would otherwise skip. A [reflexive gate](#reflexive-gate--schema-gate) is a forcing function for prose discipline; a required "generate-or-hold this turn" field is a forcing function for world events. The power is in the obligation to look, not in the model judging itself accurately.

### Gate

See [reflexive gate](#reflexive-gate--schema-gate).

### Gloss

A clause tacked onto a shown gesture that interprets its meaning for the reader ("as if," "like someone who," "the look of a person who"). It converts subtext into caption and steals the reader's inference. Fuller treatment: [Gesture gloss](vices/gesture-gloss.md).

### Grimdark drift

See [tonal drift](#tonal-drift).

### Idiolect

A model's distinctive stylistic fingerprint: the words and images it returns to across outputs (a favored "resonant," a recurring "hollow ache"). Suppressing a pet word in one scene often pushes it into an adjacent one (the balloon effect), so track it with an [imagery ledger](#imagery-ledger) rather than a single ban.

### Imagery ledger

A running list of phrases, images, or structural words the model has overused, fed back with an instruction to vary them. A bookkeeping tool: it counts repetition and asks for divergence, but decides no content. Central to fighting [autophagy](#autophagy-self-cannibalizing-prose).

### Model ceiling

The real, per-model limit on how completely a stubborn vice can be suppressed. Strong models plateau in the low-to-mid nineties percent; weaker ones lower. Past the ceiling, more rules dilute the ones that work and can distort other behavior. Measure the residue, accept it, and do not loosen your test bar to hide it.

### Onomatopoeia / SFX

Written sound effects and impact words. The failure mode is excess, not presence: one per action beat reads as parody in continuous prose. The correction is parsimony, not a total ban; a blanket ban over-corrects and can even flatten ordinary exclamation marks. Handle spam with a [second-pass reviewer](#second-pass-reviewer--auditor), not a suppressor on the writer.

### Pink elephant

The effect where naming or showing a forbidden pattern makes the model produce it (like being told not to think of a pink elephant). Keep prohibitions abstract and affirmative; keep literal bad examples out of anything the model reads. One sharp exception: a specific forbidden lexical TOKEN must be named explicitly, since abstracting it away makes the leak worse. Fuller treatment: [No pink elephant](techniques/no-pink-elephant.md).

### Planner / prose separation

An architecture that splits the component making factual decisions (who is present, what changes) from the component writing prose. Prose gates belong only on the writer; the planner's fields are plain facts. Keeps discipline where it bites and stops factual channels from turning purple.

### Purple prose

Overwrought, ornamental writing that foregrounds the writer's cleverness over the scene: piled adjectives, strained metaphor, decorative abstraction. Many catalogued vices ([gloss](#gloss), [psychological simile](#psychological-simile), [aphorism](#aphorism--oracle-voice)) are specific, nameable species of it.

### Register

The tonal and emotional key of the prose: warm or cold, loud or hushed, comic or grave, plain or ornate. Most drift problems are register problems: the output is well-written but in the wrong key for the story. What a prompt SHOWS sets register far more than what it STATES.

### Reflexive gate / schema gate

A required field on a writing tool's output schema whose key names a vice and whose value is an [affirmative commitment](#affirmative-commitment) the model states just before it writes. The gain comes from re-reading the commitment (the model almost always marks itself "ok"; the re-read is the mechanism). Works well for syntactic vices, weakly for diffuse ones. Fuller treatment: [Schema gates](techniques/schema-gates.md).

### Second-pass reviewer / auditor

A separate check that runs after generation to catch what the writer missed: [SFX](#onomatopoeia--sfx) spam, stale continuity, agency theft, high input overlap. To judge continuity it must read the full current state of everything on stage, not a thin summary. Fuller treatment: [Second-pass reviewer](techniques/second-pass-reviewer.md).

### Sibling-vice fusion

Folding related vices that share a root (fragmentation, rule-of-three, and verb-period-verb are one cadence family) into a single gate rather than one gate each. Preserves the reflexive punch and conserves the output-token budget; too many gates dilute every gate.

### System-vocabulary bleed

When engine machinery (turns, logs, triggers, sessions) surfaces inside in-world prose, breaking the fourth wall. Worst in sparse, cold-start scenes where the model has little to say and grabs the nearest available words, which are its own scaffolding. The fix is a [diegetic](#diegetic)-only rule on every text-bearing field.

### Tell

The observable surface signature of a vice: the specific phrasing, punctuation, or structure that lets you spot it in the wild. Each vice doc opens with its tell so you can recognize the pattern before you know its cause.

### Tonal drift

(Also called grimdark drift.) The slow slide of a warm, wondrous, or high-energy register toward a cold, restrained, prestige-noir one, well-written sentence by sentence but in the wrong genre for the story. Strong models are the most susceptible, because they imitate what the prompt demonstrates over what it asserts. Fix the demonstrations and the input seeds rather than stacking more tone rules.

### Vice

A recurring prose or storytelling failure mode of a language model: a habit the model reaches for because it reads as competent, that actually cheapens the writing. Each is documented with its [tell](#tell), why the model does it, why it hurts, a quick fix, and a systematic fix.

### Zero-shot

Asking a model to perform a task from instructions alone, with no worked examples. The safe default for prose, since examples so readily [plant vices](#few-shot-planting). When you do need a concrete anchor, prefer a real, high-quality one over an invented sample.

---

## Named vices (quick index)

Short definitions for specific failure modes. Most are catalogued in [vices/](vices/) with a full doc; a few are sub-forms or siblings noted here.

### Aphorism / oracle voice

Closing a concrete moment on a portable maxim, or writing a character who speaks only in polished proverbs. Watch for a beat that ends on general wisdom instead of the specific thing that just happened, and cut the maxim so the concrete moment closes.

### Dialogue fragmentation

Prose or speech shattered into hard-stopped shards where commas and conjunctions belonged, so a character sounds breathless. The fix rejoins the shards with the punctuation the sentence needs and gets its precision from word choice rather than hard stops.

### Player-voice usurpation

In interactive fiction, the narrator authoring the protagonist's choices, dialogue, or resolve, instead of stopping at the decision point. It steals the one thing that belongs to the human co-author.

### Psychological simile

A "like someone who / as though" comparison that reaches into a character's mind and states the feeling the gesture already implied. A sub-form of the [gesture gloss](vices/gesture-gloss.md); it tends to surface once the blunter emotion-naming vices are closed.

### Recap-chain enumeration

A reply or narration that re-lists what just happened, often echoing the other party's exact words, instead of reacting to what they want. It turns a character into a mirror of the prompt.

### Rule-of-three parallelism

Three short parallel clauses fired in a row for rhythm as a default cadence. A legitimate move once at a peak; a metronome when it becomes the house beat.

### Stale-state description

Describing a character or place by a stored attribute that events have since changed (still shackled after being freed). A continuity break the writer cannot see without a reconciled record.
