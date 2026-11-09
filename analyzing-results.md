# Using the Results to Surface Problems

Use this after you have graded a sample (Steps 1-9 of `pm-guide.md`). It shows how to go from a spreadsheet of grades to a short list of problems your data science team can act on.

It uses `sample-eval.csv`. The column letters in the formulas match that file's column order, so adjust them if yours differs.

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
- `sample-eval.csv` with the helper columns,
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

Using the 8 `sample` queries in `sample-eval.csv` (Rater A). These numbers are from made-up data, so they only show the method.

- **Headline:** 5 of 65 graded results were a 0, which is 7.7%. With 8 queries the margin of error is about ±18 points, so against a 10% threshold the result is **Too close to call**. It needs far more queries.
- **Queries that fail:** 2 of 8 (25%, margin of error about ±30 points, so this is only a hint). Together they cover 2,210 of the sample's 7,920 visits, about 28%.
- **Why they fail (`failure_type`):**
  - "arcteryx beta ar jacket" (Exact match, 410 visits) returns **no results**, and `expected_products` lists the jacket. That points to a coverage or catalog gap.
  - "kids hiking boots" (Product search, 1,800 visits) is a **ranking** failure. The first good result (a 3) is at position 5, and the top three are sandals, adult boots and rain boots (`top3_avg` 1.33). The right items are being returned but sit too low.
- **By position:** none of the 21 graded results in positions 1-3 is a 0, and 19 of 21 are a 2 or 3. All five 0s are at positions 4-10.
- **By reason:** "wrong product category" (13) and "missing key detail" (13) are the most common, then "off-topic" (5). Most weak results are the wrong kind of item or miss a stated attribute such as a price limit or model.
- **By goal:** Browse has 3 zeros in 20 results, Product search 2 in 37, and Exact match none in 8. These groups are too small to rely on.
- **Patterns to take forward:** (1) an Exact match query with no results, to check with the catalog team; (2) a ranking problem on a high-traffic query, where good results exist but sit below the top three, to ask the data science team about; (3) wrong-category and attribute misses further down the list, to ask whether the retrieval cutoff or filters let in loosely related items.

With real data you would work from hundreds of queries, so each group would be big enough to read.
