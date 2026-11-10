# A/B Test Plan: Homepage Headline

**Site:** Glow on a Budget, https://bellas076-design.github.io/glow-on-a-budget/
**Author:** Isabella Sanfilippo, MKTG:4400, Fall 2026
**Plan committed:** week of 9 November 2026, before any trial is run. The commit timestamp is the proof.

## 1. The four parts of the experiment

| Part | In this test |
|---|---|
| **Unit** | One AI visitor, meaning one fresh chat with memory switched off |
| **Treatment** | The homepage headline (H1) in version B |
| **Control** | The current homepage, version A, unchanged |
| **Outcome** | One yes/no answer to one fixed question, scored 1 or 0 |

## 2. Page and the single element being changed

**Page:** the home page, `index.html`.
**Element:** the H1 headline only. The intro paragraph, sections, prices, links, the fictional-site notice, and the layout are identical in both versions.

| Version | File | H1 headline |
|---|---|---|
| A, control | `index.html` | Look polished without blowing your student budget. |
| B, treatment | `index-b.html` | Student Beauty Deals Near the University of Iowa, Compared |

## 3. Why this element

The home page is where most visitors land, and the headline is the first thing they read. Version A is playful but never says what the site is, where it is, or who it is for. In my Deliverable 2 keyword plan, the home page targets "student discount salon Iowa City" and "cheap salon near University of Iowa." A visitor arriving from one of those searches needs to confirm within seconds that the page matches what they searched for. A headline that repeats their search terms should make that match obvious, so more visitors should decide the site is worth using.

Version B deliberately avoids the phrase "cheap beauty services Iowa City." That query already has its own page, `cheap-beauty-services-iowa-city.html`. Putting the same phrase in the home page headline would make two pages compete for one query. If B wins and is rolled out, it should not create that duplicate-targeting problem.

## 4. Hypothesis

**Prediction:** AI visitors shown version B will say "yes" to using the site more often than visitors shown version A.

- **Null hypothesis, H₀:** p_B = p_A. The headline has no effect, and any gap is chance.
- **Alternative, H₁:** p_B ≠ p_A. The headline changes the yes share, up or down. The test is two-sided.

This could turn out wrong. A playful headline may feel friendlier to students. And because every other part of the page is identical, the headline may make no measurable difference.

## 5. Visitors, prompt, and assignment

- **Model:** GPT-6, OpenAI reasoning model, in ChatGPT. The same model and default settings are used for every trial, and the model name is recorded on every row. A free plan may switch to a smaller model partway through, so I will check the model before each trial and log any change.
- **One fresh chat per trial, memory off.** No trial reuses a conversation, so each answer depends only on the prompt and the page it was shown.
- **Page given as pasted text,** copied from the live page, in the same way for both versions. The page address is left out, so the file name `index-b.html` cannot hint that it is a variant.
- **Random assignment, decided before anything runs.** A Python script (`random.seed(20261109)`, 50 A and 50 B, shuffled) produced the order in `ab-test-data.csv`. This is equivalent to the class RANDBETWEEN method, but it guarantees exactly 50 per version. Trial *n* uses the version in row *n*, trials run in row order, and the order is never changed. Because A and B are mixed across every day, model changes, app updates, and my own fatigue hit both versions alike.

**Exact prompt, identical in every trial except the pasted page:**

> You are a University of Iowa student on a tight budget. You want a gel manicure and a brow wax before a formal in two weeks, so you searched Google for "student discount salon Iowa City" and clicked on this page. Read it and decide whether you would use this website to choose where to book. Answer yes or no, then give one sentence of reasoning.
>
> [pasted text of version A or version B]

## 6. Primary outcome and scoring rule

- **Primary outcome:** the share of visitors who answer yes.
- **Scoring rule, fixed now:** only an answer that starts with "yes" scores 1. Everything else scores 0, including "maybe," "it depends," or no clear answer. The raw answer stays in the file, and **no trial is ever rerun or dropped.**
- This is the **only** metric the decision rests on. I will not slice the results by subgroup or add metrics afterwards. Anything I notice in the written reasons is reported as exploratory only.

## 7. Sample size, power, and dates

- **100 trials: 50 version A and 50 version B.** This is the fixed horizon. I will read the result **once, after all 100 trials,** and will not tally results along the way or stop early.
- **What this size can detect:** using the session 17 formula n ≈ 15.7 × p̄(1 − p̄) ÷ δ², with 50 per version and a yes share near 50%, the test has 80% power only for a gap of about **28 percentage points**. Smaller real effects will probably come out not significant. In that case I will report the result as underpowered, not as proof that the headline does nothing.
- **Launch:** version B published by **16 November 2026** (deadline 17 November).
- **Data collection:** 16 to 20 November 2026, in batches of about 20 trials, with each batch's date recorded.
- **Expected completion:** 20 November 2026.

## 8. Analysis and what counts as support

The data file has the columns trial (A), version (B), date (C), model (D), answer (E), outcome as 1 or 0 (F), and full output (G). The analysis is the session 18 two-proportion z test, run once:

```
H1  nA      =COUNTIF(B:B,"A")
H2  yesA    =COUNTIFS(B:B,"A",F:F,1)
H3  nB      =COUNTIF(B:B,"B")
H4  yesB    =COUNTIFS(B:B,"B",F:F,1)
H5  diff    =H4/H3-H2/H1
H6  se      =SQRT(H2/H1*(1-H2/H1)/H1+H4/H3*(1-H4/H3)/H3)
H7  z       =H5/H6
H8  p       =2*(1-NORM.S.DIST(ABS(H7),TRUE))
```

I will report:

- each version's yes share;
- the difference in percentage points and the relative lift (difference ÷ A's share);
- the standard error, z, and two-sided p;
- the 95% confidence interval, diff ± 1.96 × SE.

**Significance level:** α = 0.05, set now.

- **Supports the hypothesis:** B's yes share is higher than A's, p < 0.05, and the 95% interval excludes zero.
- **Contradicts it:** B is significantly lower.
- **Flat:** p ≥ 0.05. The interval width will show how large an effect the test could not rule out.

If either version scores 100% or 0% yes, the formula's standard error is unreliable. In that case I will also report Fisher's exact test and explain why.

## 9. Checks before trusting the result

1. Sample ratio matches the planned 50/50 split.
2. All 100 trials ran to the planned end, with no early stop.
3. The decision uses the one primary outcome only, with visitors counted as assigned.
4. The same model was used throughout. Any change is logged.
5. A result that looks too good to be true is checked against the raw outputs for copy-paste mistakes before it is believed.

## 10. Protecting rankings

- `index-b.html` contains `<meta name="robots" content="noindex">` and `<link rel="canonical" href="https://bellas076-design.github.io/glow-on-a-budget/index.html">`.
- `index-b.html` is **not** in `sitemap.xml` and is not linked from any page.

## 11. What this test can and cannot tell me

**It can tell me** whether the headline changes how a model, told to play a budget-conscious student, judges the page. The one-sentence reasons can suggest why.

**It cannot tell me** what real students would do. AI visitors answer far more alike than a crowd. They read the whole page carefully, with no real budget or time pressure, and the result may not survive a different model or a reworded prompt.

## 12. Keeping the test clean

The home page `index.html` (version A) will not be edited from launch until the last trial is run, expected 20 November 2026. Any other SEO change to the home page waits until then, so version A stays fixed for every trial. Changes to other pages can continue as normal.

## 13. Changes after launch

Any change after launch is logged here with its date and reason.

| Date | What changed | Why |
|---|---|---|
| | | |
