# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `analysis`

**Definition:** Makes an argument backed by a specific, checkable fact.

**Example 1:**

> Given Everton were deducted 10 points for one breach, I propose a 1140 point deduction. However, deducting that all in one season would be silly. Therefore: a 114 point deduction per season for the next decade.

**Example 2:**

> It was considered, that's just City PR spin. See paragraph 108 of the report 108) Those challenges and defences are considered in detail in Appendices 3, 27, 28 and 31. The conclusions that we reached were that a) The Club’s challenges to the PL’s ability to bring the Charges failed b) None of the matters raised by the Club provided it with a defence to any of the Charges

### `hot_take`

**Definition:** A confident claim with no support offered.

**Example 1:**

> EA I think should give him a hero card, great striker and his country's goat

**Example 2:**

> Is this sub just Ronaldo posts all the time now? What he's said, what other people have said, what he hasn't said, what he had for breakfast, whether his sister has a job. Isn't there a CR7 sub for this?

### `reaction`

**Definition:** An immediate response to something that just happened.

**Example 1:**

> Absolutely one of the best goals I have ever seen in the Aviva, I fucking love Parrott

**Example 2:**

> Didn’t see that coming

### `bot`

**Definition:** A comment made by a bot (a message automatically generated and posted by a computer program).

**Example 1:**

> \*\*Mirrors / Alternative Angles\*\* \*I am a bot, and this action was performed automatically. Please [contact the moderators of this subreddit](/message/compose/?to=/r/soccer) if you have any questions or concerns.\*

**Example 2:**

> \*\*FREE CONGO, SUDAN AND PALESTINE 🇸🇩 🇨🇩\*\* 🇵🇸

### The hardest boundary

**Which two labels:** `analysis` and `hot_take`

**The borderline post:**

> Spurs had 70% possession; that’s insane. Chelsea have no midfield. Alonso will be gone soon.

It opens with one checkable stat (70% possession) and then makes two confident claims that the stat only partly supports.

**The decision rule I used every time:**
If the post names at least one specific, checkable fact (a stat, a score, a date, a rule, a quoted report, or a described replay angle) and uses it to support its claim, it's `analysis`, even if the tone is heated and the rest of the post is opinion. If it makes a claim with no checkable fact behind it, it's `hot_take`. The borderline post above is `analysis`, because the 70% stat is the evidence for "Chelsea have no midfield."

**Second rule (`reaction` vs. the other two):** If the post's main point is a feeling about something that just happened, it's `reaction`, even if it mentions a fact in passing ("Can't believe we lost to the team bottom of the table 😭"). A fact only makes a post `analysis` when the post is arguing something with it.

### Stress test

I gave an AI tool my definitions and asked for eight posts that sit on my boundaries, then labelled each with the rules above.

| #   | Generated post                                                                                          | My label   | Clean?                                                            |
| --- | ------------------------------------------------------------------------------------------------------- | ---------- | ----------------------------------------------------------------- |
| 1   | Salah has 14 goal contributions in 9 games. Best player in the league, not even close.                  | `analysis` | Yes: the stat supports the claim                                  |
| 2   | WHAT A SAVE. He did that against Arsenal last year too, I swear he lives for these games.               | `reaction` | No: needed the second rule                                        |
| 3   | Refs have given us 3 penalties in 20 games. Absolute joke of a league.                                  | `analysis` | Yes: heated tone, but the fact is the argument                    |
| 4   | This manager is a fraud and everyone knew it the day he signed.                                         | `hot_take` | Yes                                                               |
| 5   | Rule 12 says handball only counts if the arm makes the body bigger. His arm was by his side. Not a pen. | `analysis` | Yes                                                               |
| 6   | Can't believe we just lost to a team bottom of the table 😭                                             | `reaction` | No: needed the second rule                                        |
| 7   | Honestly City should be relegated. They broke 115 rules.                                                | `analysis` | No: "115" is checkable, but they are charges, not proven breaches |
| 8   | Reminder: match thread rules apply, report spoilers to the mods.                                        | `bot`      | No: only if it was posted automatically                           |

**What I tightened:** posts 2 and 6 showed that "any checkable fact means `analysis`" was too broad, so I added the second rule: a fact only counts when the post argues with it. Post 8 shows that a mod notice typed by a human fits none of my labels. That's rare in my data, so I kept `bot` to mean automatically posted messages.

---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** Public comments from r/soccer, copied whole (not trimmed) into a single unsplit `labels.csv`.

**How I labelled them:** <!-- Cold first? Pre-labelled with AI and corrected?
Say so plainly — the disclosure is required, not penalised. -->
I labelled the first 29 comments cold, by hand, with no tools, using only my definitions. These are marked `cold` in the note column. An AI assistant then pre-labelled the remaining 229, following my definitions and the pattern in my first 29, and I double-checked every one of those labels. They're marked `pre-labelled (AI), reviewed`.

**Counts per label:**

| Label     | Count | Share |
| --------- | ----- | ----- |
| reaction  | 95    | 36.8% |
| hot_take  | 95    | 36.8% |
| analysis  | 47    | 18.2% |
| bot       | 21    | 8.1%  |
| **Total** | 258   | 100%  |

No label is above 70%. 18 of the 21 `bot` rows are the same AutoModerator "Mirrors / Alternative Angles" comment, so `bot` has only 4 unique texts.

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**

> _The post:_ "Spurs had 70% possession; that’s insane. Chelsea have no midfield. Alonso will be gone soon."
>
> _Could have been:_ `analysis` or `hot_take`
>
> _I chose, because:_ `analysis`. The 70% possession stat is a checkable fact, and it's the evidence for "Chelsea have no midfield." Under my decision rule, one checkable fact used to support the claim makes it `analysis`, even though the last sentence is a pure opinion.

**2.**

> _The post:_ "Until this is consistently called it'll always be a controversial decision, many players do this or even worse each game and nothing happens."
>
> _Could have been:_ `analysis` or `hot_take`
>
> _I chose, because:_ `analysis`. It argues a cause (inconsistent refereeing) rather than just reacting, and it points at something people can go and check in other matches. It's a weak case, though: it doesn't name a specific match or stat, so under a strict reading of my rule it's a `hot_take`. This is the kind of post where my labels are least consistent.

**3.**

> _The post:_ "Relegate city to sunday league"
>
> _Could have been:_ `reaction` or `hot_take`
>
> _I chose, because:_ `reaction`. It was posted in response to the City verdict news, and it reads as an instant emotional outburst, not a claim the poster is trying to defend. If it had said "City should be relegated because…", it would be a `hot_take`.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`

**Settings:** <!-- epochs, learning rate, batch size, seed -->
3 epochs, learning rate 2e-5, batch size 16, max length 128 tokens, seed 42.

**Anything I changed from the defaults, and why:** Nothing. I trained with the notebook's defaults.

**Device:** CPU (`cpu`), torch 2.14.1+cpu. Training took 36 steps and about 4 min 34 s.

**Split sizes:** <!-- train / val / test, and per-label counts in the test
split. If a label had fewer than about 8 in test, say so — it explains a lot
of next unit's variance. -->
train 180 · val 39 · test 39

| Label    | Train | Val | Test |
| -------- | ----- | --- | ---- |
| analysis | 33    | 7   | 7    |
| hot_take | 66    | 14  | 15   |
| reaction | 66    | 15  | 14   |
| bot      | 15    | 3   | 3    |

`analysis` (7) and `bot` (3) have fewer than 8 examples in the test split, so their F1 will move a lot between seeds. The data check also warned about 17 duplicate posts, mostly copies of the AutoMod bot message. Copies can land in both train and test, which will inflate the `bot` score.

---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- _What I asked for:_ I gave AI my four label definitions and asked for eight posts that sit on the boundaries between them.
- _What came back:_ Eight r/soccer-style posts (see the Stress test table under Label Taxonomy). Four were easy to label. Four weren't: two mentioned a fact in passing but were really just feelings, one leaned on a contested number ("115 rules"), and one was a mod-style notice.
- _What I changed:_ I added a second decision rule: a fact only makes a post `analysis` when the post argues with it, and a post that's mainly a feeling about what just happened is `reaction`.

**Moment 2**

- _What I asked for:_
- _What came back:_
- _What I changed:_

**Pre-labelling disclosure:** I labelled the first 29 comments myself. An AI assistant then labelled the rest (about 90% of the 258 rows), following the pattern in my first 29 and my label definitions. I double-checked every AI label. Rows are marked `cold` or `pre-labelled (AI), reviewed` in the note column.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure          | Baseline | Trained | Difference |
| ---------------- | -------- | ------- | ---------- |
| Overall accuracy |          |         |            |
| Macro F1         |          |         |            |
| F1 — `label_one` |          |         |            |
| F1 — `label_two` |          |         |            |

**What I predicted before I looked:**

<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**

<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->

---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
| --------- | ------ | ------- | ------ | --------- | ------- |
| 1.        |        |         |        |           |         |
| 2.        |        |         |        |           |         |
| 3.        |        |         |        |           |         |
| 4.        |        |         |        |           |         |
| 5.        |        |         |        |           |         |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |     |     |     |
| ---------------- | --- | --- | --- |
| \*\*\*\*         |     |     |     |
| \*\*\*\*         |     |     |     |
| \*\*\*\*         |     |     |     |

**My biggest off-diagonal number, and what it means:**

<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->

---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| #   | Criterion | Target | Verdict | How I decided |
| --- | --------- | ------ | ------- | ------------- |
| 1   |           |        |         |               |
| 2   |           |        |         |               |
| 3   |           |        |         |               |
| 4   |           |        |         |               |
| 5   |           |        |         |               |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** **_ / 30 = _**%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**

> _The post:_
>
> _Staff said / I said:_
>
> _My call, and why:_
>
> _Which pile:_

**2.**

> _The post:_
>
> _Staff said / I said:_
>
> _My call, and why:_
>
> _Which pile:_

**What the pattern in my disagreements tells me:**

---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
| --------- | ------ | ------- | ------ | --------- | ------- |
| 1.        |        |         |        |           |         |
| 2.        |        |         |        |           |         |
| 3.        |        |         |        |           |         |
| 4.        |        |         |        |           |         |
| 5.        |        |         |        |           |         |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->

---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->

**The gap between what I meant my labels to capture and what the model
learned:**

<!-- Two sentences. Your confusion matrix is the evidence. -->

<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [x] criteria.md has five numbered criteria, each naming a NUMBER
       [x] Each has a reason underneath tied to your data or taxonomy
       [x] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [x] No label above 70%
       [x] All five unit 5 sections have real content
       [x] Label Taxonomy includes the decision rule for your hardest boundary
       [x] The Dataset includes three hard cases
       [x] results.json and test_split.csv committed (the notebook does this)
       [x] At least four new commits
       [x] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
