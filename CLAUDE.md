# AWS Knowledge Graph — run playbook

This repo is a citation-backed knowledge graph of Amazon Web Services. An hourly scheduled run makes **one change** per run: it either adds one new, unique article or refreshes a small batch (1–4) of existing articles whose verification has gone stale (see 2a). Follow these steps in order. Read `SCHEMA.md` before writing.

## 1. Sync and orient
```bash
git pull --rebase origin main
python3 scripts/kg.py build      # must print OK; fix the repo first if it doesn't
```
Read `graph/stats.md`, `graph/frontier.md` and `topics/seeds.yaml`. `graph/index.json` lists every existing node (ids, titles, aliases, verified date, status, source URLs).

AWS has no named releases, so freshness is by date. Every article you write or refresh gets today's UTC date in `verified`. Determine the **re:Invent cut-off**: the last day of the most recent AWS re:Invent that has already ended (check reinvent.awsevents.com or the AWS News Blog's re:Invent recap). Pass it as `--since` below; if you can't confirm it, omit `--since` and the 180-day rule alone applies.

## 2. Pick the topic (graph-driven)
Use the current UTC hour:
- **Even hour → depth.** Take the highest-ranked node in `graph/frontier.md` (referenced by the most articles but not yet written) that fits a domain in `topics/seeds.yaml`. This turns dangling edges into real nodes.
- **Odd hour → refresh first, then breadth.** Run `python3 scripts/kg.py stale --since <re:Invent cut-off>`. If it lists any stale article, refresh exactly the articles in its **Refresh batch for this run** per step 2a; that is this run's change. The batch size adapts so the backlog clears within about two weeks (1–4 per run) and pairs the stalest article with linked or same-domain articles so research overlaps. If it prints a WARNING that the backlog won't clear at the cap, still refresh the batch and mention the warning in the commit body. Only if nothing is stale, do breadth: take the domain in `topics/seeds.yaml` with the fewest articles (ties: alphabetical, skipping the domain of the most recent commit) and pick an unwritten seed topic, or another important topic in that domain.
- If the frontier is empty or no frontier node fits, do an odd-hour run (refresh first, then breadth).

After each re:Invent this spends the odd hours refreshing until nothing is stale, while even hours keep adding frontier articles.

For a new article, prove it is unique (skip this for a refresh picked from `kg.py stale`):
```bash
python3 scripts/kg.py similar "<candidate title>" --since <re:Invent cut-off>
```
Each match shows its `[verified …]` date; matches verified before the cut-off are flagged `STALE`.
- If an existing title or alias names the same thing (or a ≥ 0.5 match is the same subject after review): if that match is flagged `STALE`, **refresh it** (step 2a) instead of writing a new article; otherwise pick a different topic.
- If any score is ≥ 0.5, **review before skipping**: read the summary (and aliases) of each matching node. Proceed only if it is clearly a different subject, and record the reason in the commit message body, one line per match, e.g. `similar: 0.55 vs kt:amazon-s3 — "S3 storage classes" covers tiers, not the service`. If you are unsure, pick a different topic.
- Scores below 0.5 need no note.

Never write a second article on a subject that already has a node — not a rename, a sub-angle that restates it, a former service name (e.g. "Kinesis Data Firehose" vs "Amazon Data Firehose", "AWS SSO" vs "IAM Identity Center", "SageMaker" vs "Amazon SageMaker AI"), an abbreviation, or a synonym. Record former names and abbreviations as `aliases`.

## 2a. Refresh a stale article (instead of adding one)
Use this for each article in the odd-hour refresh batch from `kg.py stale`, or (as a batch of one) whenever step 2 lands on an existing node flagged `STALE`. The refresh batch is this run's one change. Apply every rule below to each article in the batch; if one article can't be re-verified, leave it out and refresh the rest.
- Keep the file path, `id`, `created` date and existing edges, so links from other articles stay intact. Never rename or delete the node.
- Re-verify the article: re-open every cited source (search for it first, per step 3), check What's New with AWS and the AWS News Blog for launches and changes to the service since its `verified` date, and confirm `status` and every stated quota, price and number.
- Rewrite only what changed. Replace or add sources as needed (same rules as step 3); update every refreshed source's `accessed` date; set `verified` to today.
- If the service was renamed, update `title` to AWS's new official name and add the former name to `aliases`. If it was closed to new customers, deprecated or retired, set `status` (and, for deprecated/retired, add a `replacedBy` edge to AWS's recommended successor); the successor is a different subject and can be written as a new article in a later run.
- If the change is notable, add a short **What changed (<YYYY-MM>)** section with citations.
- If nothing changed, still set `verified` to today and bump the `accessed` dates of the sources you re-opened.
- If you cannot open enough sources to re-verify an article, don't refresh it. If no article in the batch can be refreshed, pick a different topic.

## 3. Research
- Run several web searches; read the actual pages (WebFetch), don't rely on snippets or memory.
- Collect 3–6 credible sources: at least 1 official AWS source (docs or AWS training/exam guide) unless the node is an `ExternalConcept`, and at least 2 of a strong type (see `SCHEMA.md`). Prefer docs.aws.amazon.com, the Well-Architected Framework, AWS Prescriptive Guidance, whitepapers, service FAQ and pricing pages, What's New posts and the AWS News Blog; add standards bodies (IETF, NIST, CNCF) and fedramp.gov / DISA where relevant. **Never cite Wikipedia**, content farms, exam-dump or practice-question sites, or AI-generated pages.
- Every URL you cite must be one you actually opened this run. Never invent or guess a URL. If a docs page won't render, find another official page that does.
- State quotas, prices and numbers exactly as the source gives them, and only those you verified this run. Prices and default quotas change and vary by Region: say which Region (or "us-east-1 example") and date them ("as of 2026-11"); say whether a quota is adjustable.
- Paraphrase. No quotation longer than 15 words; at most one quote per source.
- Check the lifecycle (generally available, preview, closed to new customers, deprecated, retired; AWS publishes these on the service's page and in What's New) and set `status`. Deprecated/retired services need a `replacedBy` edge to their successor.
- For public-sector topics, check the AWS Services in Scope pages for FedRAMP / DoD SRG / GovCloud availability rather than assuming a service is authorized.

## 4. Write the node
- Path `articles/<domain>/<slug>.md`, id `kt:<slug>`. Pick the most specific `type` from the ontology (`Service` for a whole AWS service, `Feature` for a capability inside one, `Resource` for things you provision like a VPC or IAM role, `Limit` for a quota, `PricingModel` for how something is billed).
- 350–900 words, practitioner level: what it is, how it works, quotas/pricing and gotchas, when to use it vs. alternatives, and a **Connections** section explaining the edges.
- Every factual paragraph carries footnote citations `[^sN]`; list all sources in `## Sources`.
- Edges: 1–3 `broader`, 2–6 `related` or typed `relations`. Use typed predicates where they fit (`dependsOn`, `runsOn`, `alternativeTo`, `governedBy`, `securedBy`, `monitoredBy`, `pricedBy`, `scopedTo`, `compliesWith`, `replacedBy`, `configuredVia`, `coveredBy` a certification, …). **Link to existing node ids wherever they fit** (check `graph/index.json`); new target ids are fine and join the frontier. Add a `coveredBy` edge (e.g. `kt:aws-certified-cloud-practitioner`, `kt:aws-certified-solutions-architect-associate`) only when the current official exam guide lists the topic.
- If a needed predicate or class is missing, add it to `ontology/ontology.ttl` (with a label and, if applicable, inverse) in the same commit, and a label for it in `PRED` in `scripts/site.py`. Keep the ontology small; reuse before adding.
- Back-links: if the new node is a natural child or relative of an existing article, you may add its id to that article's `related` list. Don't otherwise rewrite existing articles, except through a refresh (step 2a).

## 5. Validate, build, commit, push
```bash
python3 scripts/kg.py build    # fix every reported problem, then re-run until OK
git add -A
git commit -m "Add <Title> (<domain>)"   # refresh: "Refresh <Title>[, <Title>…] (verified <old date> → <today>)"
git pull --rebase origin main && git push origin main
```
In a refresh commit body, list what changed per article, one line each (or `<id>: no content changes; re-verified`). If the push is rejected, pull --rebase, rebuild, and push again (once). Commit generated `graph/` files with the article.

## Don'ts
- Don't add more than one new article per run, refresh more articles than the suggested batch, combine an addition with refreshes, delete articles, or rewrite history.
- Don't write a new article on a subject that already has a node just because its node is stale; refresh it.
- Don't commit if validation fails; leave the repo clean.
- Don't cite a source you didn't open, or state a quota or price you didn't verify.
- Don't include account IDs, access keys, ARNs from real accounts, or step-by-step instructions for bypassing security controls.
