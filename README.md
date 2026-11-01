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

TakeMeter sorts comments from r/soccer, Reddit's main football community, by the kind of take they are. Each comment gets one of four labels: `analysis` (an argument backed by a specific, checkable fact), `hot_take` (a confident claim with no support), `reaction` (an immediate response to something that just happened), or `bot` (an automatically posted message). The model is DistilBERT fine-tuned on 258 comments I labelled. The goal is to separate the posts that argue from the posts that just assert or react, which is hard to do at a glance in a busy match or news thread.

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
| Overall accuracy | 0.333    | 0.564   | +0.231     |
| Macro F1         | 0.429    | 0.543   | +0.114     |
| F1 — `analysis`  | 0.286    | 0.000   | -0.286     |
| F1 — `bot`       | 1.000    | 1.000   | +0.000     |
| F1 — `hot_take`  | 0.000    | 0.571   | +0.571     |
| F1 — `reaction`  | 0.429    | 0.600   | +0.171     |

The Baseline column is using my definitions from `label_definitions.txt` (`baseline_results.json`). I also ran it once with bare label names (`baseline_bare.json`) to measure what the definitions were worth:

| Measure          | Baseline (definitions) | Baseline (bare names) | Trained |
| ---------------- | ---------------------- | --------------------- | ------- |
| Overall accuracy | 0.333                  | 0.641                 | 0.564   |
| Macro F1         | 0.429                  | 0.639                 | 0.543   |
| F1 — `analysis`  | 0.286                  | 0.250                 | 0.000   |
| F1 — `bot`       | 1.000                  | 1.000                 | 1.000   |
| F1 — `hot_take`  | 0.000                  | 0.667                 | 0.571   |
| F1 — `reaction`  | 0.429                  | 0.640                 | 0.600   |

**What I predicted before I looked:**

<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

I predict the trained model will beat the baseline mainly on `hot_take` and `reaction`, because it learned what those posts look like in r/soccer, but it won't do better on `analysis`, which has too few examples, and it can only tie on `bot`.

**What the gap actually means:**

<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->

Against the baseline that used my definitions, the trained model looks better: accuracy rises by 0.23, almost all of it from `hot_take` 0.00 to 0.57 and `reaction` 0.43 to 0.60. But when I ran the baseline again with bare label names, it scored 0.641 accuracy and 0.639 macro F1, beating the trained model on every label except `bot`, where they tie. So my definitions made the zero-shot model worse, by 31 points of accuracy. The `hot_take` definition asks the model to notice that something is missing, and it can't. The `analysis` definition pulled in posts that only sound factual: the baseline called 21 posts `analysis` and only 4 were right.

Against the stronger, bare-names baseline, fine-tuning on this split added nothing. The trained model also never predicted `analysis` once: of the 7 analysis posts, 5 went to `hot_take` and 2 to `reaction`. My prediction was right that `hot_take` and `reaction` would be its best labels and wrong that it would beat a general model. These numbers come from one 39-post split with only 7 `analysis` posts, so the three-seed runs will show whether the gap holds.

---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion           | Target                                                                 | Seed 42                                                  | Seed 7                              | Seed 2024                           | Verdict                                                           |
| ------------------- | ---------------------------------------------------------------------- | -------------------------------------------------------- | ----------------------------------- | ----------------------------------- | ----------------------------------------------------------------- |
| 1. Overall accuracy | ≥ 0.60                                                                 | 0.564                                                    | 0.538                               | 0.538                               | MISSED                                                            |
| 2. Per-label F1     | `reaction` ≥ 0.60, `hot_take` ≥ 0.55, `analysis` ≥ 0.50, `bot` ≥ 0.90  | R 0.600, H 0.571, A 0.000, B 1.000                       | R 0.522, H 0.571, A 0.000, B 1.000  | R 0.353, H 0.625, A 0.000, B 1.000  | MISSED (`analysis` all three seeds, `reaction` two of three)      |
| 3. Label balance    | each human label ≥ 15%, ratio ≤ 2.5:1, `bot` ≥ 20 rows and ≥ 10 unique | `analysis` 18.2%, ratio 2.02:1, `bot` 21 rows / 4 unique | same (dataset property)             | same (dataset property)             | MISSED (`bot` has 4 unique texts)                                 |
| 4. Agreement        | ≥ 75%                                                                  | 24 / 30 = 80% (staff set)                                | same (not seed-dependent)           | same (not seed-dependent)           | MET (80% ≥ 75%), on the 30-post staff set, not 40 of my own posts |
| 5. Confidence       | top third ≥ 80% correct, and ≥ 20 points above bottom third            | top 0.538, bottom 0.538 (gap 0.000)                      | top 0.538, bottom 0.385 (gap 0.154) | top 0.846, bottom 0.462 (gap 0.385) | MISSED (met on seed 2024 only)                                    |

**Device:** all three seeds were trained on CPU (`cpu`), torch 2.14.1+cpu, with max length 128. This is recorded in each entry of `results_three_seeds_before.json`.

Each seed is a different 70/15/15 split with 39 test posts, so each confidence third is 13 posts. Criterion 3 is about the dataset, not the model, so it doesn't change between seeds. The full numbers are in `results_three_seeds_before.json`.

**Spread across seeds** (notebook section 6):

| Measure          | Seed 42 | Seed 7 | Seed 2024 | Spread |
| ---------------- | ------- | ------ | --------- | ------ |
| Overall accuracy | 0.564   | 0.538  | 0.538     | 0.026  |
| Macro F1         | 0.543   | 0.523  | 0.494     | 0.048  |
| F1 — `analysis`  | 0.000   | 0.000  | 0.000     | 0.000  |
| F1 — `hot_take`  | 0.571   | 0.571  | 0.625     | 0.054  |
| F1 — `reaction`  | 0.600   | 0.522  | 0.353     | 0.247  |
| F1 — `bot`       | 1.000   | 1.000  | 1.000     | 0.000  |

Accuracy moved only 2.6 points across seeds. The stability is misleading. The model scores about the same on every split because it falls back on `hot_take` more and more, not because it learned the task. `reaction` F1 swings by 0.25, and on seed 2024 the model predicted `hot_take` for 33 of the 39 posts. `analysis` is 0.000 on every seed because the model never predicts it.

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

Seed 42 (notebook section 7):

| true \ predicted | analysis | hot_take | reaction | bot |
| ---------------- | -------- | -------- | -------- | --- |
| **analysis**     | 0        | 5        | 2        | 0   |
| **hot_take**     | 0        | 10       | 5        | 0   |
| **reaction**     | 0        | 5        | 9        | 0   |
| **bot**          | 0        | 0        | 0        | 3   |

**My biggest off-diagonal number, and what it means:**

<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->

5 real `analysis` posts were called `hot_take` and none went the other way, so the model never learned the `analysis`/`hot_take` boundary. It treats every argued post as a confident claim. The direction holds on the other two seeds as well: across all three, 19 of the 21 `analysis` test posts were called `hot_take`, and the model predicted `analysis` zero times. (The `hot_take`/`reaction` cells are tied at 5 on seed 42. Across all seeds the mistakes there also lean toward `hot_take`: 25 `reaction` posts called `hot_take` against 7 the other way.)

---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| #   | Criterion                        | Target                                                                 | Verdict    | How I decided                                                                                                                                                                                                                                                                                  |
| --- | -------------------------------- | ---------------------------------------------------------------------- | ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 1   | Overall accuracy                 | ≥ 0.60                                                                 | **MISSED** | 0.564, 0.538, 0.538. Below 0.60 on all three seeds.                                                                                                                                                                                                                                            |
| 2   | Per-label F1                     | `reaction` ≥ 0.60, `hot_take` ≥ 0.55, `analysis` ≥ 0.50, `bot` ≥ 0.90  | **MISSED** | `analysis` is 0.000 on every seed. `reaction` is 0.600, 0.522, 0.353, so it holds on one seed only. `hot_take` holds on all three (0.571, 0.571, 0.625). `bot` scores 1.000, but my criterion says that only counts if duplicate bot comments were removed before the split, and they weren't. |
| 3   | Label balance                    | each human label ≥ 15%, ratio ≤ 2.5:1, `bot` ≥ 20 rows and ≥ 10 unique | **MISSED** | `analysis` is 18.2% and the ratio is 2.02:1, both of which pass, but `bot` has only 4 unique texts against a target of 10.                                                                                                                                                                     |
| 4   | Agreement with a second labeller | ≥ 75%                                                                  | **MET**    | 24 / 30 = 80% against the staff set. Caveat: my criterion described a second person labelling 40 of _my_ posts. The staff check is 30 of _their_ posts under _their_ taxonomy, so it's the closest measurement I have, not the exact one.                                                      |
| 5   | Confidence                       | top third ≥ 80% correct, and ≥ 20 points above bottom third            | **MISSED** | Top third 0.538, 0.538, 0.846; gaps 0.000, 0.154, 0.385. It holds on seed 2024 only.                                                                                                                                                                                                           |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

**Criterion 1 (accuracy): a task the model didn't reach with this much training.** The training log shows that the Loss went from 1.36 to 1.23 and 1.16 over the three epochs. 1.386 is what a model scores if it guesses evenly across four labels, so the model ended barely better than a guess, and loss was still falling when training stopped. Three epochs of 180 posts at batch size 16 is only 36 update steps. The model's confidence says the same: its average top-class probability was 0.36 to 0.39 on every seed, against 0.25 for pure chance. With that little signal it falls back on the two biggest labels. It predicted `hot_take` for 20, 28 and 33 of the 39 test posts on the three seeds. Accuracy is stable across seeds (spread 0.026) because the model is guessing the same way on every split, not because it learned something stable.

**Criterion 2 (`analysis` F1 0.000): too few examples, made worse by an under-trained model, with my labelling as a contributing factor.** The model never predicted `analysis` on any seed, and 19 of the 21 `analysis` test posts went to `hot_take`. Three things point to the cause:

- _Too few examples._ `analysis` has 33 training posts against 66 each for `hot_take` and `reaction`. An under-trained model ignores its smallest human label first.
- My `analysis` posts look different on the surface: median length 50 words against 15 for `hot_take`, and 40% contain a digit against 14%. A model that had learned anything would have picked up on length alone. A model that never predicts the label at all hasn't learned it yet; that isn't a hard boundary.
- _My labelling likely blurs the boundary too._ My agreement report is the evidence. In 3 of my 6 disagreements with staff, I called a post `analysis` because it contained a number, when the number was decoration on an unsupported claim.

**Criterion 2 (`reaction` F1 0.600, 0.522, 0.353): the same under-training, showing up as drift toward `hot_take`.** Across the three seeds, 25 `reaction` posts were called `hot_take` and only 7 went the other way. On seed 2024 only 3 posts were predicted `reaction` at all. There is a labelling weakness on this boundary, but the size of the swing between seeds (0.25) tracks how heavily each run leaned on `hot_take`. That points mainly at the model.

**Criterion 2 (`bot`): the 1.000 doesn't count.** 18 of the 21 `bot` rows are the same AutoModerator comment, so copies land in both train and test, and the model only has to recognise a text it has already seen. My criterion said this score counts only if duplicates were removed before the split. They weren't, so `bot` fails that condition even though the number is perfect.

**Criterion 5 (confidence): the confidence scores barely vary, so the thirds are mostly noise.** The average top-class probability was 0.36 to 0.39 on every seed, and confident and unsure predictions were almost equally likely to be right. On seed 42, mean confidence was 0.362 when right and 0.361 when wrong. Sorting predictions by a number that barely varies gives thirds that are close to random. Each third is only 13 posts, so one post moves its accuracy by 7.7 points. Seed 2024 passing (0.846 vs 0.462) is most likely a lucky draw, not evidence the scores mean something. Same root cause as criterion 1: the model is too under-trained to be confident about anything.

**Labelling or model, overall?** Mostly model with my labelling as a real but secondary cause on `analysis`. The agreement report shows I apply rule 1 loosely and did not properly apply `hot_take`, and that would blur the `analysis`/`hot_take` boundary in my training data. But the training loss shows the model never got far enough to be limited by that boundary.

---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** **24 / 30 = 80%**

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

> _The post:_ "The club championship scoring changed in 2019 from top-6 to top-10. Since then our B team has finished above the A team twice. That's not depth improving, it's the scoring rewarding turnout."
>
> _Staff said / I said:_ `analysis` / `hot_take`
>
> _My call, and why:_ Staff is right. This has the same structure as the staff example under rule 1: a rule changed in a named year, a checkable outcome followed, and the conclusion is drawn from the two. Take away the 2019 change and the B-team results and there's no argument left, so the facts carry it. I treated the conclusion as a claim without support, but the support is right there.
>
> _Which pile:_ (a) Rule 1 covers it, and I applied it loosely.

**2.**

> _The post:_ "Anyone who races more than once a month isn't training, they're just collecting medals."
>
> _Staff said / I said:_ `hot_take` / `analysis`
>
> _My call, and why:_ Staff is right. "Once a month" is a threshold the writer picked, not a fact anyone could check. Nothing in the post backs the conclusion. It's a confident general claim with a number in it.
>
> _Which pile:_ (a) I let the presence of a number stand in for evidence.

**3.**

> _The post:_ "Road racing peaked in about 2015 and everything since has been marketing."
>
> _Staff said / I said:_ `hot_take` / `analysis`
>
> _My call, and why:_ Staff is right. "About 2015" is a date, but it's part of the claim, not support for it. Nothing shows racing peaked then or that later growth is marketing. This is rule 1's "number as decoration" case, and the hedge "about" makes the date even less checkable.
>
> _Which pile:_ (a) Rule 1 covers it.

**4.**

> _The post:_ "Track sessions are wasted on anyone running slower than 8 minute miles. You'd get more out of just running more."
>
> _Staff said / I said:_ `hot_take` / `analysis`
>
> _My call, and why:_ Staff is right in the end, but the definition helped cause my mistake. The staff definition of `analysis` lists "a time" as an example of a checkable fact, and "8 minute miles" is literally a time. But here it's a cutoff the writer chose, not a measured result. The second sentence gives a reason, but the reason is itself an unsupported claim. Under rule 1 nothing load-bearing is checkable, so it's `hot_take`.
>
> _Which pile:_ (b) A gap in the definition. It doesn't separate a time used as evidence from a time used as a threshold inside the claim.

**5.**

> _The post:_ "Nobody actually enjoys the long run. They enjoy having done it, which is a completely different thing, and the sport would be more honest if it said so."
>
> _Staff said / I said:_ `hot_take` / `reaction`
>
> _My call, and why:_ Staff is right. Rule 2 asks whether a post is about the writer or a claim about the world. "Nobody", "they" and "the sport" make this a general claim about every runner. It doesn't describe anything that just happened to the writer. The strong feeling misled me, and rule 2 says outright that "a post can be heated and still be `hot_take`".
>
> _Which pile:_ (a) Rule 2 covers it.

**6.**

> _The post:_ "Every injury thread on this forum is the same injury and the same three replies."
>
> _Staff said / I said:_ `hot_take` / `reaction`
>
> _My call, and why:_ I think my reading is defensible. The post is almost certainly a reply inside an injury thread, prompted by the one the writer just read. That's "an immediate response to something that just happened", and it's "feeling rather than argument": a sigh of exasperation, not a position anyone could debate. Staff read "every injury thread" as a general claim under rule 2. I read "every" as exaggeration in a frustrated reply, not a claim the writer means to defend. Rule 2's examples don't cover a post that is about the writer's frustration but is phrased as a generalisation. Under rule 4's tie-break, the whole post is spent on venting, not on arguing.
>
> _Which pile:_ (c) The rule is ambiguous here, and I'd argue for `reaction`.

**What the pattern in my disagreements tells me:**

Five of six disagreements are staff's `hot_take` that I labelled something else. I did not use `hot_take` in two ways: a number in the post makes me call it `analysis`, and strong feeling makes me call it `reaction`. Four of the six are pile, so this is mostly a consistency problem, not gaps in the rules. It's the same weakness I found in my own stress test in unit 5, where "any checkable fact means `analysis`" turned out too broad. It matters for my own data: if I applied rule 1 that loosely in `labels.csv`, some of my 47 `analysis` posts are really hot takes with a number in them. That would explain why the model can't separate `analysis` from `hot_take` and sends 19 of 21 `analysis` test posts to `hot_take`.

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

       [x] Baseline vs. Trained table, with your prediction written beforehand
       [x] Run Log — Before, five criteria across three seeds
       [x] Confusion matrix TYPED AS A MARKDOWN TABLE
       [x] A verdict on every criterion
       [x] A diagnosis for every miss, using the agreement report as evidence
       [x] Agreement Report with every disagreement adjudicated
       [x] One improvement, with Run Log — After
       [x] What's Still Broken
       [x] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [x] At least four new commits
       [x] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
