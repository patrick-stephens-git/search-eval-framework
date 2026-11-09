# Search Eval Framework: PM Step-by-Step Guide

Use this guide to find out whether your search has a problem, and to get your team to agree on it before anyone starts building. It works for any product with a search box: shopping, jobs, docs, music, listings, and so on.

**What you end up with:** a Yes or No finding ("our search has a problem" or "it doesn't"), backed by graded evidence, that both the Product Manager and the Data Scientist have agreed to.

**Short on time or just starting?** See [the lite path](#lite-path-a-fast-first-check) at the end. It gives you a first answer in a day or two.

## What's in this folder

| File | What it is |
|---|---|
| `pm-guide.md` | This guide: the steps, start to finish |
| `rubric-v1.md` | The rubric raters follow, plus the column guide for the CSV files (Section 11) |
| `sample-queries-sql.md` | Example SQL for pulling the sample |
| `sample-eval.csv` | Example grading file: one row per query, one grade column for each of the top 10 results |
| `sample-golden-set.csv` | Example answer key (the golden set) |
| `analyzing-results.md` | What to do with the grades once you have them |

All the data in the CSV files is made up.

**Who is involved**
- **Product Manager (you):** leads the audit, owns the rubric and the threshold.
- **Data Scientist:** pulls the queries, checks the numbers, signs off on the finding.
- **Experts:** 2 people who know your product and customers well. They set the "right answers" and settle disputes.
- **Raters:** people who grade results at scale. They can be the same people as the experts for a first pass.

---

## Before you start

Have these ready:
1. Access to real search data: what people searched, what results they saw, and how often each query was searched.
2. A list of what people come to your search to do (their goals). Example: "go to one specific item," "find something to buy," "browse for ideas or a product that solves a problem."
3. Two experts and at least a few raters, with a few hours each.
4. A spreadsheet to record grades.

---

## The 13 steps

### Step 1: Get sample queries
**Do:** Ask the Data Scientist for a sample of real queries and how many visits included each one. Example SQL is in `sample-queries-sql.md`.
- **Method:** use a **traffic-weighted random sample**. Common queries are more likely to be picked, but rare ones still appear, so the result reflects what searchers actually experience. A plain random sample is dominated by rare queries, and a "top N" list misses the tail.
- **Size:** start with **500 queries, top 10 results each**, and use the same numbers every audit so audits stay comparable. Rough guide: about 370 queries can detect a 10-point change, and about 4,800 a 2-point change. Fewer than 100-300 queries isn't enough to trust. More results per query does not shrink the margin of error. Only more queries do.
- **Workload:** 450 queries × 10 results is 4,500 grades per rater, or about 13,500 with three raters. If that is too much, use fewer queries or the top 5.
- **Draw about 25% extra.** You will remove some queries in Step 2.
- **Label each query head, torso, or tail** by how often it's searched (`tier`), so you can report the groups separately later.
- **Record how each query was run:** the total number of results returned (`total_results`) and the method used (`rules_applied`, such as lexical, vector, hybrid, or a boost). Hide `rules_applied` from raters.
- **Set aside a trend set:** 10-20% of the sample size (at least 50 queries). Keep it the same every audit so you can see change over time.
- Paste the query, `search_visits`, and `tier` into `sample-eval.csv`. Keep each query's top 10 results in a separate list for raters to look at, since the CSV holds grades only.

**You're done when:** you have the sample queries, the trend set, and the sample date saved.

### Step 2: Clean the sample
**Do:** Have the PM and an expert look through the sample by hand and remove queries that can't be judged well:
- Too generic or ambiguous to have a clear meaning (like "2" or "buy").
- Queries where a rule overrides the normal ranking.
- Queries that don't belong to your product (bot traffic, or searches for things you don't sell).
- **Questions no product could answer** (for example "how do I clean a camp stove"). Our search returns products only, so there is nothing to grade. A question a product could answer ("waterproofing for tents") stays in and is tagged Browse.

Write down what you removed and why, and **count the questions you removed** as a share of the sample. A high share points to content the search can't serve. That is a finding in itself, not a relevance grade.

Then draw replacements so you keep roughly your target size. 48 good queries out of a target of 50 is fine. 28 is not.

**You're done when:** every remaining query is something a person could define a clear need for.

### Step 3: Write the rubric
**Do:** Write a guide that tells anyone how to judge a result. Start from `rubric-v1.md`. It includes:
- a version number (start at v1), why the task matters, and a glossary
- what "relevant" means for each searcher goal
- the 0-3 rating scale, with a definition and an example for each level
- a decision tree for choosing a rating, and reasons for any rating below 3
- edge cases and rules for raters

Use your sample queries to make the examples real.

**You're done when:** someone outside the team could read it and grade a result without asking you questions.

### Step 4: Tag each query with a goal
**Do:** The PM or an expert goes in and writes a **goal** on every row of the sample, the trend set, and the golden set, in the `goal` column, before any rating starts. The goal is what the searcher is trying to do, such as going to one specific item, finding something to buy, or browsing for ideas. **Raters never write or change it**, so the same query can't be labeled two ways.
- Use the rubric's tagging questions: go down the list and stop at the first "yes."
- If a query fits two goals, use the one most searchers would mean. Click and purchase data can help you decide.
- Have a second person tag about 10% of the queries, mixed across goals. If you disagree often, fix the goal list or the questions in the rubric.
- If you tag with rules or an LLM, an expert checks a sample of the tags first.
- Mark each query `golden`, `trend`, or `sample` in the `query_set` column.

**You're done when:** every query has a goal, and the spot-check shows two people agree.

### Step 5: Write the information need for each query
**Do:** The two experts write a short **information need** for each query before any rating starts: what this searcher wants. Raters grade against it, so they are not guessing what "action" or "fancy shirt" means. **Raters never write or change it.** Each need has five fields, one column each:

| Column | What it says |
|---|---|
| `brand_need` | Any brand, or a specific brand |
| `product_category_need` | What kind of product they are looking for (and what doesn't count) |
| `activity_need` | Any, or a specific activity (for example hiking, basketball) |
| `gender_age_need` | Any, adults, kids, men, or women |
| `attribute_need` | Any, or a specific property: size, colour, price limit, material, capacity, model, compatibility |

Use `need_note` for anything the five fields miss, such as "results should differ from each other". If your product isn't a store, keep the idea and swap in fields that fit.
- Work together. Expect roughly an hour for 50 queries. For a big sample, write the needs in batches, starting with the golden set queries.
- Check your wording against click and purchase data for your highest-traffic queries when you can.
- If experts can't write a clear need for a query, it is ambiguous. Remove it (back to Step 2).

**You're done when:** every query you will grade has all five fields filled in.

### Step 6: Build the golden set
**Do:** Have your 2 experts grade the same small set of queries using the rubric, working separately.
- About **50 queries, 10 results each** (500 grades per expert). If that is too many, use the top 5 for the golden set only. Pick queries that cover every goal and every rating level, using the goal tags. Don't use trend queries.
- Each expert fills in their own row for every golden query (`rater` = Expert 1 or Expert 2).
- **Agreement target:** use **Cohen's kappa**, which corrects for agreement by chance. A starting target is **0.60 or higher**. Below that usually means the rubric is ambiguous. If your tool offers it, use *weighted* kappa, since a 3 vs 2 is a smaller miss than a 3 vs 0. To calculate it, put Expert 1's grades in one column and Expert 2's in another, then use any kappa calculator or spreadsheet template.
- **Settle every difference.** Where the experts differ, they discuss and agree one grade using the rubric. If they can't agree, the rubric is unclear: fix it (a new version, then redo) or drop that query. Nothing unsettled goes into the golden set.

**You're done when:** the experts meet the agreement target and every grade is settled. Save the settled grades in `sample-golden-set.csv`, one row per golden query. Keep the file clean: each expert's original grades and the explanation of any disagreement stay in their rows of the main file, since they show where the rubric is weak.

### Step 7: Select and calibrate raters
**Select:**
- **Match the people to the product.** Raters should know the area. A gear store needs people who understand outdoor gear. If you have to choose, use fewer queries and better raters.
- **Screen applicants.** Give a short test of results with known answers before you let anyone onto real work.
- **Keep them blind.** Raters should not know which version of search produced a result.
- **Plan for three raters per result** when you can, with at least one stronger "gold" rater, usually an expert, who settles disagreements (Step 9).

**Calibrate:** Have each rater grade the golden set, then compare their `grade_N` values with `sample-golden-set.csv`, position by position (skip `N/A`).
- Show raters where they differed so they learn the rubric.
- Only raters who hit the agreement target (kappa of 0.60 or higher, same as Step 6) move on.

**You're done when:** you know which raters you can trust. If most fail, the rubric is the problem. Go back to Step 3.

### Step 8: Set the problem threshold
**Do:** Decide, before you see the results, how bad is "bad enough to call it a problem."
- Example: "A problem exists if more than 10% of the top 10 results are graded Not relevant (0)."
- Save it with a version number so findings can be compared later.

Set this before grading so nobody is tempted to pick a number that fits the answer they want.

### Step 9: Grade the sample queries
**Do:** Calibrated raters grade the top 10 results of every remaining sample query (the sample minus the golden queries) and every trend query.

*How to grade*
- Enter a 0-3 grade in each `grade_N` column, in the order the results were shown. For any grade below 3, add a reason in `grade_reasons` (as "position: reason"). Add anything else worth noting in `rater_notes`.
- **Never leave a grade empty.** Write `N/A` for a position where the search returned no result (a query with 6 results gets `N/A` in `grade_7` to `grade_10`; a query with none gets `N/A` in all ten). Write `unsure` if a grade can't be given.
- If no result is good, or the query returns nothing, list what they expected to see in `expected_products`.
- If a rater thinks the goal or information need is wrong, they still grade with what's given and write "goal?" or "need?" in `rater_notes`. The PM reviews these flags.
- Each rater fills in their own row for each query (`rater` = their name).

*Quality control*
- **Check in after the first batch.** Review tricky cases and fix gaps in the rubric before the rest goes out.
- **Settle disputes.** Aim for 3 raters per result. If they agree, that's the grade. If they disagree, the gold rater grades it and that grade is final. Note why. Disputes that keep recurring mean the rubric or the information need needs a fix.
- **Keep checking raters.** Hide known-answer items from the golden set in each batch (about 1 in 10 is a reasonable start). A rater who falls below target on them is paused and retrained. A rater who keeps grading differently from everyone else is removed. Keep batches short so people don't tire.

**You're done when:** every result is graded and every dispute is settled. This is your **judgment list**.

### Step 10: Rank the poor queries
**Do:** First, check with the Data Scientist that the graded queries represent real searches (a fair mix of common and rare ones). Then calculate two columns, `top3_avg` and `query_result`. A query **passes** only if it returned at least one result, has no 0 in positions 1-3, and its top-3 average is 2 or higher. Otherwise it **fails**. The formulas are in Section 11 of `rubric-v1.md`. Set the rule before you look at the results, and keep it the same between audits.
- Filter `query_result` to `fail` and sort by `search_visits`, so the failures that hit the most people come first.
- Note the reasons and the expected products raters gave.

**You're done when:** you have a ranked list showing which failures hit the most people.

### Step 11: Confirm the problem
**Do:** Work with the `sample` rows only, and compare to your threshold from Step 8.
1. **Share graded Not relevant:** the number of 0s across the ten grade columns divided by the number of grades. For example `=COUNTIF(range,0)/COUNT(range)`. `COUNT` skips `N/A` and `unsure`.
2. **Margin of error:** 2 × √(share × (1 − share) ÷ queries). Use the number of **queries**, not results, since a query's results are related. For 14% on 450 queries, that's about ±3 points.
3. **Finding:** **Yes** if the whole range is above the threshold. **No** if the whole range is at or below it. **Too close to call** if the range crosses it: add more queries and re-grade. A No is a valid, useful answer.
4. **Share of queries that fail** (from Step 10). It is easy for stakeholders to read.

Then:
- **Break it out** by goal, by head, torso, and tail, by `rules_applied` (for example lexical vs hybrid), and by position (1-3 vs 4-10), since a miss at position 1 hurts more than one at position 9. This shows where search is failing, such as product searches doing fine while exact-match searches don't.
- Compare the trend set to last audit's result, if there is one.
- Report the share of queries removed as unanswerable questions (Step 2) separately from the relevance result.
- State the [known limits](#known-limits-of-this-method) in the finding.
- To turn the finding into specific problems for the data science team, see `analyzing-results.md`.

Write down: the share, the margin of error, the threshold, the rubric version, the sample date, and a breakdown by reason.

Example: *"Yes. 14% of the top 10 results are Not relevant, ±3 points (threshold 10%). 6 points of that are wrong product category. Exact-match searches are worst at 22%. The trend set was 12% last audit."*

### Step 12: Record alignment
**Do:** You and the Data Scientist each review the same finding and record **Agree** or **Disagree**.
- If either of you disagrees, write down why. Then change the rubric, threshold, or sample and re-run.
- The finding counts as **aligned** only when both of you agree.

**You're done when:** the finding is aligned.

### Step 13: Update the rubric when needed
**Do:** Change the rubric if you see a new searcher goal, many disputed results, or a new business goal.
- Save it as a new version and note what changed, why, and who approved it.
- Old grades are now out of date. Redo the golden set, recalibrate raters, and re-grade the trend set under the new version.
- If you added or changed a goal, re-tag the affected queries and go back to Step 4. Otherwise go back to Step 6.
- **Expect a dip.** When you add a new goal and grade it, the overall score often drops, because you are now measuring something you couldn't see before. That's not a regression. Keep scores by goal, so each goal's trend stays comparable, and tell stakeholders ahead of time.

---

## Known limits of this method

Say these out loud in every finding, so nobody reads more into it than it can show.

- **It only grades what search already shows.** We grade the top 10 results the engine returns. That tells you whether those results are good. It can't tell you whether better results exist elsewhere in your catalog. The `expected_products` field helps, but it isn't a full check.
- **Grades are opinions, with a margin of error.** Trained raters still disagree, and the sample is only a slice of all searches.
- **It's a snapshot.** Search traffic and your catalog change. This finding is true for this sample, on this date.
- **Searchers don't experience queries one at a time.** They search in sessions. This audit doesn't measure that.
- **Questions are not graded.** Our search returns products only, so questions no product could answer are removed and counted, not graded.
- **It tells you whether there is a problem, not whether a fix is good.** Use it to find and agree on problems.

## Refresh cadence

- **Main sample:** draw a new one every audit. Query mix shifts with seasons, launches, and growth. Re-audit at least every quarter, and after big changes to search or the catalog.
- **Trend set:** keep it the same. Replace a query only when searchers stop using it, and keep a record of replacements.
- **Golden set:** keep it fixed until the rubric changes version.
- **Raters:** recalibrate when the rubric changes, and when someone has been away for a while.

## Lite path: a fast first check

Use this when you're just starting, want a quick answer, or want to test the idea before building the full process. It follows the same steps with less volume. The finding is **provisional**.

1. **Pick one goal** you suspect is weak (for example, exact-match searches for a named product).
2. **Pull 20-30 queries** for it, traffic-weighted. Clean them (Step 2) and tag them with that goal.
3. **Write a one-page rubric** with the four rating levels and one example each, and the information need for each query (five fields).
4. **Have two experts grade the results** separately, top 10 each (or top 5 to save time). No separate rater pool.
5. **Check agreement** (kappa). If it's under 0.60, fix the rubric and redo. Settle any differences by discussion.
6. **Compare the share to your threshold.** Label the finding "provisional" and give the margin of error. With few queries, it will be wide.
7. **You and the Data Scientist record Agree or Disagree.**

**Skip for now:** the trend set, the rater pool, hidden known-answer items, head/torso/tail splits.
**Time:** roughly 1-2 days. If the result is Yes, or too close to call, move to the full process.

---

## Quick checklist

- [ ] Traffic-weighted sample, trend set, and sample date saved
- [ ] Sample cleaned; removed queries logged and counted; replacements drawn
- [ ] Rubric v1 written (glossary, levels, decision tree, reasons)
- [ ] Every query tagged with a goal; spot-check shows agreement
- [ ] Every query has an information need (all five fields)
- [ ] Experts reached the kappa target; golden set saved
- [ ] Raters selected, screened, and calibrated
- [ ] Problem threshold set before grading
- [ ] Sample and trend queries graded; first-batch check-in done
- [ ] Disputes settled by a gold rater; hidden checks run
- [ ] `top3_avg` and `query_result` calculated; failing queries ranked by people affected
- [ ] Finding written with margin of error, breakdowns, and known limits
- [ ] PM and Data Scientist both recorded Agree
- [ ] Rubric updated and versioned if anything changed

## Common mistakes

- **Grading before calibrating.** Uncalibrated grades are opinions, not evidence.
- **Setting the threshold after seeing results.** It makes the finding easy to dispute.
- **Reusing the golden set after the rubric changes.** Redo it under the new version.
- **Letting raters guess the query's meaning.** Write the information need first.
- **Calling Yes or No without a margin of error.** A small sample can give a number that isn't real.
- **Reporting raw agreement only.** Use kappa, since some agreement happens by chance.
- **Treating "No problem found" as a failure.** It tells you to spend your effort elsewhere.
- **Treating a drop after adding a goal as a regression.** You are measuring something new.
- **Changing the trend set between audits.** You lose the ability to see change over time.

## How long it takes (rough guide)

| Phase | Steps | Typical effort |
|---|---|---|
| Setup | 1-5 | 3-5 days |
| Trust check | 6-7 | 3-4 days |
| Grading | 8-9 | 3-8 days, depending on the number of raters and results per query |
| Finding | 10-12 | 1-2 days |

These are starting estimates. Adjust them after your first audit.
