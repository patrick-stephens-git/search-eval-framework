## Problem Statement
- **What problem are we solving?** Data Science teams and product managers don't know what the problem is when working on search engine improvements (whether retrieval or ranking) resulting in trying to improve something without fully understanding what we're trying to improve, because of teams cannot tell which search failures matter most because teams judge relevance by different standards because people disagree on what a correct result is because no written relevance guidelines exist for graders to follow.

### How do we know this is a problem?
- **Business impact:** We would expect to see low activation. Activation means a Data Science team or product manager completes an audit that ends with a Yes or No finding and a recorded Agree from both roles. We would expect many search improvement projects to start with no rated evidence that a problem exists.
- **Behavioral signals:** We would expect to observe graders giving different relevance labels to the same query-result pair. We would expect to observe teams reversing or re-running experiments because they disagree on the result. We would expect to observe teams building private query sets and private quality metrics.
- **Voice-of-customer signals:** We would expect to hear Data Science teams and product managers say "we don't agree on what a good result is." We would expect to hear stakeholders question experiment results in review meetings. We would expect to hear teams ask "what are we trying to improve?" at the start of search projects.
- **Market signals:** We would expect to find competitors and vendors that sell relevance judgment guidelines, labeling tools, or search quality evaluation. We would expect to find analyst and industry reports that name missing relevance definitions as a cause of failed search projects.
---

## Market Signals Evidence Report
### Market Signals Hypothesis
> We would expect to find competitors and vendors that sell relevance judgment guidelines, labeling tools, or search quality evaluation. We would expect to find analyst and industry reports that name missing relevance definitions as a cause of failed search projects.

#### Competitor Positioning
Note: The canonical competitor list was ignored (treated as blank). All entries below are (other). Search snippets were the basis for these claims. I did not open the pages. Vendor pages are marketing copy, not independent evidence.
- (other) Appen sells human raters for e-commerce site search evaluation: [Appen — eCommerce retailer case study](https://techday2022.appen.com/?p=8952) — The page title says Appen's expertise ensures an eCommerce retailer's scalability. A search summary said Appen cites thousands of qualified raters with e-commerce site search expertise. This is vendor marketing.
- (other) Clickworker sells crowd-based search relevance testing: [Clickworker — search relevance](https://www.clickworker.com/search-relevance) — The vendor page offers search relevance as a service. This is vendor marketing, and I did not confirm e-commerce specifics.
- (other) Label Studio gives a ranking template with a per-item 1-5 rating and multi-reviewer overlap: [Label Studio — LLM Ranker template](https://labelstud.io/templates/generative-llm-ranker) — The template lists "evaluate results of semantic search" as a use. The template does not supply relevance definitions. This is vendor documentation.
- (other) Google publishes its search quality rater guidelines in public: [Google — rater guidelines overview](https://blog.google/products/search/overview-our-rater-guidelines-search/) — Google says raters work from one common set of public guidelines. This is a reference model for a written definition of relevance. It is not an e-commerce product.
- (other) Walmart, Zalando, Flipkart, Etsy and Allegro run human labeling programs, and each moves to LLM labels that human labels anchor. Sources are in the other lens sections and in the summary.

Not confirmed: TELUS Digital, Toloka and Surge appeared in search results. Their pages returned errors or did not show e-commerce relevance services. They are not cited.

#### Third-Party Tools Filling the Void
- Judgment lists with grade rubrics, grade examples and adjudication of disagreements, as a workaround for vague guidelines: [awesome-search — Judgment Lists](https://frutik.github.io/awesome-search/Concepts/Judgment-Lists) — The page says vague guidelines lead to noisy labels and misleading metrics. It is a community wiki, and it is not an Amazon source.
- A guide for setting up a relevance evaluation program: [awesome-search — Setting Up a Relevance Evaluation Program](https://frutik.github.io/awesome-search/Articles/Setting-Up-a-Relevance-Evaluation-Program) — The page appeared in results for relevance rating guidelines. I did not confirm its detailed claims.
- A practitioner guide that sets a Cohen's kappa threshold below 0.60 as a sign of ambiguous guidelines: [Tessl — judgment list author](https://tessl.io/registry/testland/judgment-list-author) — The guide advises two raters on a 10-20% overlap set and grade anchors with examples. It also lists e-commerce edge cases such as out-of-stock items and variant SKUs. It is a practitioner skill document, not a standard.
- A guide on how to write labeling guidelines: [Eugene Yan — labeling guidelines](https://eugeneyan.com/writing/labeling-guidelines/) — The page appeared in results for the same gap. It is general, not e-commerce specific.
- Public human-labeled datasets used as shared definitions: [awesome-search — Amazon ESCI Dataset](https://frutik.github.io/awesome-search/Datasets/Amazon-ESCI-Dataset) — The page describes query-product pairs labeled Exact, Substitute, Complement or Irrelevant. This is a community summary, not Amazon's annotation guideline. [awesome-search — Home Depot Product Search Relevance](https://frutik.github.io/awesome-search/Datasets/Home-Depot-Product-Search-Relevance) — A search summary says at least three raters scored each pair on a 1-3 scale, and only the average is published.
- LLM judges with human calibration: [Galileo — calibrate your LLM judge with human annotations](https://galileo.ai/blog/calibrate-llm-judge-human-annotations) — A search summary says the guide treats inter-rater reliability as the main calibration metric. It is not e-commerce specific. It is a vendor blog.

#### Analyst / Industry Callouts
No Gartner or Forrester report was found. All items below come from company engineering blogs, preprints and practitioner blogs.
- Walmart says labeled data is noisy because annotators do not always reach consensus: [arXiv — Walmart (2502.15990)](https://arxiv.org/pdf/2502.15990.pdf) — This claim came from a search summary of the paper.
- Walmart fine-tuned LLMs on over 6 million human-labeled query-item pairs and says human evaluator performance is hard to calibrate and standardize: [arXiv — Walmart (2406.00247)](https://arxiv.org/pdf/2406.00247) — This is a preprint, and it is limited to Walmart's catalog.
- Flipkart reports manual relevance labeling was slow, costly and inconsistent across annotators. Its LLM labeler beat a three-annotator consensus benchmark by about 2.3% on 3-level accuracy: [ZenML — Flipkart](https://www.zenml.io/llmops-database/llm-powered-product-relevance-labeling-for-e-commerce-search) — This is a third-party summary of Flipkart's work.
- Zalando lists different interpretations of relevance criteria among annotators as a consistency problem. Zalando also says human annotators made more brand, product and category errors, likely from fatigue: [arXiv — Zalando (2409.11860)](https://arxiv.org/html/2409.11860v1) — The paper says writing guidelines for semantic relevance is complex. It generates query-specific guidelines with an LLM. [Zalando Engineering — paper announcement](https://engineering.zalando.com/posts/2024/11/llm-as-a-judge-relevance-assessment-paper-announcement.html) — A search summary reports MLLM agreement of 65.6% (English) and 64.7% (German) against 60.2% and 60.5% for human annotators. These are raw percentages, not chance-corrected.
- Allegro says search relevance is rarely a binary decision. It uses expert calibration and tracks annotation agreement such as Cohen's kappa over time: [Allegro Tech — LLM-as-a-judge](https://blog.allegro.tech/2026/08/automating-search-relevance-llm-as-a-judge.html) — The claim came from search summaries.
- Etsy starts with human-curated golden labels, keeps an evolving written relevance labeling guideline, and validates its LLM annotator against the human labels: [scour.ing — mirror of Etsy Code as Craft post](https://scour.ing/@daniel/p/https://www.etsy.com/codeascraft/how-etsy-uses-llms-to-improve-search-relevance) — This is a third-party mirror. The original Etsy URL returned an error and is not cited.
- eBay describes collecting human relevance judgments by crowdsourcing and scoring them with DCG: [eBay Inc. — measuring search relevance](https://innovation.ebayinc.com/stories/measuring-search-relevance/) — This shows human judgment as the baseline. It does not name missing guidelines.
- Human annotation costs: one framework estimates 50,000 queries with 20 products each is one million pairs, or 5,500-8,500 hours of human work: [arXiv — Zalando (2409.11860)](https://arxiv.org/pdf/2409.11860.pdf) — This is a one-annotation-per-pair estimate from a search summary.
- Assessor disagreement can come from real opinion differences or from errors such as ambiguous instructions: [University of Twente — assessor disagreement](https://research.utwente.nl/en/publications/predicting-relevance-based-on-assessor-disagreement-analysis-and-/) — This is an academic paper, not e-commerce specific. An earlier study found disagreement has only minor effects on system comparisons.
- Crowd workers gave unreliable relevance labels for e-commerce queries, judged against click data: [arXiv — Learning to Rank for E-Commerce Search](https://arxiv.org/pdf/1903.04263) — The authors cite related work for this conclusion.

#### Community / Forum Complaints
No Reddit, Hacker News, G2, Capterra or Slack/Discord thread was found. Items below are practitioner posts and a content-aggregator summary.
- Raters use inconsistent criteria. Less domain-savvy raters rate generously, and domain experts rate harshly over small differences: [Doug Turnbull — how pointwise evals fall apart](https://softwaredoug.com/blog/2026/02/23/how-pointwise-evals-fall-apart) — This is a practitioner blog dated Feb 2026 in the URL. The claim came from a search summary.
- DoorDash audited human labels for natural language search and reportedly found them wrong in over half of reviewed cases, with disagreement at boundary levels. The post says the rubric underspecified how to judge compositional intent: [daily.dev — summary of DoorDash post](https://app.daily.dev/posts/llm-as-a-judge-evaluating-natural-language-search-qzclaql93) — The figure comes from the aggregator only. The DoorDash page itself returned an error. DoorDash is local commerce search, not general product search.
- The task of judging search results is highly subjective, and even expert assessors often disagree: [University of Texas — HCOMP 2016 slides](https://ischool.utexas.edu/ml/papers/mcdonnell-hcomp16-slides.pdf) — This is an academic source, not a forum. It is not e-commerce specific.
- A practitioner explanation of what a "relevant" search result is: [awesome-search — What is a Relevant Search Result?](https://frutik.github.io/awesome-search/Articles/What-is-a-Relevant-Search-Result) — The page appeared in results. I did not confirm its detailed claims.

### Research Gaps
- Analyst reports: No results surfaced for Analyst / Industry Callouts from Gartner, Forrester or similar firms. To close this gap, search analyst firm sites for "digital commerce search" or "site search" reports, or ask the user for access to paid reports.
- Community forums: No results surfaced for Community / Forum Complaints on Reddit, Hacker News, G2, Capterra, Slack or Discord. To close this gap, search the Relevance Slack, the r/elasticsearch, r/MachineLearning and r/ecommerce subreddits, and Hacker News threads on the Quepid, Doug Turnbull and Walmart papers.
- Vendor detail: TELUS Digital, Toloka and Surge pages returned errors, so their positioning is not cited. To close this gap, search for their e-commerce search relevance case studies from other URLs.
- Original sources: Amazon's ESCI annotator instructions, the Etsy original post and the DoorDash original post could not be cited. To close this gap, open the ESCI paper and GitHub repository and the Etsy and DoorDash posts directly.
- Pricing: No results surfaced for per-label cost or rater quality benchmarks. To close this gap, request vendor quotes.
- Claims in this report come from search result text. I did not open the pages myself.

### Market Signals Summary
- Some vendors sell human raters for search relevance and e-commerce site search evaluation. [Appen — eCommerce retailer case study](https://techday2022.appen.com/?p=8952) · [Clickworker — search relevance](https://www.clickworker.com/search-relevance) Both are vendor marketing. No source ties them to supplying written relevance definitions.
- Tools exist to run human labeling but they do not supply the definition of relevance. [Label Studio — LLM Ranker template](https://labelstud.io/templates/generative-llm-ranker) · [Tessl — judgment list author](https://tessl.io/registry/testland/judgment-list-author)
- Practitioner guides say vague guidelines cause noisy labels. They advise per-grade rubrics, grade examples, adjudication, and agreement checks. [awesome-search — Judgment Lists](https://frutik.github.io/awesome-search/Concepts/Judgment-Lists) · [Tessl — judgment list author](https://tessl.io/registry/testland/judgment-list-author) · [Eugene Yan — labeling guidelines](https://eugeneyan.com/writing/labeling-guidelines/)
- Several large retailers report that human labels are inconsistent or hard to standardize. Walmart: [arXiv — Walmart (2502.15990)](https://arxiv.org/pdf/2502.15990.pdf) · [arXiv — Walmart (2406.00247)](https://arxiv.org/pdf/2406.00247). Flipkart: [ZenML — Flipkart](https://www.zenml.io/llmops-database/llm-powered-product-relevance-labeling-for-e-commerce-search). Zalando: [arXiv — Zalando (2409.11860)](https://arxiv.org/html/2409.11860v1). DoorDash (one aggregator source): [daily.dev — summary of DoorDash post](https://app.daily.dev/posts/llm-as-a-judge-evaluating-natural-language-search-qzclaql93)
- Annotators interpret relevance criteria in different ways, and writing guidelines for semantic relevance is complex. [arXiv — Zalando (2409.11860)](https://arxiv.org/html/2409.11860v1) · [University of Twente — assessor disagreement](https://research.utwente.nl/en/publications/predicting-relevance-based-on-assessor-disagreement-analysis-and-/) · [Doug Turnbull — how pointwise evals fall apart](https://softwaredoug.com/blog/2026/02/23/how-pointwise-evals-fall-apart)
- Retailers anchor LLM labels to human golden labels and written guidelines. Etsy: [scour.ing — mirror of Etsy Code as Craft post](https://scour.ing/@daniel/p/https://www.etsy.com/codeascraft/how-etsy-uses-llms-to-improve-search-relevance). Allegro: [Allegro Tech — LLM-as-a-judge](https://blog.allegro.tech/2026/08/automating-search-relevance-llm-as-a-judge.html). Walmart: [arXiv — Walmart (2406.00247)](https://arxiv.org/pdf/2406.00247). Zalando: [Zalando Engineering — paper announcement](https://engineering.zalando.com/posts/2024/11/llm-as-a-judge-relevance-assessment-paper-announcement.html)
- Public human-labeled datasets give shared definitions: ESCI uses four grades, and the Home Depot data uses averaged 1-3 scores from at least three raters. [awesome-search — Amazon ESCI Dataset](https://frutik.github.io/awesome-search/Datasets/Amazon-ESCI-Dataset) · [awesome-search — Home Depot Product Search Relevance](https://frutik.github.io/awesome-search/Datasets/Home-Depot-Product-Search-Relevance)
- Human labeling has a cost and scale limit. [arXiv — Zalando (2409.11860)](https://arxiv.org/pdf/2409.11860.pdf) · [ZenML — Flipkart](https://www.zenml.io/llmops-database/llm-powered-product-relevance-labeling-for-e-commerce-search)
- No analyst report was found that names missing relevance definitions as a cause of failed search projects. The hypothesis is only partly supported: vendors and company reports exist, and analyst coverage is absent from the sources found.
---

- **New First Principle:** Every person who judges a search result applies the same written definition of a relevant result, and each rater gives the same rating to a result as the experts do.
---

## Hypothesis
A shared written rubric, applied by raters who are calibrated against an expert golden data set, will result in search labels that the team trusts, thus Data Science teams and product managers on an e-commerce platform can show with rated evidence whether a search problem exists, and can align on it before they start work. The first measurable change is agreement between raters and experts. Assumption: raters reach an accepted agreement level. One example of a level is a Cohen's kappa of at least [0.60](https://tessl.io/registry/testland/judgment-list-author), the level below which a practitioner guide treats guidelines as ambiguous. Input: the best-documented human baseline is raw agreement of [60.2%](https://engineering.zalando.com/posts/2024/11/llm-as-a-judge-relevance-assessment-paper-announcement.html) in English and [60.5%](https://engineering.zalando.com/posts/2024/11/llm-as-a-judge-relevance-assessment-paper-announcement.html) in German. That figure is not corrected for chance, so the true baseline is lower. The evidence is insufficient to estimate how often teams align on a problem today. The gap closes with a count of search improvement projects in the last two quarters that stated a problem with rated evidence, and with the current agreement between your own graders.
---

- **Solution Constraints:**
1. The output must be generic enough to apply to many search relevance improvements and problems in the future.
---

## Success Metrics:
1. [Business Outcome] Problems Confirmed With Alignment: The share of audits that end with a Yes or No finding and a recorded Agree from both the Data Scientist and the Product Manager (claim E). The canonical metrics are ignored, and this definition comes from the Hypothesis. The metric applies to any search relevance problem.
  - Success Metric Target: 80% or more of audits, measured over the two quarters after launch via the alignment record. Assumption: the 80% is a proposal, because no baseline exists in the evidence. Instrumentation is required: record each audit and each Agree or Disagree.
  - Kill Threshold: Fewer than 50% of audits end with both roles recording Agree after two quarters, with rater calibration at target (Metric 2). This result shows that rated evidence does not create alignment.

2. [User Outcome] Rater Calibration: The share of raters who meet the accepted agreement level with the experts on the golden data set (claims B and C). It shows whether the Target User can trust the labels. A Data Science team or product manager who trusts the labels can show whether a problem exists and can complete the job of improving search.
  - Success Metric Target: 100% of raters meet the accepted agreement level, for example 8 of 10 results, before they rate live results. Measured at every calibration via the calibration result. Instrumentation is required: record each calibration result.
  - Kill Threshold: Fewer than 50% of raters meet the accepted agreement level after two revisions of the rubric. This result shows that a written rubric does not align raters.

3. [Product Outcome] Adoption of the Rubric: The share of new search improvement projects that name the rubric version at the start (claim A). It is an Input Metric. Teams that use the rubric produce comparable labels, and comparable labels let an audit end with a finding that both roles accept (Metric 1).
  - Success Metric Target: 100% of new projects within one quarter of launch, measured at project kickoff via the project tracker. Instrumentation is required: add a required field for the rubric version to the kickoff template.
  - Kill Threshold: Below 50% of new projects after two quarters. This result shows that teams do not find the rubric useful.

4. [User Outcome] Finding Names Threshold and Reasons: The share of findings that state the threshold, the rubric version, and a breakdown by reason (claim D). It shows that the Target User can show why a problem exists. This is the job the Target User could not complete before.
  - Success Metric Target: 90% or more of findings, measured at each audit via the finding record. Instrumentation is required: add the threshold, the rubric version, and the reason breakdown as required fields in the finding.
  - Kill Threshold: Below 50% of findings after two quarters. This result shows that teams do not record findings with their evidence.

5. [Guardrail Outcome] Rater Effort per Graded Pair: The rater hours for each 1,000 graded pairs. It protects the Business Outcome from a rise in labeling cost.
  - Success Metric Target: A rise of 20% or less over the baseline, measured in every grading round via rater time logs. Instrumentation is required: log rater hours per round.
  - Kill Threshold: A rise of more than 50% over the baseline for two rounds in a row. This result shows that the rubric costs more than the value it adds.
---

## User Flow:

### Happy Path:
#### Step 1: Get Sample Queries:
- Input: Choose how to pick the sample queries, how many to pick, and how many top results to keep for each. Pick a trend set once, with the same method. Request a new sample at any time. Methods: random, most frequent, stratified, or signs of trouble. Example: 500 sample queries and 50 trend queries, stratified by how often searchers enter them, 3 top results each.
- Output: Receive the sample queries and the trend set, each with the results that searchers see and how many searchers entered it, saved with a sample date. Sample queries are new in every audit and show current problems. The trend set stays the same in every audit and shows change over time. Recommended trend set size: 10% to 20% of the sample size, at least 50 queries. Example: "rain jacket", entered by 12,000 searchers, sampled 1 March.

#### Step 2: Write and Version Rubric:
- Input: Provide the search domain and the list of searcher goals. The goals set the scope of the rubric, and the sample queries from Step 1 anchor it in real searches. Example: a gear store where searchers buy a hiking jacket or a spare part.
- Output: Receive a relevance rubric with a version number, rating levels, reasons for ratings below the top level, and a purchase question. It also holds a definition of relevant for each goal and one example per level. Example levels: Exact match, Close substitute, Related item, Not relevant. Example reasons: wrong product type, expected result missing. Purchase question: "Would the average searcher buy this result for this query? Yes or No. Assume the item is in stock at a fair price."

#### Step 3: Build Golden Set:
- Input: Experts rate a small set of queries, chosen from the sample queries, with the rubric, and answer the purchase question. State the accepted agreement level. Experts choose queries that cover every searcher goal and rating level. The golden set does not share queries with the trend set. Example: two experts rate 50 queries with 3 results each; ratings must match on 8 of 10 results.
- Output: Receive a golden data set and the results that experts rated differently. The set holds results where experts agree, and it names the rubric version. It stays fixed until the rubric gets a new version. Disputed results show where the rubric needs a fix.

#### Step 4: Calibrate Raters:
- Input: Raters rate the golden data set with the rubric and answer the purchase question. Their ratings show if each rater applies the rubric as the experts do. Example: a new rater rates the 50 expert-rated queries.
- Output: Receive a calibration result for each rater, with the results rated differently from the experts. It shows which raters the team can trust. Example: one rater matches the experts on 9 of 10 results.

#### Step 5: Set Problem Threshold:
- Input: State the rating share that counts as a problem. Adjust it at any time. The threshold turns ratings into a yes or no. Example: a problem exists if more than 10% of top results are rated Not relevant.
- Output: Receive the threshold, saved with a version number. The version number shows which threshold each finding used, so the team can compare findings over time.

#### Step 6: Rate Sample Queries:
- Input: Calibrated raters rate the top results of the sample queries and the trend set, and give a reason for each rating below the top level. Raters also answer the purchase question. If no top result is relevant, or the query returns no results, raters may list the products they expect. After a rubric change, they re-rate the trend set. Example: 450 sample queries (500 minus the 50 golden queries) and 50 trend queries, 3 rated results each.
- Output: Receive the judgment list. It holds the sample queries, the trend set, the rated results, the reasons, the purchase answers, the expected products, the disputed results, the sample date, and the rubric version. Disputed results are results where calibrated raters disagree. They point to an ambiguous query or a rubric gap.

#### Step 7: Rank Poor Queries:
- Input: Confirm that the rated queries represent real searches. The confirmation tells the team if the sample is fair to use. Example: the sample mixes frequent and rare queries.
- Output: Receive a list of poor queries, ranked by how many searchers each affects, with the reasons raters gave and the products they expected. A poor query has a top result rated Not relevant, or returns no results. Example: "waterproof coat", where 3 of 3 results are jackets that are not waterproof.

#### Step 8: Confirm Problem:
- Input: Request the problem finding for the judgment list. The finding compares the ratings to the threshold and explains the result.
- Output: Receive a Yes or No, with the numbers, the threshold, the rubric version, the sample date, the share of top results the average searcher would not buy, and a breakdown by reason. A No is a valid result. It means the share is at or below the threshold. The trend set shows the change since the last audit. Example: Yes, 14% of top results are Not relevant, and 18% would not be bought. Of the 14%, 6 points are wrong product type. On the trend set, the share was 12% last audit and is 14% now.

#### Step 9: Record Alignment:
- Input: A Data Scientist and a Product Manager each record Agree or Disagree on the finding. Each role reviews the same evidence and gives a decision. Example: both record Agree.
- Output: Receive the alignment record. It shows if the team accepts the finding. If either role records Disagree, the record keeps the reason. The team can then change the rubric, the threshold, or the sample.

#### Step 10: Update Rubric (If Applicable):
- Input: If the rubric needs a change, change it and state why. Reasons: a new searcher goal, many disputed results or ambiguous queries, or a new business goal. Each change records who approved it. Example: add a "gift" goal.
- Output: Receive a new rubric version and the list of ratings flagged as out of date. The golden data set, calibration results, judgment list, and trend set need new ratings under the new version. Raters calibrate again before they rate. Old versions and findings stay on record. Return to Step 3.
---

## User Stories:

### User Stories for Data Scientists + Product Managers:
#### Scenario 1: Team gets sample queries
- [Must Have] **Get sample queries:** As a Data Scientist or Product Manager I want to choose how the sample queries are picked and receive them with the results searchers see so that the audit covers real searches.
- [Nice-to-Have] **Keep trend set:** As a Data Scientist or Product Manager I want a fixed set of trend queries that raters re-rate in every audit so that I see how results change over time.

#### Scenario 2: Team writes the rubric
- [Must Have] **Provide search context:** As a Data Scientist or Product Manager I want to provide the search domain and the list of searcher goals so that the rubric covers what searchers want.
- [Must Have] **Receive versioned rubric:** As a Data Scientist or Product Manager I want to receive a rubric with a version number, rating levels, reasons for low ratings, and a purchase question so that everyone judges results the same way.

#### Scenario 3: Team checks whether the ratings are trustworthy
- [Must Have] **State agreement target:** As a Data Scientist or Product Manager I want to state the agreement level that I accept so that I know when experts agree and when a rater is calibrated.
- [Must Have] **Receive golden set:** As a Data Scientist or Product Manager I want experts to rate a small golden data set, and to see the results they rated differently, so that I know where the rubric needs a fix.
- [Must Have] **Receive calibration result:** As a Data Scientist or Product Manager I want to receive a calibration result for each rater so that I know which raters I can trust.
- [Nice-to-Have] **Review disputed results:** As a Data Scientist or Product Manager I want to receive the results where calibrated raters disagree so that I find ambiguous queries and rubric gaps.

#### Scenario 4: Team finds the problem
- [Must Have] **Set problem threshold:** As a Data Scientist or Product Manager I want to set and adjust the rating share that counts as a problem so that the ratings give a clear yes or no.
- [Must Have] **Receive rated results:** As a Data Scientist or Product Manager I want to receive the sample queries rated by one or more raters, with reasons and purchase answers, so that I see how well current search meets the rubric.
- [Must Have] **Review poor queries:** As a Data Scientist or Product Manager I want to confirm that the rated queries represent real searches, then receive poor queries ranked by how many searchers each affects, with reasons and expected products, so that I see which problems matter most.
- [Must Have] **Receive problem finding:** As a Data Scientist or Product Manager I want to receive a Yes or No with the numbers, the threshold, the rubric version, and a breakdown by reason so that I can show whether a problem exists and why.
- [Must Have] **Record alignment:** As a Data Scientist or Product Manager I want to record Agree or Disagree on the finding so that the team knows if we accept the problem.

#### Scenario 5: Team changes the rubric (if applicable)
- [Must Have] **Update rubric:** As a Data Scientist or Product Manager I want to change the rubric, state why, and see which ratings become out of date so that the team re-rates under the new version.

### User Stories for Raters:
#### Scenario 1: Rater rates results for a search domain
- [Must Have] **Receive rating guidance:** As a Rater I want to receive the rubric, with the rating levels and an example for each, so that I rate every result by the same standard as other raters.
- [Must Have] **Calibrate on golden set:** As a Rater I want to rate the golden data set and see where I differ from the experts so that I learn to apply the rubric as they do.
- [Must Have] **Rate sample results:** As a Rater I want to rate each sample result, give a reason for low ratings, and answer the purchase question so that my ratings explain why a result fails.
- [Nice-to-Have] **List expected products:** As a Rater I want to list the products I expect when no top result is relevant so that the team sees what search should have returned.
---

## Functional Requirements:

### Scenario 1: Team gets sample queries
- [Nice-to-Have] **FR-1: Choose sampling method:** When a user requests sample queries, the system shall let the user choose how the system picks them. Source: Get sample queries.
- [Nice-to-Have] **FR-2: Offer sampling methods:** The system shall offer four methods: random, most frequent, stratified, and signs of trouble. Source: Get sample queries.
- [Must Have] **FR-3: Stratify by frequency:** The system shall apply the stratified method by returning equal numbers of queries from three groups, each covering one third of all searches. Source: Get sample queries.
- [Nice-to-Have] **FR-4: Stratify by label:** When a user provides a label for each query, the system shall let the user stratify by that label with equal numbers per group. Source: Get sample queries.
- [Nice-to-Have] **FR-5: Apply trouble method:** The system shall apply the signs-of-trouble method by returning queries that gave no results or that searchers rephrased. Source: Get sample queries.
- [Must Have] **FR-6: Default sampling method:** If the user chooses no method, the system shall use the stratified method by query frequency. Source: Get sample queries.
- [Must Have] **FR-7: Return sample queries:** When a user requests a number of sample queries, the system shall return that number of queries that real searchers entered. Source: Get sample queries.
- [Nice-to-Have] **FR-8: Label trouble sample:** When the system uses the signs-of-trouble method, it shall state that the sample does not represent all searches. Source: Get sample queries.
- [Nice-to-Have] **FR-9: Keep top-result count:** When a user selects a number of top results, the system shall use that number for every query. Source: Get sample queries.
- [Must Have] **FR-10: Default top results:** If the user selects no number of top results, the system shall use 3. Source: Get sample queries.
- [Must Have] **FR-11: Return query results:** The system shall return, for each sample query, the top results that searchers see. Source: Get sample queries.
- [Must Have] **FR-12: Show query frequency:** The system shall show, for each sample query, how many searchers entered it. Source: Get sample queries; Review poor queries (Scenario 4).
- [Nice-to-Have] **FR-13: Save sample date:** The system shall save each sample with the date of the sample. Source: Get sample queries.
- [Nice-to-Have] **FR-14: Keep earlier samples:** When a user requests a new sample, the system shall return new queries and keep earlier samples. Source: Get sample queries.
- [Nice-to-Have] **FR-15: Pick trend set:** When a user requests a trend set of a given size, the system shall pick that many queries with the chosen sampling method. Source: Keep trend set.
- [Nice-to-Have] **FR-16: Keep trend set fixed:** The system shall return the same trend set queries in every later audit. Source: Keep trend set.
- [Nice-to-Have] **FR-17: Recommend trend size:** The system shall recommend a trend set size of 10% to 20% of the sample size, with a minimum of 50 queries. Source: Keep trend set.
- [Nice-to-Have] **FR-18: Separate trend queries:** The system shall not place a trend set query in the golden data set. Source: Keep trend set; Receive golden set (Scenario 3).
- [Nice-to-Have] **FR-19: Flag inactive query:** When searchers no longer enter a trend set query, the system shall flag that query as inactive. Source: Keep trend set.
- [Nice-to-Have] **FR-20: Replace trend query:** When a user replaces a trend set query, the system shall pick a new query with the chosen sampling method and keep the replacement date. Source: Keep trend set.
- [Nice-to-Have] **FR-21: Keep replaced queries:** The system shall keep each replaced trend set query and show it on request. Source: Keep trend set.

### Scenario 2: Team writes the rubric
- [Must Have] **FR-22: Keep domain and goals:** When a user provides a search domain and a list of searcher goals, the system shall keep both for later steps. Source: Provide search context.
- [Must Have] **FR-23: Accept any domain:** The system shall accept any search domain and any number of searcher goals. Source: Provide search context.
- [Must Have] **FR-24: Return versioned rubric:** When a search domain and a searcher goal exist, the system shall return a rubric with a version number. Source: Receive versioned rubric.
- [Must Have] **FR-25: Define relevance per goal:** The system shall include in the rubric a written definition of a relevant result for each searcher goal. Source: Receive versioned rubric.
- [Must Have] **FR-26: Return chosen levels:** When a user selects a number of rating levels, the system shall return the rubric with that number of rating levels. Source: Receive versioned rubric.
- [Must Have] **FR-27: Return level examples:** The system shall include in the rubric one example result for each rating level. Source: Receive versioned rubric.
- [Must Have] **FR-28: List low-rating reasons:** The system shall include in the rubric a list of reasons for ratings below the top level. Source: Receive versioned rubric.
- [Nice-to-Have] **FR-29: State purchase question:** The system shall include in the rubric the purchase question: would the average searcher buy this result for this query, Yes or No. Source: Receive versioned rubric.
- [Nice-to-Have] **FR-30: State purchase assumption:** The system shall state in the rubric that the purchase question assumes the item is in stock at a fair price. Source: Receive versioned rubric.
- [Must Have] **FR-31: Show same guidance:** The system shall show the same rubric version to every rater and every user. Source: Receive versioned rubric; Receive rating guidance (Scenario 6).

### Scenario 3: Team checks whether the ratings are trustworthy
- [Must Have] **FR-32: Keep agreement target:** When a user states an accepted agreement level, the system shall keep it for comparison with each agreement result. Source: State agreement target.
- [Must Have] **FR-33: Record expert ratings:** When an expert rates a result with a rating level and a purchase answer, the system shall record both. Source: Receive golden set.
- [Must Have] **FR-34: Return golden set:** When experts finish rating, the system shall return a golden data set of the results where experts agree. Source: Receive golden set.
- [Must Have] **FR-35: List expert disputes:** The system shall list each result that experts rated differently. Source: Receive golden set.
- [Nice-to-Have] **FR-36: Name golden version:** The system shall name the rubric version on the golden data set. Source: Receive golden set.
- [Must Have] **FR-37: Keep golden set fixed:** The system shall keep the golden data set unchanged until the rubric gets a new version. Source: Receive golden set.
- [Must Have] **FR-38: Return calibration result:** When a rater finishes rating the golden data set, the system shall return a calibration result for that rater. Source: Receive calibration result; Calibrate on golden set (Scenario 6).
- [Must Have] **FR-39: Show differing results:** The system shall show the rater each golden result where the rater's rating differs from the experts' rating. Source: Calibrate on golden set (Scenario 6).
- [Must Have] **FR-40: State calibration status:** The system shall state whether each rater meets the accepted agreement level. Source: State agreement target; Receive calibration result.
- [Must Have] **FR-41: Accept calibrated raters:** The system shall accept live ratings only from raters who meet the accepted agreement level. Source: Receive calibration result.
- [Nice-to-Have] **FR-42: Use current version:** The system shall calculate agreement from ratings made under the current rubric version only. Source: Receive calibration result; Update rubric (Scenario 5).
- [Nice-to-Have] **FR-43: List disputed results:** When two or more calibrated raters rate the same result, the system shall list each result they rated differently as disputed. Source: Review disputed results.
- [Nice-to-Have] **FR-44: Show disputed count:** The system shall show how many disputed results each query has. Source: Review disputed results.

### Scenario 4: Team finds the problem
- [Must Have] **FR-45: Keep threshold:** When a user states a rating level and a share of top results, the system shall keep both as the problem threshold. Source: Set problem threshold.
- [Nice-to-Have] **FR-46: Version threshold:** When a user changes the threshold, the system shall save it as a new threshold version. Source: Set problem threshold.
- [Must Have] **FR-47: Accept any rater count:** The system shall accept ratings from one or more calibrated raters for each result. Source: Receive rated results.
- [Nice-to-Have] **FR-48: Exclude golden queries:** The system shall exclude golden data set queries from the sample queries that raters rate. Source: Receive rated results; Receive golden set.
- [Must Have] **FR-49: Return judgment list:** When raters finish rating, the system shall return the judgment list with ratings, reasons, purchase answers, expected products, sample date, and rubric version. Source: Receive rated results.
- [Must Have] **FR-50: Return poor queries:** When a user confirms that the rated queries represent real searches, the system shall return the list of poor queries. Source: Review poor queries.
- [Must Have] **FR-51: Define poor query:** The system shall list a query as poor when raters give a top result the lowest rating level or the query returns no results. Source: Review poor queries.
- [Nice-to-Have] **FR-52: Show lowest-level count:** The system shall show, for each poor query, how many of its top results have the lowest rating level. Source: Review poor queries.
- [Must Have] **FR-53: Rank poor queries:** The system shall rank poor queries by the number of searchers that each query affects. Source: Review poor queries.
- [Nice-to-Have] **FR-54: Sort by count:** When a user sorts poor queries by lowest-level count, the system shall order them by that count. Source: Review poor queries.
- [Must Have] **FR-55: Show reasons and products:** The system shall show, for each poor query, the reasons raters gave and the products they expected. Source: Review poor queries.
- [Must Have] **FR-56: Return finding:** When a user requests the problem finding, the system shall return Yes if the rating share is above the threshold, and No if it is not. Source: Receive problem finding.
- [Must Have] **FR-57: Show finding details:** The system shall show with the finding the rating share, the threshold, the rubric version, the sample date, and the number of queries rated. Source: Receive problem finding.
- [Must Have] **FR-58: Show reason breakdown:** The system shall show the share of rated top results for each reason. Source: Receive problem finding.
- [Nice-to-Have] **FR-59: Show purchase share:** The system shall show the share of top results with a No purchase answer. The Yes or No finding does not use it. Source: Receive problem finding.
- [Nice-to-Have] **FR-60: Show trend change:** The system shall show the trend set result next to the trend set result of the last audit. Source: Receive problem finding; Keep trend set.
- [Nice-to-Have] **FR-61: Compare shared queries:** The system shall compare trend set results across audits using only queries that both audits rated, and state the number of queries compared. Source: Receive problem finding; Keep trend set.
- [Must Have] **FR-62: Record decision:** When a Data Scientist or Product Manager records Agree or Disagree on a finding, the system shall keep the decision with the role. Source: Record alignment.
- [Nice-to-Have] **FR-63: Require disagree reason:** When a role records Disagree, the system shall require a reason and keep it with the finding. Source: Record alignment.
- [Must Have] **FR-64: Mark finding aligned:** The system shall mark a finding as aligned only after a Data Scientist and a Product Manager both record Agree. Source: Record alignment.

### Scenario 5: Team changes the rubric (if applicable)
- [Must Have] **FR-65: Save new version:** When a user changes the rubric or a searcher goal, the system shall save a new rubric version and keep earlier versions. Source: Update rubric.
- [Nice-to-Have] **FR-66: Record change:** The system shall record, for each new version, what changed, why, who approved it, and the date. Source: Update rubric.
- [Nice-to-Have] **FR-67: Require both approvals:** The system shall mark a rubric version as final only after a Data Scientist and a Product Manager both approve it. Source: Update rubric.
- [Must Have] **FR-68: Flag old ratings:** When the rubric gets a new version, the system shall flag all ratings made under earlier versions as out of date. Source: Update rubric.
- [Nice-to-Have] **FR-69: Show flagged ratings:** The system shall show which ratings are out of date next to the rated results and in a list. Source: Update rubric.
- [Nice-to-Have] **FR-70: Keep rating history:** The system shall keep ratings flagged as out of date and show them on request. Source: Update rubric.
- [Must Have] **FR-71: Require recalibration:** When the rubric gets a new version, the system shall require each rater to meet the accepted agreement level again before rating. Source: Update rubric; Calibrate on golden set (Scenario 6).
- [Nice-to-Have] **FR-72: Keep earlier findings:** The system shall keep earlier findings and show the rubric version that each finding used. Source: Update rubric.

### Scenario 6: Rater rates results for a search domain
- [Must Have] **FR-73: Record rating:** When a rater rates a result with a rating level, the system shall record that rating. Source: Rate sample results.
- [Must Have] **FR-74: Require reason:** When a rater rates a result below the top level, the system shall require a reason from the rubric list. Source: Rate sample results.
- [Nice-to-Have] **FR-75: Record purchase answer:** When a rater rates a result, the system shall record the rater's Yes or No purchase answer. Source: Rate sample results.
- [Nice-to-Have] **FR-76: Keep expected products:** When a rater lists expected products for a query, the system shall keep them with that query. Source: List expected products.
- [Must Have] **FR-77: Separate rater ratings:** The system shall keep each rater's ratings separate from the ratings of other raters. Source: Rate sample results.

### Edge Case Requirements:
- [Nice-to-Have] **FR-E1:** If only one rater rated the results, the system shall return that rater's ratings and state that it can list no disputed results. Source: Receive rated results; Review disputed results.
- [Nice-to-Have] **FR-E2:** If a rater leaves a result unrated, the system shall list that result as unrated in the judgment list. Source: Rate sample results.
- [Nice-to-Have] **FR-E3:** If no query is poor, the system shall state that it found no poor queries. Source: Review poor queries.
- [Nice-to-Have] **FR-E4:** If a query returns no results, the system shall list it as poor and let raters list expected products. Source: Review poor queries; List expected products.
- [Nice-to-Have] **FR-E5:** If a user requests more sample queries than searchers entered, the system shall return all available queries and state the number returned. Source: Get sample queries.
- [Nice-to-Have] **FR-E6:** If a group holds fewer queries than its equal share, the system shall return all queries in that group and state the shortfall. Source: Get sample queries.
- [Nice-to-Have] **FR-E7:** If a query has no label when the user stratifies by label, the system shall place it in a group named "unlabeled". Source: Get sample queries.
- [Nice-to-Have] **FR-E8:** If every rating from earlier audits is out of date, the system shall state that no comparable trend result exists. Source: Receive problem finding; Update rubric.
- [Nice-to-Have] **FR-E9:** If no rater meets the accepted agreement level, the system shall state that it accepts no live ratings. Source: Receive calibration result.
- [Must Have] **FR-E10:** If the judgment list has no rated results, the system shall state that no finding is available. Source: Receive problem finding.
- [Nice-to-Have] **FR-E11:** If raters rated the same result under two rubric versions, the system shall not combine those ratings in one agreement result. Source: Receive calibration result; Update rubric.
- [Nice-to-Have] **FR-E12:** If two audits share no trend set queries, the system shall state that no trend comparison exists. Source: Receive problem finding; Keep trend set.

### Open Questions:
- None.
---

### Idea 1: Full Rubric Audit
- **"What if ...?"** What if we receive the search domain, the searcher goals, and a sample of real queries, have experts and calibrated raters rate the top results against a written rubric, and compare the rated share to a threshold, so that the team gets a Yes or No finding with reasons?
- **Trust inflection:** "How might we make the finding something that both roles accept?" By having raters show that they apply the rubric as the experts do before their ratings count. The finding shows the threshold, the rubric version, and the reasons next to the Yes or No.
- **Imagine this:** The Data Scientist and the Product Manager read one finding. They no longer argue about what a correct result is. Each can see the rubric, who rated, and why results failed.

### Idea 2: Expert-Only Quick Check
- **"What if ...?"** What if we have two or three experts rate a golden set until they meet the accepted agreement level with each other, then rate a small sample of queries directly against the rubric, with no separate rater pool, and compare the share they rate Not relevant to the threshold, so that the team gets a first Yes or No in days?
- **Speed inflection:** "How might we get a first answer before the team builds a rater pool?" By letting the experts act as both the ground truth and the raters on a small sample, once they meet the agreement level with each other. The finding is marked provisional until a larger sample confirms it.
- **Imagine this:** The team gets an early signal about whether a problem exists. It needs fewer people and no calibration. Everyone knows the answer is early and can ask for a larger audit if the signal is Yes.

### Idea 3: Adopt a Public Rubric
- **"What if ...?"** What if we start from a published relevance scale, such as the four-grade scale in public shopping datasets, and send the sample queries to an outside rating team that follows it, so that the team gets rated evidence without writing a rubric from scratch?
- **Starting-point inflection:** "How might we avoid writing the rubric and building a rater team?" By adopting an existing scale and its examples, and adding only our searcher goals and reasons. A small set of our experts' ratings checks the outside raters.
- **Imagine this:** The team starts with a rubric that others in the industry already use. There are fewer debates about the scale. The team still adds the goals that are specific to its store.
---

## Recommended Solution:
- **Recommended Solution:** Idea 1: Full Rubric Audit
- **Hypothesis Fit:** The Hypothesis has two parts: a shared written rubric, and raters who are calibrated against an expert golden data set. Idea 1 is the only option that tests both parts. It meets all 42 Must Have requirements and breaks no Solution Constraint. It misses no requirement. The Nice-to-Have items, such as the trend set and the purchase share, can be added during the build.
- **Evidence Anchor:** The best-documented human baseline is raw agreement of [60.2%](https://engineering.zalando.com/posts/2024/11/llm-as-a-judge-relevance-assessment-paper-announcement.html) in English and [60.5%](https://engineering.zalando.com/posts/2024/11/llm-as-a-judge-relevance-assessment-paper-announcement.html) in German. This figure is not corrected for chance. It shows that people disagree without a shared rubric and calibration.
- **Key Assumption:** Raters can reach the accepted agreement level with the experts by using the written rubric. Cheapest test: before any build, two experts rate 30 results with a draft rubric. If they agree on 8 of 10 results within one rubric revision, then run the same test with 3 raters.
- **Why Not the Alternatives:**
   - Idea 2: Expert-Only Quick Check: It tests only agreement between experts, so a Yes would not show that a wider rater pool applies the rubric the same way (Metric 2).
   - Idea 3: Adopt a Public Rubric: A public scale gives grades but not our searcher goals or reasons, and the outside raters still need calibration, so it saves little and weakens our shared definition.
- **Kill Condition:** Metric 2 Kill Threshold: fewer than 50% of raters meet the accepted agreement level after two revisions of the rubric. Also Metric 1 Kill Threshold: fewer than 50% of audits end with both roles recording Agree after two quarters. Cut or pivot at either point.
---

## Recommended Solution Mechanic:
- **Input(s):**
    - The search domain and the list of searcher goals.
    - The queries that searchers entered, how many searchers entered each, and the top results that searchers see for each.
    - The sampling choices: the method, the number of queries, and the number of top results.
    - Ratings and purchase answers from experts on a small set of queries.
    - The accepted agreement level.
    - Ratings, reasons, purchase answers, and expected products from raters.
    - The problem threshold: a rating level and a share of top results.
    - An Agree or Disagree from a Data Scientist and from a Product Manager.
    - A rubric change with the reason and the approval.
- **Operation:**
    - Draw the sample queries with the chosen method, and attach the top results and the searcher counts.
    - Write the rubric from the domain, the goals, and the sample queries, and give it a version number.
    - Collect expert ratings on the golden set queries. Keep the results where experts agree, and list the results where they differ.
    - Collect rater ratings on the golden data set. Compare them with the expert ratings. Mark each rater as calibrated or not calibrated against the accepted agreement level.
    - Collect ratings, reasons, and purchase answers from calibrated raters on the remaining sample queries.
    - List each query as poor when a top result has the lowest rating level or the query returns no results. Rank the poor queries by searchers affected.
    - Compare the rated share with the threshold. Return Yes or No with the numbers, the threshold, the rubric version, and the share for each reason.
    - Record each role's Agree or Disagree, and mark the finding aligned when both roles Agree.
    - On a rubric change, save a new version, flag earlier ratings as out of date, and require raters to calibrate again.
- **Output(s):**
    - A rubric with a version number — it replaces the missing written guidelines, so every person judges results by the same standard.
    - A golden data set and a calibration result for each rater — they show which raters apply the rubric as the experts do, which tests the Key Assumption.
    - A judgment list with reasons and purchase answers — it gives the rated evidence that search results fail and why.
    - Poor queries ranked by searchers affected, with reasons and expected products — they show which failures matter most.
    - A Yes or No problem finding with the numbers, the threshold, the rubric version, and a breakdown by reason — it proves whether a problem exists and why.
    - An alignment record — it shows whether the Data Scientist and the Product Manager accept the problem before work starts.
    - A list of out-of-date ratings after a rubric change — it keeps old evidence from feeding a new finding.
---

## Build vs Buy Evaluation

### Buy Options Researched
| Vendor/Product | What It Does | Pricing | Coverage/Fit | Integration | Known Limitations | Source |
|---|---|---|---|---|---|---|
| Quepid (OpenSource Connections) | Tool for building judgment lists, scoring rankings, and comparing rankers. Raters judge query and result pairs on a 0-3 scale, with one to three raters per pair. | Hosted app described as free and public. Self-hosting is also described. | Partial | Connects to Elasticsearch with an endpoint and API key. Third-party page says it drives any engine over HTTP. | The pages I read did not describe calibration against a golden set, a problem threshold, or an alignment record. Built for text queries. A third-party page lists limits for vector search. | [Elastic — Creating judgement lists with Quepid](https://www.elastic.co/search-labs/blog/quepid-judgement-lists) · [awesome-search — Quepid](https://frutik.github.io/awesome-search/Tools/Quepid) ✓ Verified |
| Releval | Tool to evaluate, track, and improve search relevance with metrics and query templates. Listing says it supports human and AI judging. | Listing shows Basic $99/month, Pro $299/month, Enterprise custom, plus $0.02 per request. | Partial | Listing names Elasticsearch, OpenSearch, Solr, Vespa, and custom search APIs, plus CI/CD. | Third-party listing only, so claims are unconfirmed on the vendor site. Listing says AI judging details are not public. No free tier. | [Stork.AI — Releval](https://www.stork.ai/en/releval) ✓ Verified (directory listing; vendor site not fetched) |
| Clickworker | Managed service that supplies trained crowd workers who judge a customer's search results, with quality checks and a project manager. | Not stated. Page says to contact sales. | Partial (raters only) | Customer supplies search results and criteria. Results come back by API or in a structured data format. | Sales-gated. Page gives no rating scale, turnaround time, or rater agreement figures. | [Clickworker — Search Relevance](https://www.clickworker.com/search-relevance) ✓ Verified |
| Appen | Human evaluators who rate search results for eCommerce and search clients. | Unknown, requires a sales call. | Partial (raters only) | Not verified. | Not verified. The case study page failed to load. | No public URL. Source: Appen (no stable public URL found at time of research). |
| Label Studio (HumanSignal) | General data labeling tool with ranking and multi-annotator review options. | Not verified. Aggregator pages disagree on the paid tier price. | Adjacent | Not verified. | Not built for search relevance audits. Not verified. | No public URL. Source: Label Studio (no stable public URL found at time of research). |

### Six-Dimension Evaluation
| Dimension | Verdict | Justification |
|---|---|---|
| 1. Core Competency Fit | Favors Build | The shared rubric, the golden set, and the Yes or No finding are what solve the Problem Statement and are specific to our store. Rating tools and raters are generic. |
| 2. Buy Market Maturity | Favors Build | Each option covers part of the work (judging, metrics, or raters). None that I could verify covers rubric versioning, calibration, the threshold finding, and alignment together. |
| 3. Time-to-Value | Favors Buy | Quepid's hosted app is described as free and public, and a managed rater service is ready-made. No engineering estimate exists, so this is an assumption. |
| 4. Total Cost of Ownership (3-Year) | Favors Buy | The tools cost $0 to $299 per month. Raters cost money whether we build or buy. Building the whole workflow adds engineering and maintenance cost. |
| 5. Data & Workflow Ownership | Favors Build | Rubric versions, the golden set, findings, and alignment records are our evidence. We need to own their history and structure. |
| 6. Strategic Optionality | Neutral | Owning the audit layer could support later work such as a trend set. No stated company plans show two future investments. |

**Tally:** 3 favor Build, 2 favor Buy, 1 neutral.

### Recommendation: Build

**Reasoning:**
- The split is closer than 4-2, so Core Competency Fit is the tiebreaker, and it favors Build. The close call is acknowledged.
- Build the thin layer that is ours: the rubric with versions, the golden set, rater calibration status, the threshold finding, and the alignment record.
- Reuse generic parts where they fit. The rating interface can be a tool such as Quepid, and human raters can come from a managed service.

**If Build:**
- **Why not Buy Quepid:** It covers judging and metrics. The pages I read did not describe calibration, a threshold finding, or an alignment record. It stays a candidate component.
- **Why not Buy Releval:** Only a third-party listing was verified, and it gives metrics and tracking, not the rubric, calibration, or alignment workflow.
- **Why not Buy Clickworker:** It supplies raters only. It is sales-gated and shows no rater agreement figures. It stays a candidate source of raters.
- **Why not Buy Appen:** I could not verify the source, and it appears to supply raters only.
- **Why not Buy Label Studio:** It is a general labeling tool. I could not verify it, and it does not cover the audit workflow.
- **Build Risks Acknowledged:** Engineering time to first use is unknown. The team takes on maintenance of the audit layer. Time spent building is not spent on other search work.

**Key Assumption:**
- No available product handles rubric versioning, calibration against a golden set, the threshold finding, and the alignment record together. If one does, the recommendation flips to Buy.
---
