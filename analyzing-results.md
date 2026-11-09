# Using the Results to Surface Problems

Use this after you have graded a sample (Steps 1-9 of `pm-guide.md`). It shows how to go from a spreadsheet of grades to a short list of problems your data science team can act on.

It uses `sample-eval.csv`, which has 112 `sample` queries of made-up data. The column letters in the formulas match that file's column order, so adjust them if yours differs. The worked example at the end of this guide uses the same file.

**The goal is not a score.** The goal is to answer four questions:
1. Is there a problem, and how big is it?
2. **What kind** of problem is it: finding the right item, ordering it, or the catalog?
3. **Where** is it worst: which queries, goals, positions, or setups?
4. **Which problems matter most** to searchers?

---

## 1. Check the data first

Before you analyze anything:

- [ ] **One final row per query.** If several raters graded a query, use the settled `final` row (after disputes), or the single calibrated rater's row. Don't average raw rows from different raters.
- [ ] **No empty grade cells.** Every grade is a number 0-3, `N/A`, or `unsure`.
- [ ] **Right sets.** Work with `query_set` = `sample` for the headline. Use `trend` separately to track change. Exclude `golden`.
- [ ] **One rubric version.** All rows share the same `rubric_version`. Don't mix versions.
- [ ] **Raters were calibrated.** If a rater was below the kappa target, don't use their rows.
- [ ] **Spot-check a few rows.** Read 5 queries and their grades. Do they look right?

If any of these fail, fix them first. Everything below depends on them.

---

## 2. The headline

Work out these numbers for the `sample` rows:

| Number | How |
|---|---|
| **Share graded Not relevant (0)** | All 0 grades ÷ all graded results (ignore `N/A` and `unsure`) |
| **Share of queries that fail** | Queries with `query_result` = `fail` ÷ all `sample` queries. `=COUNTIFS(C:C,"sample",AB:AB,"fail")/COUNTIFS(C:C,"sample")`. This is the easiest number to explain to stakeholders. |
| **Margin of error** | 2 × √(share × (1 − share) ÷ number of queries). Use **queries**, not results. |
| **Compared with the threshold** | Yes if the whole range is above it. No if it is at or below. Too close to call if the range crosses it. |

A sample drawn by traffic (the default) already reflects what searchers see, so a plain average is fine. If you used an equal-by-tier sample, report each tier separately instead.

`query_result` is calculated by the pass or fail rule in Section 11 of `rubric-v1.md`. The margin-of-error formula works for the share of failing queries too.

**With around 100 queries the margin of error is about ±6 points at a 10% rate.** Smaller slices are wider. Use them to find leads, not to prove sizes.

---

## 3. Work out what kind of problem each query has

This is the most useful step. `query_result` tells you **which** queries fail. The pattern of grades tells you **why**, and so where the fault is.

| Pattern in the 10 grades | Failure type | What it means | Who looks at it |
|---|---|---|---|
| All `N/A` (nothing returned) | **No results** | The search found nothing | Spelling, synonyms, catalog gaps |
| **No 3 anywhere** in the top 10 | **Retrieval** | The right item isn't being returned | Recall, query understanding, lexical vs vector |
| A 3 exists, but **only below position 3** | **Ranking** | The right item is returned but buried | The ranking model, boosts, features |
| A 3 in the top 3 | OK on the best result | Check the rest for noise | Look at the lower grades |
| `expected_products` is filled in | **Catalog or index gap** | The item may not exist or be indexed | Catalog and data teams |

Add these helper columns to the end of the sheet (for row 2, copy down). `top3_avg` and `query_result` are already in columns AA and AB:

| Column | Name | Formula |
|---|---|---|
| AF | `graded` | `=COUNT(Q2:Z2)` |
| AG | `zeros` | `=COUNTIF(Q2:Z2,0)` |
| AH | `best_position` | `=IFERROR(MATCH(3,Q2:Z2,0),"none")` |
| AI | `failure_type` | `=IF(AF2=0,"no results",IF(AH2="none","retrieval",IF(AH2>3,"ranking","ok")))` |

Here `Q:Z` are `grade_1` to `grade_10`, and `Q:S` are the first three.

Then count and weight them (for `sample` rows):

- Queries per failure type: `=COUNTIFS(C:C,"sample",AI:AI,"retrieval")`
- **Searcher visits per failure type**: `=SUMIFS(E:E,C:C,"sample",AI:AI,"retrieval")`

The second number matters more. A pattern on 5 queries that covers 20% of visits beats one on 15 rare queries.

---

## 4. Find the patterns

Cut the data a few ways. For each cut, look for a group that is clearly worse than the rest.

| Cut | Column | What to look for | What it points to |
|---|---|---|---|
| **By reason** | `grade_reasons` | The most common reason | **Wrong product category**: category matching. **Wrong brand**: brand and model matching. **Wrong activity / audience**: attribute and facet handling. **Missing key detail**: attributes, size, price, compatibility. **Off-topic**: noisy retrieval. **Out of date**: stale index. |
| **By goal** | `goal` | A goal with a much higher failure rate | Exact match failing: model and brand matching. Product search failing: category understanding and ranking. Browse failing: variety and semantic matching. |
| **By position** | `grade_1`-`grade_10` | 0s at positions 1-3 vs 4-10 | Bad at the top: ranking. Fine at the top but noisy below: retrieval cutoff or filters. |
| **By frequency tier** | `tier` | Failures in the head | Biggest impact per fix |
| **By search setup** | `rules_applied` | One setup worse than another | Only if you graded more than one setup (for example lexical vs hybrid) |
| **By need field** | the `_need` columns with `grade_reasons` | Which field is missed most: brand, category, activity, audience, attribute | Which search capability to improve |
| **By result count** | `total_results` | Low counts with weak grades | Coverage or over-filtering |
| **By how the query is written** | `query` (read them) | Brand names with no space or punctuation ("northface"), misspellings, model numbers | Brand-name normalization, spelling correction |
| **Below the top 3** | `grade_reasons` for positions 4-10 | Many 0s and wrong-category results at positions 4-10 on queries that still pass | Noise the pass or fail rule does not show. Report it as its own number. |

Useful spreadsheet formulas (for `sample` rows, rows 2-500):

- **Share of 0s overall:** `=SUMIFS(AG:AG,C:C,"sample")/SUMIFS(AF:AF,C:C,"sample")`
- **Share of 0s for one goal:** add `,I:I,"Exact match"` to both parts
- **0s in positions 1-3:** `=SUMPRODUCT((C2:C500="sample")*(Q2:S500=0))`
- **Graded results in positions 1-3:** `=SUMPRODUCT((C2:C500="sample")*ISNUMBER(Q2:S500))`
- **Queries with a given reason:** `=SUMPRODUCT((C2:C500="sample")*ISNUMBER(SEARCH("wrong brand",AC2:AC500)))`

**Be careful with small groups.** Treat a group of under about 15 queries as a lead to confirm with a larger sample, not a result.

---

## 5. Read the worst queries

Numbers tell you where. Reading tells you why.

1. Filter `query_result` to `fail` and sort by `search_visits`, highest first.
2. Read the **10 worst**: the query, the information need, and the grades with `grade_reasons` and `rater_notes`.
3. Write down any themes you see. Common ones:
   - synonyms or word forms the search doesn't link ("coat" vs "jacket")
   - brand or model names not matched
   - attributes ignored (price, size, waterproof)
   - audience mixed up (men's results for women's queries)
   - a filter that removes good items
   - spelling variants
4. Note which themes appear in more than one query. Those are your patterns.

---

## 6. Rank the problems

For each pattern, fill in a row (the numbers below are made up, to show the format):

| Pattern | Queries | Share of sample visits | Severity | Failure type | Priority |
|---|---|---|---|---|---|
| Exact-match queries return no results | 1 | 5% | High (nothing shown) | No results | |
| Wrong category items at positions 4-10 | 6 | 22% | Medium | Retrieval | |

- **Reach:** the share of visits the pattern touches.
- **Severity:** nothing shown or a 0 near the top is worse than a weak result low down.
- **Confidence:** how many queries support it. Few queries means a hypothesis, not a finding.

Check these before you rank:
- **Patterns overlap.** One query can fit more than one row. A misspelled brand can be a "no results" query and a spelling query. Do not add the shares together.
- **One query can carry a pattern.** If one head query holds half of a pattern's visits, say so. Check that query on its own.
- **Do not compare setups across different queries.** If one setup (for example vector) ran only on Browse queries, its fail rate says nothing about the others. Compare setups on the same queries.
- **Compare a group with its opposite.** "28% of queries with a stated attribute fail" means little until you know the rate for queries without one, and whether the two ranges overlap.
- **Some patterns will not hold up.** Report them as "looked at, not a priority". It saves the team from checking them again.

Pick the **top two or three**. A short list is more likely to get fixed.

---

## 7. Write it up

Keep it to one page:

1. **The finding.** Yes, No, or Too close to call. Give the share, the margin of error, the threshold, the sample date, and the rubric version.
2. **The top 2-3 patterns.** For each:
   - what is happening, in one sentence
   - how many queries and what share of visits
   - 3-5 example queries with their grades
   - your best guess at the cause
   - the question you want answered
3. **What you could not tell.** Sample size, small groups, or anything unclear.

---

## 8. Hand it to the data science team

Bring:
- the one-page write-up,
- your graded CSV with the helper columns,
- the list of example queries for each pattern.

Ask them to:
- **Confirm or reject each hypothesis** with the logs and the search system.
- **Use the graded queries as a test set.** For a fix, re-score the affected queries before and after. They can calculate NDCG@10 per query from the grades. It measures ordering within the results returned, so it won't see items that were never returned. Use "no 3 in the top 10" and `expected_products` for those.
- **Guard against regressions** by re-running the trend set after each change.
- **Agree a target** before building, such as "reduce the share of 0s for exact-match queries from 20% to 10% without raising it elsewhere."

Then check the fix in an A/B test with real traffic before relying on it. The grades tell you whether a change did what you expected. They don't prove it is better for searchers.

After a fix, re-grade the affected queries and the trend set, and start the loop again. Scores can dip when you add new kinds of queries to the sample. That is not a regression.

---

## Cautions

Around 100 queries gives a rough view, and small groups are leads only. The other limits (a snapshot, only the top 10, grades are opinions, queries not sessions) are in "Known limits" in `pm-guide.md`. Say them whenever you share a finding.

---

## Worked example (made-up demo data)

Using the 112 `sample` queries in `sample-eval.csv` (Rater A). The numbers are from made-up data, so they only show the method. Real data will be messier.

### The headline

30 of 112 queries fail (27%, margin of error about ±8 points). They cover 18.5% of the sample's visits. 10% of graded results are a 0. Against a 10% threshold on the share of 0s, the result is **Too close to call**.

### Failure types (Section 3)

| Failure type | Queries | Share of visits | Of which fail |
|---|---|---|---|
| No results | 11 | 3.3% | 11 |
| Retrieval (no 3 in the top 10) | 8 | 3.8% | 7 |
| Ranking (first 3 below position 3) | 10 | 11.3% | 8 |

### The patterns

Only one pattern has more than 15 queries. Treat the rest as leads.

| # | Pattern | Queries | Share of visits | Confidence |
|---|---|---|---|---|
| 1 | Brand spelling variants return no results | 8 | 3.1% | Lead with one clear cause |
| 2 | Typos return nothing or the wrong things | 4 | under 1% | Lead, small |
| 3 | Right item returned but buried | 10 | 11.3% | Lead. One query is 6.1% of all visits. |
| 4 | No good result in the top 10 | 8 | 3.8% | Lead. Overlaps with 2 and 7. |
| 5 | Stated audience or attribute is missed | 39 | 19.8% | Weak. The ranges overlap (see below). |
| 6 | Browse results lack variety | 16 | 7.5% | Lead |
| 7 | Exact model missing, substitutes shown | 3 | under 1% | Lead, very small |
| 8 | Wrong-category noise below the top 3 | 44 | 53.9% | Strongest |

**1. Brand spelling variants.** Nine Exact match queries return 0 results (3.1% of visits). Eight follow one pattern: `arcteryx` (4 queries) against the catalog's "Arc'teryx", `thermarest` and `therm a rest` (2), `northface` (1) and `hydroflask` (1). The ninth, `big agnes copper spur hv ul2`, looks like a different cause. All nine ran on lexical search. Question for the team: do we normalize brand names? Is `big agnes` in the catalog?

**2. Typos.** `rain jaket` and `sleeping bagg` return 0 results. `hikng boots` and `backpak 40l` return results with no 3. All four ran on lexical search.

**3. Right item buried.** Ten queries have their first 3 below position 3, with an average best position of 5.1. Seven of the ten name an audience or attribute (`kids hiking boots`, `mens rain jacket`, `carbon trekking poles`). `down jacket` alone is 6.1% of all visits, so without it the pattern covers about 5% of visits. Check that query on its own.

**4. No good result in the top 10.** Eight queries, 3.8% of visits, all on lexical search. Three use descriptive wording (`winter hiking boots`, `ultralight backpacking tent`, `wide fit hiking boots`). Two are the typos from pattern 2. Three are the exact models from pattern 7. Do not add this to patterns 2 and 7.

**5. Stated audience or attribute.** Among Product search queries, those with a stated audience or attribute fail 11 of 39 (28%, ±14 points). Those without fail 6 of 31 (19%, ±14 points). The ranges overlap, so the data does not yet show a difference. Among failing queries, "missing key detail" is the most common reason in the top 3 (20 times), then "wrong product category" (17). Get a larger sample before you build anything.

**6. Browse variety.** Ten of 16 Browse queries have a near-duplicate noted in `rater_notes`, and only 2 of 16 fail. The results are relevant but repetitive, so the pass or fail rule does not see the problem.

**7. Exact model missing.** `la sportiva bushido ii`, `scarpa zodiac plus gtx` and `kelty tempest 2` return other models. Two fail. `scarpa zodiac plus gtx` passes with a top-3 average of 2.0, because a different model grades 2.

**8. Wrong-category noise.** Forty-four queries (54% of visits) have two or more wrong-category or off-topic results at positions 4 to 10. The share of 0s is 2.0% in positions 1 to 3 and 13.7% in positions 4 to 10. 33 of the 44 still pass. It affects the most visits, but each query is only mildly worse.

### What did not hold up

- **Few results and compatibility queries.** 13 queries return under 10 results (4.9% of visits), but only 2 fail. The top 3 are usually good, so this is not a priority here.
- **Head vs tail.** Head queries fail 1 of 13. Torso and tail queries fail 29 of 99. But the share of 0s is about the same in every tier (9 to 11%). Tier shows where the failures are, not why. Use it as a cut of patterns 1 to 8.
- **Lexical vs vector.** Lexical fails 23 of 74, hybrid 7 of 29, and vector 0 of 9. Vector ran only on Browse queries. This compares different kinds of query, so it does not show that vector search is better. A fair test runs the same queries on each setup.

### A gap in the pass or fail rule

Patterns 6, 7 and 8 mostly pass the rule in `rubric-v1.md` Section 11, because it checks only the top 3 and ignores variety. If you rely on the pass rate alone, you under-count these problems. Report the share of 0s at positions 4 to 10 as a second number.

### What to take forward

1. **Brand spelling variants (pattern 1).** One clear cause, a small share of visits, and probably cheap to fix.
2. **Right item buried (pattern 3).** The biggest reach among the failure types. Confirm `down jacket` separately.
3. **Noise below the top 3 (pattern 8).** The widest problem. Add a second metric to track it.

With real data you would work from hundreds of queries, so more of these groups would clear the 15-query bar.
