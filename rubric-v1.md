# Search Relevance Rubric

**Version:** v1
**Date:** [YYYY-MM-DD]
**Owner:** [Product Manager]
**Approved by:** [Product Manager] · [Data Scientist]

> **How to use this template:** Replace everything in [square brackets] with your own product's details. Keep the structure. Delete this note when done. Then test the rubric with two experts (Step 6 of the PM guide) and revise until they agree.
>
> The examples use an outdoor gear store. The five information-need fields (brand, product category, activity, gender/age, attribute) fit a store that sells products. For another kind of product, keep the idea and swap in fields that fit, such as location, seniority, or file type.

---

## 1. Why this matters

Your ratings decide whether the team believes our search has a problem. If two raters judge the same result differently, nobody can trust the finding and the team spends its time arguing about the ratings instead of fixing search. Your job is to judge results the way this rubric says, not the way you personally would. When something is unclear, tell us. Do not guess.

## 2. Product context

- **What the product is:** [e.g. online store for outdoor gear / job board / help-center search]
- **What a "result" is:** [e.g. a product / a job posting / an article]
- **Who the searcher is:** [e.g. a shopper looking to buy / a job seeker / a customer with a problem]

## 3. Glossary

| Term | Meaning |
|---|---|
| Query | What the searcher typed |
| Result | One item shown for the query |
| Goal | What the searcher is trying to do (Section 4) |
| Information need | A short description of what the searcher wants for this query (Section 5) |
| Rating (or grade) | The 0-3 score you give a result (Section 6). The CSV columns call it a grade. |
| Reason | Why a result scored below 3 (Section 7) |
| Top results | The first [10] results shown, in the order searchers see them |
| Golden set | Queries our experts rated first. We use it to check that raters agree with them. |
| Disputed | A result where raters disagree |
| [Add terms that are specific to your product] | [..] |

## 4. Searcher goals

Every query has one **goal**: what the searcher is trying to do. The goal changes what a "3" looks like, but the rating scale in Section 6 stays the same. There are three goals:

| Goal | What the searcher wants | Signals in the query | What a "3" looks like |
|---|---|---|---|
| **Exact match** | One specific item they already know about | Brand + model name, model number, SKU | That exact item (other sizes or colours of it are fine). A different model from the same brand is at most a 2. |
| **Product search** | Something to buy that fits a type, a brand, or an attribute | A product type ("rain jacket"), a brand on its own ("osprey"), attributes or a price limit ("waterproof coat under $150"), a part or add-on for something they own ("fuel canister for jetboil") | A typical, good example of that type that meets every stated attribute. For a brand-only query, a typical item from that brand. For a part or add-on, one that fits the named item (a part for a different item is a 1 at most). |
| **Browse** | Ideas, options, or a product that solves a problem. They don't know yet exactly what they want. | An occasion, recipient, activity, or vague need with no product type ("gifts for hikers"), or a problem a product could solve ("waterproofing for tents") | A product that clearly fits the occasion or solves the problem. The top results should also differ from each other (see below). |

**Our search returns products only.** Questions that no product could answer ("how do I clean a camp stove") are not graded. They are removed in Step 2 of the guide and counted, because they show content the search can't serve. A question that a product could answer ("waterproofing for tents") is a Browse query.

**Browse queries:** don't mark a result down because it differs from the others. But if the first three results are near-duplicates, note it in `rater_notes`. It points to a search that shows too little variety.

### How to tell which goal a query has

**The PM or an expert writes the goal for every query (sample, trend, and golden), in the `goal` column, before rating starts (Step 4 of the guide). Raters never write or change it.** It is already filled in when a rater opens the file.

1. Use the questions below to tag. Stop at the first "yes."
   1. Does it name a specific product, brand + model, or model number? → **Exact match**
   2. Is it a question or request for advice that no product could answer? → **Not graded.** Remove it in Step 2 and count it.
   3. Is it an occasion, recipient, activity, vague need, or a problem a product could solve, with no product type? → **Browse**
   4. Otherwise (a product type, a brand on its own, attributes, or a part for something they own) → **Product search**
2. **Two people tag a small share of the queries** (about 10%, mixed across goals). If they disagree often, the goal list or the questions above need to change.
3. **If a rater thinks the tag is wrong,** they still rate using the given tag and write **"goal?"** in `rater_notes`. The PM reviews these flags.
4. **If a query fits two goals** (for example "osprey atmos" could be Exact match or Product search), tag it with the most likely goal, which is the one most searchers would mean. Query logs can help here: look at what people clicked or bought after searching it.

If you tag queries automatically (for example with an LLM or keyword rules), have an expert check a sample of the tags before you use them.

## 5. Information need

Every query also has an **information need**: a short description of what this searcher wants. Raters rate against it. **The two experts write it for every query before rating starts (Step 5 of the guide), in the `_need` columns. Raters never write or change it.** If it conflicts with what you would have assumed, follow the information need. Each need has five fields:

| Field | What it says | Values |
|---|---|---|
| **Brand** | Whether the searcher wants any brand or a specific one | Any · Specific: [brand(s)] |
| **Product category** | What kind of product they are looking for | [category], with notes on what does *not* count |
| **Activity** | Whether the product must fit a specific activity | Any · Specific: [activity, e.g. hiking, camping, trail running] |
| **Gender / age** | Who the product is for | Any · Adults · Kids · Men · Women |
| **Attribute** | A specific property the result must have: size, colour, price limit, material, capacity, model, compatibility | Any · Specific: [attribute(s), e.g. waterproof; price $150 or less; model Moab 3] |

Add one short free-text line (`need_note`) for anything the five fields don't capture, such as "results should differ from each other" or what does not count.

**Examples**

| Query | Goal | Brand | Product category | Activity | Gender / age | Attribute | Note |
|---|---|---|---|---|---|---|---|
| "osprey atmos ag 65" | Exact match | Specific: Osprey | Backpack | Any | Any | Specific: model Atmos AG 65 | Other Osprey models are substitutes. |
| "rain jacket" | Product search | Any | Rain jacket (waterproof shell) | Any | Any | Any | Not a poncho, fleece, or insulated jacket |
| "women's trail running shoes" | Product search | Any | Running shoes | Specific: trail running | Women | Any | |
| "kids hiking boots" | Product search | Any | Hiking boots | Specific: hiking | Kids | Any | |
| "waterproof coat under $150" | Product search | Any | Rain jacket | Any | Any | Specific: waterproof; price $150 or less | |
| "gifts for hikers" | Browse | Any | Any hiking gear | Specific: hiking | Any | Specific: small to mid-priced | Results should differ from each other |
| "tent poles for 2 person tent" | Product search | Any | Tent poles | Camping | Any | Specific: fits a 2-person tent | |
| "jacket" | Product search | Any | Jacket | Any | Any | Any | Very broad. Rate typical, popular jackets as 3. |

**"Any" means any.** If a field says Any, a result is not wrong for having a particular brand, activity, audience, or attribute.

A result that gets one of these fields wrong can't get a full 3. The decision tree in Section 6 says how far each miss lowers the rating, and the Reasons table (Section 7) names which field missed.

## 6. Rating levels

Pick the **one** level that fits best. Read from the top and stop at the first level that fits.

| Rating | Name | Definition | Example (query → result) |
|---|---|---|---|
| **3** | Full match | Matches everything in the information need: the right category, brand, activity, and audience, and every stated attribute. | [e.g. "red running shoes size 10" → red running shoes, size 10 available] |
| **2** | Close substitute | Right type of thing, but misses one detail. The searcher would likely still consider it. | [e.g. "red running shoes size 10" → orange running shoes, size 10] |
| **1** | Related item | Connected to the query but not what was asked for. Wrong category, or misses key details. | [e.g. "red running shoes size 10" → running socks] |
| **0** | Not relevant | Has no useful link to the query, or would confuse the searcher. | [e.g. "red running shoes size 10" → kitchen blender] |

**Tie-breaker:** if you are torn between two levels, choose the **lower** one.

### Decision tree for rating

Go down the list. Stop at the first "yes."

1. Does the result have no useful link to the information need? → **0**
2. Is it in the wrong product category, or does it miss two or more of brand, activity, audience, and attribute? → **1**
3. Does it miss exactly one stated field (brand, activity, audience, or an attribute such as size, colour, price, model)? → **2**
4. Does it match everything stated? → **3**
5. Can't tell? → mark **Unsure** (Section 8).

Then pick a reason (Section 7) if you rated below 3.

## 7. Reasons for low ratings

If you rate a result **2, 1, or 0**, pick a reason. Choose the main one.

| Reason | Use when |
|---|---|
| Wrong product category | The result is a different kind of product than the information need asks for |
| Wrong brand | The information need names a specific brand and the result is another brand |
| Wrong activity | The information need names a specific activity and the result is for another |
| Wrong audience | The information need names an audience (men, women, kids, adults) and the result is for another |
| Missing key detail | Right type, but misses a stated attribute (size, colour, price, model, compatibility...) |
| Too general | Right area, but far less specific than the query |
| Too specific | Right area, but narrower than the query needs |
| Off-topic | No meaningful link to the query |
| Out of date | The result is old, discontinued, or no longer valid |
| Poor quality | Right match, but the result itself is incomplete, broken, or low quality |
| Ambiguous query | The information need is missing or unclear and you can't pick one reading |
| [Add your own] | [..] |

## 8. Edge cases

| Situation | What to do |
|---|---|
| Result is unavailable (out of stock, expired, removed) | Rate the match. Do not penalize availability. Mark the reason **Out of date** if the result is gone for good. |
| Query is a typo or misspelling | Rate against what the searcher most likely meant. |
| Query is very broad (one word, like "jacket") | Rate **3** for a clear, typical example of the thing. Rate lower if it's an odd or niche version. |
| Query returns no results | Don't rate. Write **N/A** in every grade column, write "No results returned" in `rater_notes`, and list what you expected to see in `expected_products`. |
| A question no product could answer ("how do I clean a camp stove") | Don't rate it. Write **"question"** in `rater_notes` so the PM can remove and count it. |
| Result matches the words but not the meaning | Rate on meaning. A keyword match alone is not a match. |
| Sponsored or promoted result | Rate it like any other result. |
| No information need is given | Rate against the most common meaning, write **"need?"** in `rater_notes`, and flag it for the PM. |
| You don't know enough to judge | Mark **Unsure** and flag it for the PM. Do not guess. |

## 9. Rules for raters

- Judge the result against the **information need** only. Don't let brand, price, or the picture sway you unless the information need asked for it.
- Don't rate based on what you personally like.
- Rate each result by itself. Don't compare it to the others on the page.
- Work separately. Don't discuss ratings with other raters until the round is finished.
- You will not be told which version of search produced a result. Don't try to guess.
- Some items in your batches have known answers and are there to check consistency. Treat every item the same way.
- After your **first batch** we will check in with you. Bring questions and tricky cases then.
- When something in the rubric is unclear, write it down. Do not make up your own rule.

## 10. Worked examples

These use the outdoor gear store. Replace them with real examples from your own sample queries. Cover every level, every goal, and several reasons.

| Goal | Query | Result | Rating | Reason | Why |
|---|---|---|---|---|---|
| Exact match | "osprey atmos ag 65" | Osprey Atmos AG 65, size M | 3 | – | Exact item |
| Exact match | "osprey atmos ag 65" | Osprey Exos 58 | 2 | Missing key detail | Right brand and type, wrong model |
| Exact match | "osprey atmos ag 65" | Gregory Baltoro 65 | 1 | Wrong brand | Wrong brand and wrong model (two misses) |
| Product search | "rain jacket" | Men's waterproof shell | 3 | – | Typical rain jacket, audience is Any |
| Product search | "waterproof coat under $150" | Waterproof coat, $210 | 2 | Missing key detail | Right type, misses the price limit |
| Product search | "rain jacket" | Fleece mid-layer | 0 | Off-topic | Not waterproof, not a rain jacket |
| Product search | "women's trail running shoes" | Women's trail running shoes | 3 | – | Matches category, activity, and audience |
| Product search | "women's trail running shoes" | Men's trail running shoes | 2 | Wrong audience | One miss: audience |
| Product search | "women's trail running shoes" | Women's road running shoes | 2 | Wrong activity | One miss: activity |
| Product search | "kids hiking boots" | Kids' sandals | 1 | Wrong product category | Not boots |
| Browse | "gifts for hikers" | Insulated water bottle | 3 | – | Clear fit for the occasion |
| Browse | "gifts for hikers" | Gift card | 1 | Too general | Fits any occasion, nothing specific to hikers |
| Browse | "waterproofing for tents" | Tent seam sealer | 3 | – | Solves the problem |
| Product search | "tent poles for 2 person tent" | Replacement pole set, 2-person | 3 | – | Fits the named item |
| Product search | "tent poles for 2 person tent" | Trekking poles | 1 | Wrong product category | Shares a word, not the same thing |
| Product search | "rain jacket" | Rain jacket, discontinued | 3 | Out of date | Right match. Availability does not change the grade. |

## 11. Rating file format

Raters work in a simple spreadsheet or CSV with **one row per query, per rater**. A worked example is `sample-eval.csv`. Add a row for each rater, and a new set of rows for each audit date. The file holds grades only, not result names. Raters look at the results themselves, in the search tool or in the results list from `sample-queries-sql.md` (Part 6), in the order searchers saw them.

**Who fills in what:** the PM, the Data Scientist and the experts fill in every column up to `rater`, including `goal` and the `_need` columns, before rating starts. Raters fill in only the columns from `rater` onward: their name, the grades, `grade_reasons`, `expected_products` and `rater_notes`. The PM or analyst calculates `top3_avg` and `query_result` after grading. Raters never fill them in. If a rater thinks a `goal` or `_need` value is wrong, they flag it in `rater_notes`. They don't change it.

| Column | Filled in by | What it holds |
|---|---|---|
| `eval_date` | PM | Date of this audit (yyyy-mm-dd) |
| `rubric_version` | PM | Version used, such as v1 |
| `query_set` | PM | `trend`, `sample`, or `golden` |
| `query` | From the SQL | What the searcher typed |
| `search_visits` | From the SQL | How many visits (sessions) included this query |
| `tier` | From the SQL | `head`, `torso`, or `tail` |
| `total_results` | From the SQL or by re-running the query | How many results the search returned in total, not just the 10 rated. If it is under 10, write `N/A` in the grade columns for the positions with no result. If it is 0, every grade is `N/A`; fill in `expected_products`. |
| `rules_applied` | Data Scientist | How the results were produced, separated by semicolons: for example `lexical`, `vector`, `hybrid (lexical + vector)`, `synonyms`, `brand boost`, `price filter`. Use the same wording every time. **Hide this column from raters** so they can't tell which setup made a result (Section 9). |
| `goal` | **PM or expert**, before rating. Not the rater. | The searcher goal (Section 4) |
| `brand_need`, `product_category_need`, `activity_need`, `gender_age_need`, `attribute_need`, `need_note` | **Experts**, before rating. Not the rater. | The information need (Section 5). Each `_need` column holds what the searcher wants for that field, such as `Any` or `Specific: Osprey`. |
| `rater` | Rater | Their name or ID. Experts use `Expert 1`, `Expert 2`. |
| `grade_1` to `grade_10` | Rater | A 0-3 grade for each of the top 10 results, in the order shown. `grade_1` is the first result. **Never leave a grade empty.** Use `N/A` where the search returned nothing at that position, and `unsure` if you can't grade it (say why in `rater_notes`). (Section 6) |
| `top3_avg` | PM or analyst (calculated) | The average of `grade_1` to `grade_3`, ignoring `N/A` and `unsure`. `N/A` if none of the top 3 has a grade. |
| `query_result` | PM or analyst (calculated) | `pass` or `fail`, using the rule below |
| `grade_reasons` | Rater | Why a grade is below 3, as `position: reason` using Section 7, such as `4: wrong brand; 7: off-topic` |
| `expected_products` | Rater | What they expected to see, if no result was good |
| `rater_notes` | Rater | Anything else: unclear goal or need (`goal?`, `need?`), unsure grades, near-duplicates |

### Pass or fail rule

A query **passes** only if all three are true:
1. The search returned at least one result.
2. No result in positions 1-3 is graded 0.
3. The average of the top three grades (`top3_avg`) is 2 or higher.

Otherwise it **fails**. The rule is the same for every goal. Set it before you look at the results, and don't change it between audits, or the trend becomes meaningless. In a spreadsheet, with `grade_1` to `grade_3` in columns Q to S and `total_results` in column G:

- `top3_avg`: `=IFERROR(ROUND(AVERAGE(Q2:S2),2),"N/A")`
- `query_result`: `=IF(AND(G2>0,COUNT(Q2:S2)>0,COUNTIF(Q2:S2,0)=0,N(AA2)>=2),"pass","fail")` (where `AA2` is the `top3_avg` cell)

### Golden set file

Once the two experts agree on enough results, save the agreed grades in a separate file, `sample-golden-set.csv`. It has **one row per golden query**. It is fixed until the rubric changes version.

| Column | What it holds |
|---|---|
| `golden_date`, `rubric_version` | When the set was built, and the rubric version the experts used |
| `query`, `search_visits`, `tier` | The query and its traffic, as in the main file |
| `goal`, `brand_need`, `product_category_need`, `activity_need`, `gender_age_need`, `attribute_need`, `need_note` | The goal and information need the experts used |
| `grade_1` to `grade_10` | The final grade for each position, which both experts agreed on (after settling any disagreement). `N/A` where the search returned nothing at that position. |

Raters are compared with this file position by position, skipping `N/A`. Every graded position is settled. If the experts can't agree on a grade, don't put it in the file: fix the rubric (a new version) or drop that query first.

The file is only the answer key. Each expert's own grades and their explanation of any disagreement stay in their rows of the main rating file (`rater_notes`). Use those to fix the rubric, and record the fix in the change log (Section 12).

## 12. Change log

| Version | Date | What changed | Why | Approved by |
|---|---|---|---|---|
| v1 | [date] | First version | | [PM] · [DS] |

---

### Before you finalize, check

- [ ] Every level has a clear definition and a real example from your product
- [ ] Every reason is something raters can pick without arguing
- [ ] The glossary covers every term raters might read differently
- [ ] The decision tree gives the same answer as the rating levels
- [ ] Edge cases cover the odd queries you saw in your sample
- [ ] Every query has a goal tag, and a spot-check of tags shows two people agree
- [ ] Every query has an information need with all five fields filled in
- [ ] Two experts can use it and reach the agreement target (Step 6)
