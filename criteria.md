# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. _"The model is accurate"_ is an
opinion. _"Every label has an F1 of at least 0.60 on the held-out set"_ is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — _"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"_. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area                  | A question it could answer                                            |
| --------------------- | --------------------------------------------------------------------- |
| Overall accuracy      | How often does it need to be right to be worth using?                 |
| Per-label performance | Is one label allowed to be much worse than the others?                |
| Balance               | How lopsided can your label counts get before it's a problem?         |
| Consistency           | If someone else labelled the same posts, how often should you agree?  |
| Confidence            | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

## 1. Overall accuracy is at least 0.60 on the held-out set.

<!-- Your criterion. It must name a number. -->

**Why this target:** Four labels means random is 0.25 and the majority-label baseline is 0.37, so 0.60 is more than 20 points above a model that learned nothing. I did not pick higher because reaction and hot_take are genuinely blurry (a short opinion can be either), and with only about 260 rows I would not honestly call 0.80 realistic.

---

## 2. Every label has an F1 of at least 0.50, with higher bars where the label is easier: reaction at least 0.60, hot_take at least 0.55, analysis at least 0.50, bot at least 0.90

<!-- Your criterion. -->

**Why this target:** Reaction and hot_take are my two biggest labels, so they have to carry the model. Analysis is my smallest human-written label (47 rows, about 7 in the test set), so one wrong prediction moves its F1 a lot and I set the bar lower. Bot is mostly templated text, so 0.90 is fair, but it only counts if the duplicate bot comments were removed before the split (only 4 of my 21 bot rows are unique texts).

---

## 3. Label balance: no human-written label is below 15% of the dataset, and the largest-to-smallest ratio among reaction, hot_take and analysis is at most 2.5 to 1. Bot must have at least 20 rows and at least 10 unique texts

<!-- Your criterion. -->

**Why this target:** Today analysis is 18% and the ratio is about 2.0 to 1, which passes. Bot is only 8% and has 4 unique texts, which fails, so I need more varied bot examples. I treat bot separately because it is rare in real threads, but 4 unique texts is too few to learn from or test on.

---

## 4. Consistency: a second person who labels 40 randomly chosen comments without seeing my labels agrees with me on at least 75% of them.

<!-- Your criterion. -->

**Why this target:** About 90% of my labels were assigned by an AI assistant following the pattern in my first 29, so I need evidence that a human reading the same label definitions reaches the same answer. I expect most disagreements between reaction and hot_take, and if agreement is below 75% then the label definitions need tightening before any model score means much.

---

## 5. Confidence: on the held-out test set, sort the predictions by the model's top-class probability and split them into thirds. The most confident third is correct at least 80% of the time, and at least 20 points more often than the least confident third.

<!-- Your criterion. -->

**Why this target:** A confidence score is only useful if it separates the sure cases from the guesses. I use thirds, as the notebook does, instead of fixed cutoffs like 0.80, because with about 50 test rows a fixed cutoff could leave a bucket with only a few predictions, while thirds always give groups of about 16. My overall accuracy target is 0.60, so if confidence means anything the top third should be well above that (80%) and the bottom third should be near or below it, which makes a 20 point gap reasonable. With only about 16 per group the result is noisy, so I will report the exact counts. If the model gives no confidence score to sort by (for example, it only outputs a label), this criterion is not met.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
