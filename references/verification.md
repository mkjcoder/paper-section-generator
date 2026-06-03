# Verification

Verification is the safety core of this skill. The final chapter references must be real, reliable, DOI-backed, and verified from official or publisher sources. Core metadata accuracy is more important than formatting completeness.

## LLM Metadata Boundary

LLMs may help recommend papers, judge relevance, extract abstracts, draft cited prose, check field completeness, and compare consistency between source records. LLMs must not infer, invent, repair, or directly generate core metadata values.

Forbidden LLM-generated core metadata:

```text
doi
title
author
journal
year
volume
number / issue
pages / article number
```

These values must come from official sources only. Do not use Google snippets, WebSearch summaries, or title-based guesses as final metadata.

## Candidate-Level Verification

Before user confirmation, verify enough to avoid suspicious papers:

- Title
- Author names as available
- Year
- Venue
- DOI and stable URL
- Relevance
- CAS partition when required
- Year filter when required

## Deep Verification After User Confirmation

For every retained paper, verify all core fields from the publisher article page, then cross-check through DOI-resolved metadata, Crossref, official venue, or official PDF sources:

```text
title
full authors
year
journal / conference / publisher
DOI
URL
abstract or research content
volume
number / issue
pages or article number
CAS partition, partition year, and source when required
```

Publisher/original article pages and DOI-resolved metadata are authoritative and mandatory for final references. Crossref is a secondary source. Google Scholar, Semantic Scholar, OpenAlex, search snippets, and other aggregators may help locate papers, but must not be used as final authority for bibliographic fields.

## Official Metadata Source Priority

Each retained paper must first obtain a DOI. Then collect metadata in this authority order:

1. Publisher article page.
2. DOI-resolved metadata or DOI official BibTeX, such as `https://doi.org/<doi>` content negotiation when available.
3. Crossref API or Crossref DOI record as secondary support.

When these sources disagree, use the journal publisher article page as the final authority for title, author list, journal, formal publication year, volume, issue, and pages/article number. Record every conflict field in `reference_validation_report.md`. Do not use Google Scholar, Semantic Scholar, OpenAlex, or search snippets to decide final metadata.

Do not generate BibTeX from title alone. Do not accept a reference into the final BibTeX without DOI verification.

## Field Priority and Exactness Rules

| Priority | Fields | Rule |
|---|---|---|
| P0 | DOI, Title, Author | Must exist and exactly match official metadata. Author order and count must match. |
| P1 | Journal, Year, Volume | Must match official publication metadata. Year is formal publication year, not DOI registration year or Online First year unless that is the final official publication year. |
| P2 | Number(Issue), Pages / Article Number | Pages/article number must match official publisher information. Number is the only field allowed to be absent in final output, and only with explicit normalization reporting. |
| P3 | URL, Publisher | Formatting and traceability fields; keep accurate but never prioritize them over core metadata. |

Core field requirements:

- DOI must exist.
- Title must exactly match the official title; do not abbreviate, trim, rewrite, or add subtitles.
- Author must exactly match the official author list, order, and count.
- Author lists must be complete. Do not use shortened author lists, first-author-only records, `et al.`, incomplete initials, or inferred names in final `references.bib`.
- Journal must use the official journal name.
- Year must use the formal publication year.
- Volume must match the publisher page.
- Number represents the official issue, not pages or article number.
- Pages/article number must match the official value.
- DOI matching must verify that the DOI resolves to the same title and author list as the publisher article page. If DOI metadata points to a different title, author list, journal, year, volume, or pages/article number, treat it as `DOI_MISMATCH` and exclude it from final BibTeX.
- The final commented URL must be generated only from the verified DOI as `https://doi.org/<DOI>`. Non-DOI URLs are not valid final BibTeX URLs.
- In final references, missing or uncertain fields are not allowed except `number` under the explicit Stage 2 normalization rule.

## Field-Level Verification

For every generated BibTeX entry, verify each populated field before final output. Existence of a real DOI and a real title is insufficient.

Mandatory field checks:

1. DOI validity and DOI resolution.
2. Exact title match, allowing only capitalization and punctuation normalization.
3. Complete author list, author count, and author order verification.
4. Journal or conference name verification.
5. Publication year verification.
6. Volume verification.
7. Issue/number verification when available.
8. Page range or article number verification.
9. Publisher metadata consistency.

Rules:

- Every populated field must be independently checked against the publisher article page and DOI-resolved metadata.
- If a populated field conflicts with authoritative metadata, correct it automatically from the publisher article page or DOI-resolved metadata and record the correction in `reference_validation_report.md`.
- If a field cannot be verified during verification, leave it blank or mark `NEEDS_VERIFICATION` in intermediate records and the validation report. Do not invent or guess.
- Missing fields are acceptable during verification, but incorrect populated fields are never acceptable.
- Final `references.bib` may be generated only after all populated final fields pass field-level verification and all required final fields are available, except `number` when handled by the explicit normalization rule.

## Two-Stage Reference Verification Workflow

All references must pass two independent verification stages before final BibTeX generation.

### Stage 1: Reference Authenticity Verification

The purpose of Stage 1 is to verify whether the bibliographic information itself is real and accurate. The goal is authenticity, not formatting completeness.

Required fields to verify:

- DOI
- Title
- Authors
- Journal / conference
- Publication year
- Volume
- Issue number
- Pages or article number
- URL

Verification priority:

1. Publisher page
2. DOI-resolved metadata / DOI official BibTeX
3. Crossref API / Crossref DOI record

Rules:

- Missing fields must never be fabricated.
- Unknown values may be left blank or marked `NEEDS_VERIFICATION` only in candidate/intermediate records and validation reports. Final `references.bib` must not contain `[信息缺失]` or `NEEDS_VERIFICATION`.
- Fields that do not exist in the original publication must remain empty during Stage 1.
- No automatic field completion is allowed during Stage 1.
- The `number` field must participate in authenticity verification as the official issue field.
- The `number` field represents issue information only. Do not use page numbers or article numbers to fill `number`.
- If the original source provides an issue number, record the verified value.
- If the publication clearly has no issue number, leave `number` empty during Stage 1 and mark `ISSUE_MISSING`.
- If the issue number cannot be verified, leave `number` empty during Stage 1 and mark `NEED_FINAL_NORMALIZATION`.
- Do not set `number = {1}` during Stage 1.

### Stage 2: Bibliographic Consistency Verification

After manuscript generation and citation-order verification are complete, perform a second verification pass on the generated `references.bib` file.

Checks include:

- Citation order consistency
- BibTeX syntax correctness
- Duplicated entries
- Missing mandatory fields; any missing final field except `number` blocks final `references.bib`
- DOI format validation in `candidate_literature.md`
- URL format validation in commented `% url = {...}` lines
- Author formatting consistency
- DOI/title/author/journal/year/volume/number/pages verification against official metadata
- DOI-derived URL generation and validation
- HTTP status and redirect-target check for DOI URL when available
- Author count/order verification against publisher and DOI-resolved metadata
- Automatic correction log for every populated field that was changed
- `number = {1}` normalization reporting when applied

Only during Stage 2 may formatting normalization rules be applied. Stage 2 must never invent bibliographic facts.

### Number Field Handling

During Stage 1:

```bibtex
number = {...}
```

Use the verified issue number only when the original source provides it.

```bibtex
number = {}
```

Use an empty value when the publication clearly has no issue number or when the issue number cannot be verified. Mark `ISSUE_MISSING` or `NEED_FINAL_NORMALIZATION` in the validation report.

Only after all references pass Stage 1 and the final manuscript citation-order consistency check is complete may this formatting normalization be applied during Stage 2:

```bibtex
number = {1}
```

This replacement is a formatting compatibility operation, not bibliographic verification. It must be applied to every empty article `number = {}` field during Stage 2, and it must never overwrite a verified issue number.

If Stage 2 changes an empty issue to `number = {1}`, mark `NORMALIZED_IN_FINAL_OUTPUT` in `reference_validation_report.md` and record both the Stage 1 value and final BibTeX value. If this normalization is not explicitly shown, final validation fails.

## Validation Status Rules

- `VERIFIED`: DOI, title, author, journal, year, volume, issue, and pages/article number all pass publisher-page-backed official-source verification.
- `PARTIALLY_VERIFIED`: P0 and P1 fields plus pages/article number pass, but `number` is missing, `ISSUE_MISSING`, or `NEED_FINAL_NORMALIZATION`. `PARTIALLY_VERIFIED` may enter final `references.bib` only if the sole unresolved field is `number` and Stage 2 records `NORMALIZED_IN_FINAL_OUTPUT`.
- `UNVERIFIED`: DOI, title, author, journal, year, or volume is missing, conflicts with official metadata, or cannot be verified. `UNVERIFIED` references must not enter final `references.bib`.

If DOI, title, author, journal, year, volume, or pages/article number is missing or mismatched, stop final BibTeX generation. If a populated field is wrong, correct it from authoritative metadata before validation. Do not output a partial or best-effort `references.bib`.

After BibTeX generation, automatically verify:

```text
DOI
Title
Author
Journal
Year
Volume
Number(Issue)
Pages / Article Number
```

### Highest Priority Rule

Authenticity verification always has higher priority than BibTeX completeness. A missing field is preferable to an incorrect field. Never invent bibliographic information solely to satisfy BibTeX formatting requirements.

## Abstract Source Verification

For writing cited-paper descriptions, verify and record the best available abstract source in this priority order:

1. Publisher abstract
2. DOI page abstract
3. Author-provided abstract from arXiv or an institutional repository
4. Database abstract from Crossref, PubMed, IEEE, Springer, Elsevier, Wiley, ACM, Semantic Scholar, or OpenAlex
5. Full text, only when legally and clearly accessible

If only title, metadata, or domain context is available, the paper may be listed as a candidate, but do not write method, dataset, result, or limitation claims about it in final sections.

## DOI and URL Verification

For metadata verification, follow the official metadata priority above.

For final BibTeX URL traceability:

1. DOI must exist and be verified.
2. Generate the URL as `https://doi.org/<DOI>`.
3. Do not use publisher PII URLs, ScienceDirect URLs, MDPI article URLs, official venue URLs, PDF URLs, Crossref pages, aggregator pages, search URLs, or repository URLs as final BibTeX URL comments.
4. Verify that the DOI landing page or DOI-resolved metadata corresponds to the same paper as the BibTeX entry.
5. If HTTP checking is available, record HTTP status code and redirect target in `reference_validation_report.md`.

The URL selected for `% url = {...}` must not replace DOI-based metadata verification. It is generated from verified DOI only.

## Missing or Conflicting Fields

Never guess. If a candidate/intermediate field cannot be verified, leave it blank or mark it as:

```text
NEEDS_VERIFICATION
```

If fields conflict across sources, prefer the publisher article page and describe the conflict in `candidate_literature.md`.

Also record conflicts in `reference_validation_report.md`, including field name, source values, selected final value, automatic correction applied, and reason. When Crossref, Publisher Page, and DOI-resolved metadata disagree, Publisher Page data is the final authority. Any DOI mismatch must be recorded as `DOI_MISMATCH` and blocks final BibTeX output.

## Verification Completion Report

After deep verification, report:

```text
深度核验完成：X 篇文献达到最终 BibTeX 准入标准；Y 篇存在 DOI/标题/作者/期刊/年份/卷号/页码或 Article Number 错配或缺失，已阻断进入最终 references.bib。
```

Also write `reference_validation_report.md` with at least:

```text
| Ref | DOI | Title | Author | Journal | Year | Volume | Issue | Pages | Status |
```

Also include URL checks:

```text
| Ref | DOI URL | HTTP Status | Redirect Target | Metadata Match | URL Status |
```

For any `number` normalization, also include:

```text
Issue status: NORMALIZED_IN_FINAL_OUTPUT
Original Stage 1 number: {}
Final BibTeX number: {1}
```
