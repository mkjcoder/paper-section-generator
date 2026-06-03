# BibTeX

Use the user's required reference formats. Keep URL information available for search, but do not put an active `url` field inside BibTeX entries. BibTeX generation is retrieval -> verification -> parsing -> output, not LLM metadata generation.

## Metadata Source Contract

- Do not generate BibTeX core metadata from memory, search snippets, title-only matches, or LLM inference.
- Every final entry must have a DOI for verification and publisher-page-backed official-source verification.
- Obtain metadata in this authority order: Publisher Page, DOI-resolved metadata / DOI official BibTeX, then Crossref API/record as secondary support.
- Google Scholar, Semantic Scholar, OpenAlex, and search snippets may be used for discovery only, not as final metadata authority.
- When sources conflict, use the journal Publisher Page metadata as final authority and record conflicts and corrections in `reference_validation_report.md`.
- Exclude `UNVERIFIED` papers from final `references.bib`.
- Do not include active `doi` fields in final BibTeX entries. DOI is mandatory for verification, not for display in `references.bib`.
- Final `references.bib` must not be generated if any entry has an unresolved DOI mismatch or any missing/mismatched field other than `number`.
- A BibTeX entry is not valid merely because DOI and title are real. Every populated field must be verified independently before final output.

## Article Format

Use:

```bibtex
@article{KeyYear,
  author  = {...},
  title   = {...},
  journal = {...},
  year    = {...},
  volume  = {...},
  number  = {...},
  pages   = {...}
}
% url = {...}
```

Required active fields for `@article`:

```text
author, title, journal, year, volume, number, pages
```

## Conference Format

Use:

```bibtex
@inproceedings{KeyYear,
  author    = {...},
  title     = {...},
  booktitle = {...},
  year      = {...},
  pages     = {...}
}
% url = {...}
```

Required active fields for `@inproceedings`:

```text
author, title, booktitle, year, pages
```

## URL Handling

- Put paper URLs after each entry as a commented line: `% url = {...}`.
- Do not include active `url = {...}` inside the BibTeX entry.
- Final `references.bib` must use the canonical DOI URL only: `% url = {https://doi.org/<DOI>}`.
- Do not use ScienceDirect PII URLs, MDPI article URLs, publisher temporary URLs, search-result URLs, aggregator URLs, or repository URLs in final `references.bib`.
- The DOI-derived URL is for verification and user traceability only; it must not be treated as a manuscript citation.
- If DOI is missing, do not generate a URL. Mark the entry `EXCLUDED_PENDING_CONFIRMATION` or `UNVERIFIED` and block final `references.bib`.
- The DOI-derived URL must resolve to metadata matching the BibTeX title, author list, journal, year, volume, and pages/article number. If it does not, mark `DOI_MISMATCH` and block final `references.bib`.
- Optional but recommended: perform an HTTP status check on each `https://doi.org/<DOI>` URL. Record status code and redirect target in `reference_validation_report.md`.

## DOI Handling

- DOI is mandatory for verification of all final references and must be recorded in `candidate_literature.md` and `reference_validation_report.md`.
- Do not invent DOI values.
- Do not include DOI as an active BibTeX field in final `references.bib` unless the user explicitly changes the required format.
- Read the verified DOI from `reference_validation_report.md` or the verified intermediate metadata, generate the commented DOI URL from it, and keep all other BibTeX fields unchanged unless field-level verification requires a correction.

## Field Rules

| Field | Rule |
|---|---|
| `author` | Full official author list only; author count, order, and names must match the publisher article page or DOI-resolved metadata. Use `and` between all authors. Do not use `et al.` or truncated lists. |
| `title` | Match publisher/official page title; preserve necessary capitalization with braces. |
| `journal` | Use official full journal name, not abbreviation, when verified. |
| `booktitle` | Use official conference or proceedings name when verified. |
| `year` | Formal publication year from official metadata, not DOI registration year or Online First year unless it is the final publication year. |
| `volume` | Verified volume from the publisher article page. If missing, the paper is blocked from final `references.bib`. |
| `number` | Official issue number, not pages. During Stage 1, use `{}` only if no issue exists or cannot be verified and mark `ISSUE_MISSING` or `NEED_FINAL_NORMALIZATION`. During Stage 2, after authenticity verification and citation-order consistency verification are complete, normalize every empty `number = {}` to `number = {1}` for Elsevier/BibTeX compatibility and mark `NORMALIZED_IN_FINAL_OUTPUT`. Do not overwrite verified issue numbers. |
| `pages` | Official page range or article number from the publisher article page. Missing or mismatched pages/article number blocks final `references.bib`. |

## Two-Stage BibTeX Handling

BibTeX generation must follow the two-stage verification workflow in `verification.md`.

Stage 1 checks authenticity and field-level correctness. Do not complete, normalize, or guess fields merely to make BibTeX look complete. Unknown values may appear only in candidate/intermediate records as blank values or `NEEDS_VERIFICATION`. Final `references.bib` must not contain `{[信息缺失]}` or `NEEDS_VERIFICATION`. The only final-field exception is article `number`, which remains `{}` during Stage 1 when no issue exists or cannot be verified.

Stage 2 checks generated `references.bib` consistency after the manuscript citation order is final:

- Citation order consistency
- BibTeX syntax correctness
- Duplicated entries
- Missing mandatory fields
- DOI format and DOI-to-paper matching validation in `candidate_literature.md` and `reference_validation_report.md`
- URL format validation in commented `% url = {https://doi.org/<DOI>}` lines
- Rejection of non-DOI URLs in final `references.bib`
- Author formatting consistency
- Author completeness, count, and order verification
- Core metadata consistency against official source records
- Independent verification of every populated field
- Automatic correction of populated fields that conflict with authoritative metadata
- Article `number` normalization only when allowed

Stage 2 normalization must run only after Stage 1 authenticity verification and final manuscript citation-order verification are complete. It must normalize all empty article `number = {}` fields to `number = {1}` for template compatibility, and it must never overwrite verified issue numbers or other verified bibliographic values. The normalization must be visibly reported in `reference_validation_report.md`; an unreported normalization is a failed validation.

## BibTeX Keys

Use first-author surname + year:

```text
Xu2024
Khan2019
Traub2022
```

If duplicate, append letters:

```text
Xu2024a
Xu2024b
```

## Missing Fields

Never guess. Use blank values or `NEEDS_VERIFICATION` only for candidate/intermediate records. Final `references.bib` must exclude papers with missing or mismatched DOI, title, author, journal, year, volume, pages, or article number. Article `number` is the only field allowed to be missing under the two-stage rule:

```bibtex
number = {}
```

Use an empty value during Stage 1 if the issue number does not exist or cannot be verified.

```bibtex
number = {1}
```

Use this only during Stage 2 as a formatting compatibility normalization after authenticity verification and final consistency checks are complete. This normalization must never overwrite a verified issue number.

Final output must show the normalization result as:

```text
Issue status: NORMALIZED_IN_FINAL_OUTPUT
Original Stage 1 number: {}
Final BibTeX number: {1}
```

Do not:

- Omit required active fields.
- Include active `doi` fields in final BibTeX entries unless the user explicitly changes the format.
- Use incomplete author lists, author-count mismatches, wrong author order, or `et al.` in final BibTeX.
- Use guessed values.
- Use another paper's DOI or URL.
- Use ScienceDirect PII URLs, MDPI article URLs, publisher temporary URLs, aggregator URLs, search URLs, or repository URLs as final commented URL values.
- Treat aggregator-generated estimates as verified.
- Use title-only metadata to generate BibTeX.
- Use Google snippets or WebSearch summaries as final metadata.
- Fill `number` with `1` during authenticity verification.
- Leave empty article `number = {}` values unnormalized after Stage 2 when Elsevier/BibTeX compatibility is required.
- Invent bibliographic information solely to satisfy BibTeX completeness.
