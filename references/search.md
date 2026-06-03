# Search

Use WebSearch as the candidate-discovery entry point. After a candidate DOI or official page is found, official metadata sources may be used for verification according to `verification.md`.

## Query Generation

Generate 4-6 query groups from title phrases, abstract method terms, application terms, research object terms, synonyms, adjacent-domain terms, and field-specific phrases.

Example patterns:

```text
"core phrase" method application journal
"method synonym" "research object" review
"problem term" graph/network/optimization robustness
```

## Preferred Sources

Prefer WebSearch results that lead to publisher pages, DOI landing pages, official journal/conference pages, official PDFs, and major academic publishers or venues.

Aggregator pages such as Google Scholar-visible results, Semantic Scholar, OpenAlex, Crossref-visible pages, and arXiv pages can help discovery, but they are not final metadata authority. Publisher article pages and DOI-resolved metadata must be used for final field verification. Search snippets and WebSearch summaries are never final metadata sources.

Every candidate must prioritize DOI discovery. Candidate records without DOI may be shown only as backup or pending items; they are not eligible for final manuscript use unless DOI and official-source metadata are later verified.

## Search Rounds

| Target candidates | Suggested WebSearch rounds |
|---|---|
| 1-10 | 2-3 |
| 11-20 | 4-6 |
| 21-30 | 6-8 |
| >30 | Batch the work and warn the user about screening cost |

## Screening

Keep papers that are highly relevant, useful for Introduction or Related Work, recent or foundational as appropriate, published in reputable venues, supported by DOI and stable official URL, and verifiable against user filters.

Remove weakly related papers, nonacademic pages, papers with unverifiable DOI/title/author/year, duplicates, preprints duplicated by formal publications, and any paper that fails or cannot verify required CAS/year filters.

For every candidate retained in the main list, record a concrete selection rationale. Do not keep a paper only because its title contains similar keywords.

Required rationale fields:

```text
Why selected:
Specific match to the user's topic:
Potential manuscript claim supported:
Evidence source to inspect later:
Why alternatives were weaker or less direct:
```

If no concrete manuscript claim can be identified for a candidate, move it to backup instead of using it to pad the list.

## Pending Replacement Candidates

If a paper appears suitable but cannot be fully verified, do not use it in final prose or final BibTeX. Keep it in a pending replacement section for user review.

Required fields:

```text
Pending candidate:
Uncertain field(s):
Reason not used in final writing:
Suggested replacement target section: Introduction / Related Work
Suggested replacement target paragraph:
Suggested replacement target citation key or claim:
Why it may be a better fit:
What the user must confirm before use:
Publisher/DOI page to check:
Status: EXCLUDED_PENDING_CONFIRMATION
```

The candidate introduction must tell the user exactly where the paper could be substituted, so the user can manually adjust the manuscript if they decide the paper is suitable.

## Deduplication

Deduplicate by exact DOI, exact title, highly similar title with matching authors/year, and preprint/formal publication pairs. Keep the formal publication when available.

## Confidence Labels

- `高`: DOI or publisher page found; title/authors/year and core fields verified.
- `中`: Stable source found, but DOI/pages/volume/issue or other fields are incomplete.
- `低`: Single source only, conflicting fields, or major missing metadata. Do not use in final prose unless user confirms.

A `高` confidence label requires both metadata traceability and a plausible evidence path for the manuscript claim. Metadata confidence alone is not enough.

## CAS Partition Filtering

Apply only when requested.

| User wording | Execution |
|---|---|
| 中科院一区 | Keep only verified CAS 1 papers |
| 中科院二区 | Keep only verified CAS 2 papers |
| 中科院一区或二区 | Keep only verified CAS 1 or 2 papers |
| 中科院二区以上 / 二三区以上中的“二区以上” | Treat as CAS rank <= 2 |
| 中科院三区以上 | Treat as CAS rank <= 3 |
| 不限制期刊分区 | Do not filter by CAS partition |

Verification query examples:

```text
"Journal Name" 中科院分区
"Journal Name" 最新 中科院 SCI 分区
"Journal Name" CAS journal ranking
"Journal Name" LeThub 最新期刊分区
"Journal Name" LeThub 预警期刊
"Journal Name" LetPub 中科院分区
"Journal Name" MedSci 中科院分区
```

Record CAS partition, partition year, and source. Prefer the latest annual LeThub journal partition table when available, then use other verifiable CAS partition sources as support. If only JCR Q1/Q2 is found, mark CAS as `[分区待人工核验]` and do not treat it as satisfying CAS filtering.

### Warning Journal Exclusion

When CAS partition filtering is active, also check whether the journal appears in any prior-year LeThub warning journal list or warning-risk record.

Rules:

- If the journal has appeared as a warning journal in any prior year, do not include papers from that journal in the main candidate list.
- Move such papers to the backup section even when the article itself is highly relevant and the current partition appears acceptable.
- Mark the reason as `往年预警期刊 / warning journal history`.
- Record the warning year and source URL when available.
- If warning status cannot be verified, mark `预警状态：[待人工核验]`; do not treat it as a verified warning-free journal when the user requires strict CAS filtering.
- Warning exclusion is applied before final candidate confirmation and before Stage 1 reference authenticity verification.

## Year Filtering

When the user gives a year range, parse it explicitly:

| User wording | Execution |
|---|---|
| 最近5年 / 近5年 | Current year - 5 through current year |
| 最近10年 / 近10年 | Current year - 10 through current year |
| 2020年以来 / 2020至今 | year >= 2020 |
| 2018-2024 / 2018到2024 | 2018 through 2024 |
| 只要2022年以后的 | year >= 2022 |

Year and CAS filters are independent and cumulative. Main candidates must satisfy both when both are specified.
