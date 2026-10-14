# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

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

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

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

## 1. Overall Accuracy on Held-Out Split

The fine-tuned model achieves an overall classification accuracy of at least **0.65** on the held-out test split.

**Why this target:**
With three labels, a random guesser achieves roughly 33%, and a naive majority baseline achieves around 35–40% on balanced data. An accuracy of 0.65 represents a substantial jump over chance, meaning the classifier correctly sorts at least two out of every three posts on held-out text before it can be trusted as a community filter.

---

## 2. Macro F1 and Per-Label Floor

The macro-averaged F1 score is at least **0.62**, and every individual label (`analysis`, `hot_take`, `reaction`) achieves an F1 of at least **0.55** on the held-out test split.

**Why this target:**
Macro F1 weights all three classes equally, preventing high accuracy on an easier class (e.g., `reaction`) from concealing poor performance on the harder `analysis` vs. `hot_take` boundary. The 0.55 per-label floor ensures the model genuinely learns to distinguish all three categories rather than collapsing one into zero recall.

---

## 3. Dataset Distribution Balance

No single label exceeds **45%** of the dataset, and the smallest label accounts for at least **25%** of all 200 labeled examples in `labels.csv` (between 50 and 90 examples per label).

**Why this target:**
The project brief permits up to a 70% class share, but in a three-label setup, any class exceeding 45% skews the model's loss landscape toward predicting that class whenever uncertain. Keeping all three classes between 25% and 45% ensures at least 50 total examples and at least 7–8 held-out test examples per class under a 70/15/15 stratified split, preventing wild F1 swings caused by single-sample test errors.

---

## 4. Annotator Agreement on Staff Calibration Set

My annotations achieve an agreement rate of at least **70%** (at least 21 out of 30 matched posts) against the staff reference labels in `data/staff_labels.csv` when evaluated using `agreement.py`.

**Why this target:**
A classifier cannot perform more consistently than the human annotator who taught it. Achieving 70% agreement on a 30-post calibration set with staff taxonomy proves that the written decision rules are unambiguous and reproducible by independent readers, while acknowledging that natural language discourse carries roughly 20–30% genuine edge-case ambiguity.

---

## 5. High-Confidence Calibration

For all test predictions where the model outputs a predicted class probability of at least **0.80**, the empirical accuracy on those predictions is at least **0.80**.

**Why this target:**
In a real-world community triage pipeline, high-confidence flags are often automated without human review. If the model expresses 80%+ certainty but is wrong more than 20% of the time, downstream systems cannot trust its confidence scores; requiring precision to match or exceed the confidence threshold validates that the model is well-calibrated and not prone to overconfident hallucinations on boundary cases.



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
