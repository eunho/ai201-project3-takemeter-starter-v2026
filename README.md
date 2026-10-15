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

This classifier sorts posts from `r/movies`, a high-volume public Reddit community dedicated to film discussions, reviews, and news. Film discourse on `r/movies` ranges from deeply researched structural breakdowns to unsubstantiated contrarian claims and immediate emotional reactions. TakeMeter classifies each post into three distinct categories (`analysis`, `hot_take`, `reaction`) to separate substantive critiques from noise and visceral first impressions.

### Candidate Distinctions Observed in the Text

1. **Concrete verification vs. bare assertion:** Substantive posts cite specific scene mechanics, named actors/directors, timestamps, or technical film elements (e.g. noting that Constance Miller is already in the shop in *McCabe & Mrs. Miller*, or examining cut sequences in *The Sixth Sense*). Bare takes declare a film "a complete waste of a concept" or "the last great thriller" without citing specific evidence.
2. **Immediate post-watch emotional state vs. retrospective thesis:** Reaction posts reflect immediate visceral feelings right as the credits roll (e.g., "just finished watching... what a mindf*ck", "genuinely touched my heart"), whereas analytical posts build a structured argument about themes, pacing, or cinematic choices.
3. **Assertive thesis vs. crowdsourced inquiry:** Some posts argue a definitive viewpoint ("Halloween III is a good movie and anthology was the right concept"), while others solicit external input or ask the community open questions ("What are your most hated plot contrivances?", "Where do you sit in the cinema?").

## Label Taxonomy

### `analysis`

**Definition:** Makes an argument about a film's quality, meaning, or production backed by a specific, checkable fact or scene detail.

**Example 1:**
> "Trivia of the Week: Constance Miller was already in the shop when McCabe went to buy three girls for $200 in McCabe & Mrs. Miller (1971). Do you ever wonder how Constance Miller suddenly shows up and starts talking McCabe into letting her manage the whorehouse? Well, it's kind of a cool, blink-and-you'll-miss-it detail, but she's already inside the shop in Bearpaw (the next town) when McCabe goes to buy three girls for $200 from the pimp."

**Example 2:**
> "After surely 8 billion rewatches of The Sixth Sense, I only now just noticed something [SPOILERS]. Obviously if you haven’t watched it yet, why are you still reading. I feel so dumb. I’ve seen this film so many times and only just now noticed right after Crowe gets shot and it cuts to the next fall, Malcolm is sitting in the chair and wearing the exact same clothes he dies in, because ghosts don't change clothes."

### `hot_take`

**Definition:** A confident, contentious, or evaluative claim about a film, director, or cinematic trend with no verifiable evidence or breakdown offered.

**Example 1:**
> "Halloween III: It’s a good movie. I wish they stayed with the anthology concept instead of going back to Michael Myers. But I guess everyone wanted a franchise killer in the 80s.. The music John Carpenter did on the synth is great. Don’t forget to watch the big give-away at 9:00. 18 days till Halloween- Silver Shamrock!"

**Example 2:**
> "Gone Girl (2014) - the last great Hollywood thriller? I remember this film being a real event. Based on a bestseller, Fincher directing, Affleck’s renaissance and a starring role for the mysterious Rosamund Pike. And it didn’t disappoint. It felt like a truly high-end thriller. Clever, witty, dark, daring and with multiple twists and turns. It’s 2.5 hours and flies by."

### `reaction`

**Definition:** An immediate personal reflection, visceral emotional response, or open community query focused on feelings rather than constructing an argument.

**Example 1:**
> "Apocalypse Now: what a mindf*ck... I just finished watching AN: Redux and even though I knew it wasn't going to be a typical military movie I'm still wondering what the hell I just saw. I can't even imagine watching that in a dark theater. I wonder how many people just sat there in shocked silence during the credits."

**Example 2:**
> "What's your top movie for the spooky season? Just tryna nail down as many good Halloween/horror/horror adjacent movies to stock up on for the festivities and I know most people here have good taste probably. My favorite for this time of year is John Carpenters 'In the Mouth of Madness'. Cheers, and may your nightmares be spooky ghost scary."

### The hardest boundary

**Which two labels:** `analysis` vs. `hot_take`

**The decision rule I used every time:**
If the post cites a specific, checkable fact from the film or its production (such as a named scene event, direct dialogue quote, specific timestamp, or verified production detail) to support its thesis, it is classified as `analysis`—even if the overall tone is provocative or argumentative. If the post offers only subjective value judgments or broad adjectives ("masterpiece", "ruined", "overrated", "flies by") without grounding them in verifiable evidence, it is classified as `hot_take`.

---

## The Dataset

**Where the posts came from:**
Collected directly from `r/movies`, Reddit's primary public film discussion forum, sampling across hot, top (all time, year, month), and controversial threads. Non-discussion posts (ticket giveaways, academic surveys, news link aggregations) were filtered out to ensure only substantive user-authored text was retained.

**How I labelled them:**
Cold first, AI pre-labelled second. The first 20 posts were labeled completely cold by hand with no assistance or AI tools to calibrate against the taxonomy and establish an honest annotator baseline (marked `cold` in `note`). The remaining 187 posts were pre-classified using an automated heuristic script encoding the taxonomy definitions, followed by a manual verification pass where each example was inspected, corrected, and reconciled against the decision rule (marked `pre-labelled, checked` in `note`).

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `hot_take` | 70 | 33.8% |
| `reaction` | 70 | 33.8% |
| `analysis` | 67 | 32.4% |
| **Total** | 207 | 100% |

**Three hard cases**

**1.**
> *The post:*
> "What are your most hated movie plot contrivances? There are plenty, but the one that's top of mind for me today is Bad Boys (1995). Téa Leoni's character will only talk to Mike (Will Smith), but Mike isn't available, so Marcus (Martin Lawrence) impersonates him to earn her trust. And they keep this charade going for damn near the entire movie. I was just yelling at the screen."
>
> *Could have been:* `reaction` vs. `analysis`
>
> *I chose, because:*
> While the post opens with a community prompt and concludes with emotional frustration ("yelling at the screen"), the core argument is anchored in a concrete, verifiable plot contrivance from *Bad Boys* (Marcus impersonating Mike to Téa Leoni's character). Because it cites checkable narrative mechanics rather than relying solely on sentiment, the decision rule designates it as `analysis`.

**2.**
> *The post:*
> "What’s a remake you just can’t get into no matter how good it is? I’m not a purist, and there are some remakes I enjoy more or on par with original, such as Ringu/The Ring, Pocahontas/Avatar, Oceans 11, It, but I can’t get into The Departed because how good Infernal Affairs was. I mean the departed was nice, but I just can’t shake how exact the ripoff was. Are there any remakes that you just refuse to accept?"
>
> *Could have been:* `analysis` vs. `hot_take`
>
> *I chose, because:*
> The post references specific film pairs (*The Departed* vs. *Infernal Affairs*), giving a surface appearance of comparative analysis. However, the author provides zero structural, cinematographic, or scene-level analysis, offering only the bare assertion that *The Departed* is an "exact ripoff" they refuse to accept. Because no checkable evidence is provided, it is classified as `hot_take`.

**3.**
> *The post:*
> "Tommy Boy (1995) is a perfect Fall/Autumn movie. Ive always loved Tommy Boy. Grew up watching it constantly and still rewatch it once a year. One of my all time favorites. I never really noticed it or thought about it before, but im watching it tonight and it made me realize that i normally always rewatch it during the Fall season. Its really a perfect October movie."
>
> *Could have been:* `hot_take` vs. `reaction`
>
> *I chose, because:*
> Although the post opens with a bold assertion ("Tommy Boy is a perfect Fall/Autumn movie"), it offers no atmospheric, thematic, or critical argument for that claim. The entire substance is a personal reflection on childhood viewing rituals and nostalgia ("watching it tonight and it made me realize that i normally always rewatch it"). Since it documents subjective viewing habit and personal feeling rather than an argued thesis, it is classified as `reaction`.

---

## The Training Run

**Base model:**
`distilbert-base-uncased`

**Settings:**
- Epochs: 3
- Learning rate: 2e-5
- Batch size: 16
- Max token length: 128
- Random seed: 42
- Hardware device: Apple Silicon GPU (`mps`), PyTorch 2.14.1

**Anything I changed from the defaults, and why:**
Kept the starter defaults (`epochs=3`, `lr=2e-5`, `batch_size=16`, `max_length=128`, `seed=42`). A max sequence length of 128 tokens captures the core premise and supporting thesis of Reddit discussion posts without imposing excessive padding overhead. The training run completed on `mps` in 8.55 seconds with train loss declining from 1.099 to 1.063 across 3 epochs.

**Split sizes:**
- Total dataset: 207 examples (stratified 70% train / 15% val / 15% test)
- Train split: 144 examples (`analysis`: 47, `hot_take`: 48, `reaction`: 49)
- Validation split: 31 examples (`analysis`: 10, `hot_take`: 11, `reaction`: 10)
- Test split: 32 examples (`analysis`: 10, `hot_take`: 11, `reaction`: 11)

All three labels have at least 10 examples in the held-out test split (exceeding the recommended floor of 8), which ensures stable evaluation metrics across random seeds.

---

## How I Used AI

**Moment 1**

- *What I asked for:* I asked AI to generate 8 ambiguous, borderline movie discussion posts that sit on the boundary between `analysis` and `hot_take`, without pre-determining which label was correct.
- *What came back:* The AI generated tricky cases, including posts asserting that an adaptation failed while quoting director interviews, and posts declaring a film philosophical garbage with heated rhetoric.
- *What I changed:* I tightened my decision rule: if an author cites a checkable, verifiable fact (such as a specific scene, quote, or named production detail) to support their argument, the post is classified as `analysis` regardless of how provocative or emotional the tone is. Unsubstantiated value judgments remain `hot_take`.

**Moment 2**

- *What I asked for:* I consulted AI to sanity-check realistic metric targets for a 3-label text classifier trained on ~200 examples, specifically asking for trade-offs between overall accuracy and per-label F1 scores.
- *What came back:* An explanation that with ~10 test examples per class, a single misclassification swings a label's F1 by nearly 7–10 percentage points, making an aggressive target like 0.80 F1 across all labels prone to failure due to sampling noise.
- *What I changed:* I set defensible criteria in `criteria.md`: an overall accuracy of 0.65, a macro F1 of 0.62, and a conservative per-label floor of 0.55 to guard against minority class collapse while accounting for sample variance.

**Pre-labelling disclosure:**
I used an automated rule-based heuristic to pre-label the bulk dataset (187 out of 207 posts), following a 20-post cold labeling pass performed completely by hand with no assistance (`cold` note). Every single pre-labeled post was subsequently reviewed, verified, and manually corrected against the taxonomy and hardest boundary decision rule before saving `labels.csv` (`pre-labelled, checked` note).

---

## Stretch Features (Extra Credit)

> **Declaration of Planned Additions:** Before running our extended experiments, we declared and implemented all three optional stretch features for extra credit:
> 1. **A fourth label (`recommendation`):** `r/movies` features a high volume of community posts seeking film recommendations or curating watchlists by trope/mood, distinct from analyzing or reacting to an already-watched film.
> 2. **50 extra examples for whichever label is thinnest:** In our original 207-post dataset, `analysis` was thinnest (67 posts vs. 70 for the other two). We collected and labeled 50 additional substantive `analysis` posts from `r/movies`, expanding `analysis` to 117 posts and the overall corpus to 312 posts.
> 3. **A second training run with one hyperparameter changed:** We trained Run 1 with standard baseline learning rate (`2e-5`) and Run 2 with tuned learning rate (`5e-5`), logging the side-by-side metric comparison and documenting what moved.

### Stretch 1: Fourth Label (`recommendation`)

**Definition:** A request for film suggestions based on specific tropes, moods, or themes, or a curated list recommending titles without evaluating an individual film's production or cinematic merit.

**Example 1:**
> "Looking for movie recommendations with a 'one night gone wrong' kind of storyline.. I’m looking for some intense and gripping movies that take place mostly over a single night where the main character finds themselves in an escalating situation."

**Example 2:**
> "Suggest mindf*ck movies please . Movies like timelapse, the one i love, whiplash, vivarium, midsommar, mother!, shutter island etc. are my favorites. Any movies that have an unpredictable storyline that messes with your head?"

**Decision rule for the hardest new boundary (`recommendation` vs. `reaction`):**
If a post solicits movie recommendations from the community based on themes, tropes, or watchlists ("Looking for movies like...", "Recommend me..."), it is classified as `recommendation`. If a post expresses an immediate emotional or visceral reaction to a film the author just watched ("Just finished watching... what a mindf*ck", "I cried my eyes out"), it is classified as `reaction` even if the author mentions their future watchlist.

---

### Stretch 2: 50 Extra Examples for the Thinnest Label (`analysis`)

In the baseline 3-label corpus, `analysis` was the thinnest class with 67 examples (vs. 70 for `hot_take` and 70 for `reaction`). We added 50 freshly collected and verified posts from `r/movies` specifically analyzing scene mechanics, cinematography, director styles, and narrative structures, raising `analysis` from 67 to 117 examples.

#### Dataset Distribution (Before vs. After Stretch Additions)

| Label | Original Count | Extra Added | Final Count | Final Share |
|---|---|---|---|---|
| `analysis` *(thinnest)* | 67 | +50 | **117** | **37.5%** |
| `hot_take` | 70 | 0 | **70** | **22.4%** |
| `reaction` | 70 | 0 | **70** | **22.4%** |
| `recommendation` *(4th label)* | 0 | +55 | **55** | **17.6%** |
| **Total** | **207** | **+105** | **312** | **100.0%** |

*All labels remain comfortably below the 70% dominance ceiling (maximum class is 37.5%), preventing majority-class collapse.*

---

### Stretch 3: Second Training Run with One Hyperparameter Changed

We ran two complete training runs on the expanded 4-class, 312-post dataset (`seed=42`, `epochs=3`, `batch_size=16`, `max_length=128`, evaluated on the held-out `test_split.csv` of 47 posts). The single hyperparameter varied was **learning rate**:
- **Run 1 (Baseline LR):** `learning_rate = 2e-5`
- **Run 2 (Tuned LR):** `learning_rate = 5e-5`

#### Performance Comparison Table

| Metric | Run 1 (`lr=2e-5`) | Run 2 (`lr=5e-5`) | Difference |
|---|---|---|---|
| **Overall Accuracy** | 0.3830 | **0.5745** | **+0.1915 (+19.2%)** |
| **Macro F1** | 0.1385 | **0.4292** | **+0.2907 (+29.1%)** |
| F1 — `analysis` | 0.5538 | **0.6296** | **+0.0758 (+7.6%)** |
| F1 — `recommendation` | 0.0000 | **0.9333** | **+0.9333 (+93.3%)** |
| F1 — `reaction` | 0.0000 | **0.1538** | **+0.1538 (+15.4%)** |
| F1 — `hot_take` | 0.0000 | 0.0000 | +0.0000 |

#### Empirical Note on What Moved

1. **Escaping Majority-Class Underfitting:** In Run 1 (`lr=2e-5`), the learning rate was too conservative for a 300-example fine-tuning run over 3 epochs; the model underfit and defaulted to predicting only the plurality class (`analysis`), resulting in zero F1 on all other three classes.
2. **Breakthrough on High-Signal Lexical Patterns (`recommendation`):** In Run 2 (`lr=5e-5`), the larger gradient steps allowed DistilBERT to rapidly learn discriminative attention patterns. The new 4th label (`recommendation`) skyrocketed to **0.9333 F1** (1.0 precision, 0.875 recall) due to distinctive inquiry phrases ("Looking for movies like...", "Recommend me...").
3. **Strengthened Performance on Enriched Label (`analysis`):** The 50 extra examples provided rich training signal, enabling `analysis` F1 to rise from 0.5538 to **0.6296** with a very high recall of 0.944.
4. **Persistent Boundary Challenge (`hot_take`):** Even at `lr=5e-5`, `hot_take` remained at 0.0 F1 because short subjective film assessments share extensive lexical overlap with both `reaction` and `analysis`. Resolving this boundary requires additional epochs or explicit contrastive loss.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

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
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

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

**Agreement rate:** ___ / 30 = ___%

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
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

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

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

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
