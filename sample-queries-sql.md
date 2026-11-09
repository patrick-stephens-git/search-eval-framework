# Getting Sample Queries with SQL

Companion to **Step 1** of `pm-guide.md`. These are example queries for each sampling method. Hand them to your Data Scientist, or adapt them yourself.

**Is this generalizable?** The pattern is: *clean and count each unique query → add a weight or a tier → draw the sample → attach the results searchers saw.* That works for any product with search logs. The table names, column names, SQL dialect, and what your logs record will differ. See [What to change for your setup](#what-to-change-for-your-setup).

**Next:** paste the output into `sample-eval.csv` (query, search_visits, and tier go in the columns of the same names), then add the rest of the columns there. The CSV holds grades only. Part 6 gives you the list of results for raters to look at.

## Assumed tables

Rename these to match yours.

```
search_events   one row per search
  search_id, session_id, user_id (can be NULL), query_text,
  searched_at, result_count,
  search_mode (optional: how results were produced, e.g. 'lexical', 'vector', 'hybrid')

search_results  one row per result shown (optional, see Part 6)
  search_id, position, item_id, item_title

query_labels    optional: one row per query that already has a goal tag
  query, goal
```

The SQL is written for **PostgreSQL**. Dialect swaps are in the table at the end. Dates are placeholders. Use a window of the last 1-3 months.

---

## Part 0: Build one summary table (run this first)

Everything below reads from it. It cleans each query, counts how many visits (sessions) included it, and puts it in a head, torso, or tail group.

```sql
CREATE TEMP TABLE query_stats AS
WITH cleaned AS (
  SELECT
    lower(trim(regexp_replace(query_text, '\s+', ' ', 'g'))) AS query,
    session_id,
    result_count
  FROM search_events
  WHERE searched_at >= DATE '2026-08-01'
    AND searched_at <  DATE '2026-11-01'
    AND query_text IS NOT NULL
),
agg AS (
  SELECT
    query,
    COUNT(DISTINCT session_id) AS search_visits,   -- visits, so one person searching it 300 times in a visit counts once
    COUNT(*)                    AS searches
  FROM cleaned
  WHERE query <> ''
  GROUP BY query
),
running AS (
  SELECT
    *,
    SUM(searches) OVER (ORDER BY searches DESC, query) AS running_searches,
    SUM(searches) OVER ()                              AS total_searches
  FROM agg
)
SELECT
  query,
  search_visits,
  searches,
  CASE
    WHEN (running_searches - searches) < total_searches / 3.0       THEN 'head'
    WHEN (running_searches - searches) < total_searches * 2 / 3.0   THEN 'torso'
    ELSE 'tail'
  END AS tier                                   -- each tier holds about 1/3 of all searches
FROM running;
```

Check it: `SELECT tier, COUNT(*) AS queries, SUM(searches) FROM query_stats GROUP BY tier;` The three tiers should each have about a third of the searches. The head will have very few queries and the tail very many.

---

## Part 1: Random sample

Every unique query has an equal chance. It over-represents rare queries, so it is best for discovering odd failures, not for the headline number.

```sql
SELECT query, search_visits, tier
FROM query_stats
ORDER BY random()
LIMIT 500;
```

## Part 2: Traffic-weighted sample (the recommended default)

Probability-proportional-to-size. Common queries are more likely to be picked, but rare ones still appear. The weight is the number of *visits*, so one person searching "cake recipes" 300 times in one visit doesn't skew it.

```sql
SELECT query, search_visits, tier
FROM query_stats
ORDER BY -ln(1.0 - random()) / search_visits   -- smaller value = more likely to be picked
LIMIT 500;
```

This is weighted sampling without replacement. It's very close to true proportional sampling when the sample is small compared with the number of queries. Keep the `tier` column so you can report head, torso, and tail separately in Step 11.

## Part 3: Stratified by frequency tier

Equal numbers from head, torso, and tail. Use it for regression checks and for making sure each tier is visible. If you use it for the headline number, weight the results by traffic or report each tier on its own.

```sql
SELECT query, search_visits, tier
FROM (
  SELECT
    *,
    ROW_NUMBER() OVER (PARTITION BY tier ORDER BY random()) AS rn
  FROM query_stats
) t
WHERE rn <= 167;                            -- 500 / 3 tiers
```

If a tier has fewer queries than the target (often the head), you get all of them. Say so in your notes.

## Part 4: Stratified by goal

Equal numbers per searcher goal, with traffic weighting inside each goal. This needs goal tags in `query_labels`. Goal tagging is Step 4, so this is for when you already have tags from rules or an LLM, or when you are topping up a thin goal after tagging.

```sql
SELECT query, goal, search_visits, tier
FROM (
  SELECT
    s.query, s.search_visits, s.tier,
    COALESCE(l.goal, 'unlabeled') AS goal,
    ROW_NUMBER() OVER (
      PARTITION BY COALESCE(l.goal, 'unlabeled')
      ORDER BY -ln(1.0 - random()) / s.search_visits
    ) AS rn
  FROM query_stats s
  LEFT JOIN query_labels l USING (query)
) t
WHERE rn <= 100;                            -- per goal
```

## Part 5: Signs of trouble

Queries that often return nothing, or that searchers quickly re-type. **This sample is not representative of all searches.** Label it that way in the finding.

```sql
WITH ordered AS (
  SELECT
    lower(trim(regexp_replace(query_text, '\s+', ' ', 'g'))) AS query,
    searched_at,
    result_count,
    LEAD(lower(trim(regexp_replace(query_text, '\s+', ' ', 'g'))))
      OVER (PARTITION BY session_id ORDER BY searched_at)    AS next_query,
    LEAD(searched_at)
      OVER (PARTITION BY session_id ORDER BY searched_at)    AS next_at
  FROM search_events
  WHERE searched_at >= DATE '2026-08-01'
    AND searched_at <  DATE '2026-11-01'
    AND query_text IS NOT NULL
),
trouble AS (
  SELECT
    query,
    COUNT(*) AS searches,
    SUM(CASE WHEN result_count = 0 THEN 1 ELSE 0 END) AS zero_results,
    SUM(CASE WHEN next_query IS NOT NULL
              AND next_query <> query
              AND next_at - searched_at <= INTERVAL '60 seconds'
             THEN 1 ELSE 0 END)                       AS rephrased
  FROM ordered
  WHERE query <> ''
  GROUP BY query
)
SELECT query, searches, zero_results, rephrased
FROM trouble
WHERE searches >= 5                                   -- ignore very rare queries
  AND (zero_results + rephrased) * 1.0 / searches >= 0.3
ORDER BY random()
LIMIT 500;
```

"Rephrased" is a rough signal: a new query within 60 seconds in the same session. Sometimes the searcher just moved on to a new need.

---

## Part 6: Attach the top results searchers saw

Take your sample (from any method above) and add each query's top 10 results. This uses the most recent search for each query.

```sql
-- assumes you saved your sample as eval_sample (see Part 7)
WITH latest AS (
  SELECT query, search_id, result_count, search_mode
  FROM (
    SELECT
      lower(trim(regexp_replace(query_text, '\s+', ' ', 'g'))) AS query,
      search_id, result_count, search_mode,
      ROW_NUMBER() OVER (
        PARTITION BY lower(trim(regexp_replace(query_text, '\s+', ' ', 'g')))
        ORDER BY searched_at DESC
      ) AS rn
    FROM search_events
    WHERE searched_at >= DATE '2026-08-01'
      AND searched_at <  DATE '2026-11-01'
  ) x
  WHERE rn = 1
)
SELECT s.query, s.tier, s.search_visits,
       l.result_count AS total_results,
       l.search_mode  AS rules_applied,
       r.position, r.item_id, r.item_title
FROM eval_sample s
JOIN latest l               ON l.query = s.query
LEFT JOIN search_results r  ON r.search_id = l.search_id AND r.position <= 10
ORDER BY s.query, r.position;
```

`total_results` and `rules_applied` go into the CSV columns of the same names. The `LEFT JOIN` keeps queries that returned nothing (they come back with empty result columns). If your logs don't record how results were produced, fill `rules_applied` from your search configuration for that date, or leave it blank.

**If your logs don't record the results shown:** save only the queries, then run each one through your search system on a set date and record the top 10. Write that date down as the sample date. Results can differ by user (personalization, tests), so note which setup you used.

## Part 7: Save it, and keep the trend set fixed

```sql
-- the trend set: draw ONCE, then never re-run
CREATE TABLE eval_trend_set AS
SELECT query, search_visits, tier, DATE '2026-11-08' AS sample_date
FROM query_stats
ORDER BY -ln(1.0 - random()) / search_visits
LIMIT 75;

-- the main sample: new every audit, no overlap with the trend set
-- draw about 25% extra so you still have enough after cleaning (Step 2)
CREATE TABLE eval_sample AS
SELECT query, search_visits, tier, DATE '2026-11-08' AS sample_date
FROM query_stats
WHERE query NOT IN (SELECT query FROM eval_trend_set)
ORDER BY -ln(1.0 - random()) / search_visits
LIMIT 625;                                   -- 500 + 25% spare
```

Save the output as a table with a date. The saved table is your record. Re-running `random()` later gives different queries. Also keep golden-set queries out of the rated sample: remove them from `eval_sample` after Step 6.

---

## What to change for your setup

| What | How it varies |
|---|---|
| Table and column names | Rename to match your logs. |
| What counts as a "visit" | `session_id` works if your logs have sessions. If not, use a device ID plus a date, or count distinct users (`COALESCE(user_id, device_id)`) and keep the column name. |
| Date window | 1-3 months is typical. Use longer for seasonal products, and avoid a window with a one-off event. |
| Query cleaning | Lowercasing and trimming is the minimum. You may want to remove punctuation, fix common typos, or treat plural/singular as the same. Step 2 covers removing bad queries. |
| Results shown | Many products don't log them. See the note in Part 6. |
| Rephrase rule | 60 seconds is a guess. Pick what fits your product. |
| Logs outside SQL | If logs sit in a search tool or analytics product, export one row per unique query with its count, then run the same logic in a spreadsheet or notebook. |
| Tiers and sizes | 500 queries and a split into thirds are starting points. See Step 1 for sizing. |

**Dialect swaps** (the logic doesn't change):

| | PostgreSQL (used above) | BigQuery | Snowflake | MySQL 8+ |
|---|---|---|---|---|
| Random 0-1 | `random()` | `RAND()` | `UNIFORM(0::FLOAT, 1::FLOAT, RANDOM())` | `RAND()` |
| Collapse spaces | `regexp_replace(x, '\s+', ' ', 'g')` | `REGEXP_REPLACE(x, r'\s+', ' ')` | `REGEXP_REPLACE(x, '\\s+', ' ')` | `REGEXP_REPLACE(x, '\\s+', ' ')` |
| Seconds between | `next_at - searched_at <= INTERVAL '60 seconds'` | `TIMESTAMP_DIFF(next_at, searched_at, SECOND) <= 60` | `DATEDIFF('second', searched_at, next_at) <= 60` | `TIMESTAMPDIFF(SECOND, searched_at, next_at) <= 60` |
| Natural log | `ln()` | `LN()` | `LN()` | `LN()` |

**Testing:** the logic was checked in SQLite on about 20,000 made-up searches (date and time-gap syntax adapted): tiers came out at about a third of searches each, cleaning merged case and spacing variants, the trouble query found the zero-result queries, and the trend set and sample didn't overlap. It has not been run on PostgreSQL or on real data, so have your Data Scientist run it and sanity-check the numbers.
