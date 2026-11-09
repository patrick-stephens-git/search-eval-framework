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
| **Share of queries that fail** | Queries with `query_result` = `fail` ÷ all `sample` queries. `=COUNTIFS(C:C,"sample",AL:AL,"fail")/COUNTIFS(C:C,"sample")`. This is the easiest number to explain to stakeholders. |
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

Add these helper columns to the end of the sheet (for row 2, copy down). `top3_avg` and `query_result` are already in columns AK and AL:

| Column | Name | Formula |
|---|---|---|
| AP | `graded` | `=COUNT(Q2:Z2)` |
| AQ | `zeros` | `=COUNTIF(Q2:Z2,0)` |
| AR | `best_position` | `=IFERROR(MATCH(3,Q2:Z2,0),"none")` |
| AS | `failure_type` | `=IF(AP2=0,"no results",IF(AR2="none","retrieval",IF(AR2>3,"ranking","ok")))` |

Here `Q:Z` are `grade_1` to `grade_10`, and `Q:S` are the first three. The `product_id_1` to `product_id_10` columns (`AA:AJ`) sit between the grades and `top3_avg`. The formulas do not use them.

Then count and weight them (for `sample` rows):

- Queries per failure type: `=COUNTIFS(C:C,"sample",AS:AS,"retrieval")`
- **Searcher visits per failure type**: `=SUMIFS(E:E,C:C,"sample",AS:AS,"retrieval")`

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

- **Share of 0s overall:** `=SUMIFS(AQ:AQ,C:C,"sample")/SUMIFS(AP:AP,C:C,"sample")`
- **Share of 0s for one goal:** add `,I:I,"Exact match"` to both parts
- **0s in positions 1-3:** `=SUMPRODUCT((C2:C500="sample")*(Q2:S500=0))`
- **Graded results in positions 1-3:** `=SUMPRODUCT((C2:C500="sample")*ISNUMBER(Q2:S500))`
- **Queries with a given reason:** `=SUMPRODUCT((C2:C500="sample")*ISNUMBER(SEARCH("wrong brand",AM2:AM500)))`

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

Most patterns have fewer than 15 queries (only problems 5, 6 and 8 have more). Treat those as leads.

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

### Problems found

Each problem has the same parts: what the PM brings, the evidence in the eval, the problem statement, and the likely direction. The problem statement follows this pattern: {target user} {cannot do something} {when}, {so a business outcome suffers}, {because of the suspected cause}. The cause is a guess for the data science team to confirm.

#### 1. Brand spelling variants return nothing

**PM brings:** "Searches for products we sell show no results."  
**Evidence in the eval:** 9 Exact match queries return 0 results (3.1% of visits), and all 9 fail. 8 follow one pattern: `arcteryx` (4 queries) against the catalog's "Arc'teryx", `thermarest` and `therm a rest` (2), `northface` (1) and `hydroflask` (1). The 9th, `big agnes copper spur hv ul2`, looks like a different cause. All 9 ran on lexical search, and `expected_products` is filled in for each.  
**Problem statement:** Shoppers cannot find a specific product when they type the brand name without its punctuation or spacing, so these searches end with no click or purchase, because of a brand-name match that fails when the spelling differs from the catalog.  
**Likely direction:** Normalize brand names and add brand synonyms. Check the catalog for `big agnes` separately. Not a vector problem.

#### 2. Typos return nothing or the wrong things

**PM brings:** "A small typo breaks the whole search."  
**Evidence in the eval:** 4 queries, under 1% of visits. `rain jaket` and `sleeping bagg` return 0 results. `hikng boots` and `backpak 40l` return results with no 3 in the top 10. All 4 fail and all ran on lexical search.  
**Problem statement:** Shoppers cannot find the right product when their query has a spelling error, so these searches end with no click, because of a search that requires exact word matches.  
**Likely direction:** Spelling correction, fuzzy matching, or a vector fallback.

#### 3. Right item returned but buried

**PM brings:** "The good result is there. It is just not near the top."  
**Evidence in the eval:** 10 queries have their first 3 below position 3, with an average best position of 5.1. 8 of the 10 fail. They cover 11.3% of visits. `down jacket` alone is 6.1% of all visits, so without it the pattern covers about 5%. 7 of the 10 name an audience or attribute, such as `kids hiking boots`, `mens rain jacket` and `carbon trekking poles`. Setups are mixed: 6 lexical, 4 hybrid.  
**Problem statement:** Shoppers cannot find the right item near the top of the page when it is returned below position 3, so fewer of these searches end in a click, because of a ranker that does not weigh audience and attribute match.  
**Likely direction:** Add audience and attribute features, retune boosts, or add a reranker. Check `down jacket` on its own, because it carries much of the number.

#### 4. No good result anywhere in the top 10

**PM brings:** "Nothing on the page is what I wanted."  
**Evidence in the eval:** 8 queries have no 3 in the top 10, covering 3.8% of visits. 7 fail. All 8 ran on lexical search. 3 use descriptive wording (`winter hiking boots`, `ultralight backpacking tent`, `wide fit hiking boots`). 2 are the typos from problem 2. 3 are the exact models from problem 7. Do not add this count to problems 2 and 7.  
**Problem statement:** Shoppers cannot find a correct item when they describe it with words that differ from the catalog, so more of these searches end with no click, because of lexical retrieval that does not link the query words to the right products.  
**Likely direction:** Hybrid or vector retrieval, query expansion, or synonyms.

#### 5. Stated audience or attribute is missed

**PM brings:** "I asked for kids' or waterproof or under $100. It showed me none of that."  
**Evidence in the eval:** Among Product search queries, those with a stated audience or attribute fail 11 of 39 (28%, ±14 points). Those without fail 6 of 31 (19%, ±14 points). The ranges overlap, so the data does not yet show a difference. In the top 3 of failing queries, "missing key detail" is the most common reason (20 times), then "wrong product category" (17). The average top-3 grade is 2.26 with a stated need and 2.62 without.  
**Problem statement:** Shoppers cannot find items that meet a stated audience, attribute, or price limit, so they click or buy less, because of a search that treats the whole query as text and does not read the attributes.  
**Likely direction:** Extract audience, price, and attributes into filters or facets. A small LLM or NER parser is common. Get a larger sample before you build anything.

#### 6. Browse results lack variety

**PM brings:** "Gift and idea searches show the same item over and over."  
**Evidence in the eval:** 10 of 16 Browse queries (63%, 5.6% of visits) have a near-duplicate noted in `rater_notes`. Only 2 of 16 fail. The results are relevant but repetitive. The 10 ran on vector or hybrid.  
**Problem statement:** Shoppers cannot see a range of options when they search for ideas or gifts, so they browse less and click fewer results, because of a ranking that orders by closeness to the query and does not vary the results.  
**Likely direction:** Result diversification, such as MMR or grouping.
#### 7. Exact model missing, substitutes shown

**PM brings:** "I searched for a specific model and it showed me other ones without saying so."  
**Evidence in the eval:** 3 Exact match queries return results but not the exact model: `la sportiva bushido ii`, `scarpa zodiac plus gtx` and `kelty tempest 2`. 2 fail. `scarpa zodiac plus gtx` passes with a top-3 average of 2.0, because a different model grades 2. Under 1% of visits.  
**Problem statement:** Shoppers cannot tell whether a specific model is available when the search shows other models instead, so they leave or click the wrong item, because of a ranking that returns close substitutes and does not signal that the model is missing.  
**Likely direction:** Check the catalog. Add a "not available" message or flag the substitutes.

#### 8. Wrong-category noise below the top 3

**PM brings:** "The first results are fine, but the page fills up with unrelated items."  
**Evidence in the eval:** 44 of 112 queries (54% of visits) have two or more wrong-category or off-topic results at positions 4 to 10. The share of 0s is 2.0% in positions 1 to 3 and 13.7% in positions 4 to 10. 33 of the 44 still pass. Examples are `tent`, `camp stove` and `salomon`.  
**Problem statement:** Shoppers who search for a product type see items of other types below the first results, so they trust the result list less and click fewer results, because of retrieval that lets loosely related items in and has no category filter.  
**Likely direction:** Category filters or boosts, or a tighter retrieval cutoff. This problem touches the most visits, but each query is only mildly worse.

With real data you would work from hundreds of queries, so more of these groups would clear the 15-query bar.
