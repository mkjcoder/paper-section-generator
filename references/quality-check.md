# Quality Check

Before final delivery, check:

```text
[ ] Gate 0 topic understanding passed before candidate search.
[ ] Gate 1 candidate search/screening passed before candidate confirmation.
[ ] Gate 2 candidate confirmation passed before metadata verification.
[ ] Gate 3 metadata field verification passed before citation evidence construction.
[ ] Gate 4 citation evidence construction passed before manuscript writing.
[ ] Gate 5 manuscript writing and claim-evidence comparison passed before BibTeX generation.
[ ] Gate 6 BibTeX generation passed before final audit.
[ ] No later gate was started while an earlier gate had `NEEDS_VERIFICATION`, `DOI_MISMATCH`, `UNSUPPORTED_CLAIM`, `EXCLUDED_PENDING_CONFIRMATION`, missing metadata, or unresolved user confirmation.
[ ] Any gate blocker was recorded in the corresponding report and downstream output was not generated from blocked data.
[ ] WebSearch was used as the candidate-discovery entry point.
[ ] Scholarly APIs or metadata services were used only after DOI/official-page discovery and only for verification/BibTeX parsing.
[ ] LLM did not generate DOI, title, author, journal, year, volume, issue, pages, or article number metadata.
[ ] Every final reference has a verified DOI in `candidate_literature.md` and `reference_validation_report.md`.
[ ] Final `references.bib` contains no active `doi` fields.
[ ] Every final commented BibTeX URL is generated as `% url = {https://doi.org/<DOI>}` from the verified DOI.
[ ] Final `references.bib` contains no ScienceDirect PII URL, MDPI article URL, publisher temporary URL, aggregator URL, repository URL, or search-result URL.
[ ] DOI-derived URLs were checked for DOI-to-paper metadata consistency against BibTeX fields before writing final prose or final BibTeX.
[ ] If HTTP checks were available, DOI URL status codes and redirect targets were recorded in `reference_validation_report.md`.
[ ] DOI metadata resolves to the same title, authors, journal, year, volume, and pages/article number as the publisher article page.
[ ] Any DOI mismatch is marked `DOI_MISMATCH` and blocks final `references.bib`.
[ ] Any no-DOI paper was excluded from final `references.bib` unless the user explicitly requested a nonfinal candidate note.
[ ] Final metadata authority used Publisher Page and DOI-resolved metadata; Crossref was used only as secondary support.
[ ] Google Scholar, Semantic Scholar, OpenAlex, and search snippets were used only for discovery, not as final metadata authority.
[ ] No Google snippet, WebSearch summary, or LLM-inferred value was used as final metadata.
[ ] Publisher Page was used as final authority whenever official sources conflicted.
[ ] Conflicting Crossref/Publisher/DOI-resolved metadata fields were recorded in `reference_validation_report.md`.
[ ] Candidate count defaults to 20 unless the user specified another count.
[ ] Multiple query groups were generated from the title and abstract.
[ ] Duplicates, weakly related papers, and suspicious papers were removed from the main list.
[ ] Every candidate has a confidence label and enough metadata for screening.
[ ] CAS filtering uses the latest annual LeThub partition table when available.
[ ] Journals with any prior-year LeThub warning history are excluded from the main candidate list and placed in backup.
[ ] Candidate list was shown to the user, and final writing happened only after user confirmation.
[ ] Every retained final paper was deeply verified against the publisher article page and cross-checked against DOI-resolved metadata and Crossref where available.
[ ] Required fields were not invented; unverifiable fields are blank or marked `NEEDS_VERIFICATION` only in candidate/intermediate records and validation reports.
[ ] Final `references.bib` contains no `[信息缺失]` or `NEEDS_VERIFICATION` values.
[ ] Final `references.bib` was not generated if any field other than `number` remained missing or mismatched.
[ ] Stage 1 reference authenticity verification was completed before final BibTeX generation.
[ ] Stage 1 did not auto-complete missing fields or normalize article `number` values.
[ ] Stage 1 verified DOI, title, author, journal, year, volume, issue, and pages/article number.
[ ] Stage 1 independently verified every populated BibTeX field, not only DOI/title existence.
[ ] Any populated field conflicting with authoritative metadata was automatically corrected and logged in `reference_validation_report.md`.
[ ] Stage 2 bibliographic consistency verification was completed after Stage 1 and after manuscript citation order was final.
[ ] Stage 2 checked citation order, BibTeX syntax, duplicates, mandatory fields, DOI/URL format, and author formatting.
[ ] Stage 2 rechecked DOI, title, author, journal, year, volume, issue, and pages/article number against official metadata.
[ ] CAS and year filters, when requested, are strictly enforced in the main list; failures are in backup.
[ ] Introduction and Related Work use only confirmed and verified references.
[ ] `citation_evidence_report.md` was created before final delivery.
[ ] Every cited Introduction and Related Work claim is mapped to a citation evidence card.
[ ] Every citation evidence card includes paper-selection rationale, publisher article page, DOI landing page when available, evidence source type, supported manuscript claim, and support status.
[ ] No pending-confirmation paper appears in final Introduction, Related Work, or `references.bib`.
[ ] Any paper excluded because of pending confirmation is recorded as `EXCLUDED_PENDING_CONFIRMATION`.
[ ] Every `EXCLUDED_PENDING_CONFIRMATION` paper includes suggested replacement section, paragraph, citation key or claim, reason it may fit, and what the user must confirm.
[ ] Pending replacement candidates are listed for user adjustment but excluded from final prose and final BibTeX.
[ ] Any unsupported citation claim is marked `UNSUPPORTED_CLAIM` and removed or rewritten before final manuscript delivery.
[ ] Each cited-paper description is grounded in an evidence card from the best available abstract source.
[ ] Method, dataset, result, and limitation claims are explicitly supported by the cited paper's abstract or accessible full text.
[ ] If full text is unavailable, no citation paragraph adds methods, experiments, datasets, baselines, numerical improvements, or limitations beyond the abstract.
[ ] Related Work is written as continuous paragraphs without subsection headings or topic labels.
[ ] Each Related Work paragraph starts with a field-specific transition sentence tied to its research theme.
[ ] Related Work paragraphs avoid generic template openings and keep paragraph logic coherent.
[ ] Related Work prioritizes papers not already used in Introduction.
[ ] Introduction/Related Work overlap is within the default 30% limit unless justified.
[ ] Method/model/framework/experiment references use opening citations; background/concept references use trailing citations.
[ ] Related Work citation descriptions do not add experiments, results, methods, datasets, or limitations beyond the abstract or accessible verified full text.
[ ] Citation numbers follow first appearance order with no skips or disorder.
[ ] `references.bib` order matches final manuscript citation order.
[ ] Article BibTeX entries contain author, title, journal, year, volume, number, pages.
[ ] Conference BibTeX entries contain author, title, booktitle, year, pages.
[ ] Author lists are complete; author count, author order, and author names match publisher/DOI-resolved metadata.
[ ] Final BibTeX contains no `et al.`, truncated author lists, first-author-only records, or incomplete author metadata.
[ ] Final BibTeX entries do not contain active `doi` fields.
[ ] URL is outside the entry as `% url = {...}`.
[ ] Article `number` uses a verified issue number when available.
[ ] Unverified or nonexistent article `number` remains `{}` during Stage 1 and is marked `ISSUE_MISSING` or `NEED_FINAL_NORMALIZATION`.
[ ] All empty article `number = {}` fields are normalized to `number = {1}` during Stage 2 only.
[ ] `number = {1}` normalization never overwrites a verified issue number.
[ ] Any final `number = {1}` normalization is marked `NORMALIZED_IN_FINAL_OUTPUT` in `reference_validation_report.md`.
[ ] `reference_validation_report.md` shows `Original Stage 1 number: {}` and `Final BibTeX number: {1}` for every normalized issue.
[ ] `number` is the only field allowed to require final normalization.
[ ] Page numbers or article numbers were never used to populate `number`.
[ ] BibTeX contains no fabricated fields.
[ ] `UNVERIFIED` references are excluded from final `references.bib`.
[ ] `PARTIALLY_VERIFIED` references enter final `references.bib` only when the sole unresolved field is `number` and the report marks `NORMALIZED_IN_FINAL_OUTPUT`.
[ ] `reference_validation_report.md` contains `VERIFIED`, `PARTIALLY_VERIFIED`, or `UNVERIFIED` status for each confirmed paper.
[ ] `reference_validation_report.md` includes at least `| Ref | DOI | Title | Author | Journal | Year | Volume | Issue | Pages | Status |`.
[ ] `candidate_literature.md`, `manuscript.tex`, `references.bib`, `reference_validation_report.md`, and `citation_evidence_report.md` were physically created in the output directory.
[ ] Final response does not claim completion unless all five required files exist.
[ ] `manuscript.tex` uses Elsevier `elsarticle` style and `elsarticle-num` bibliography style.
[ ] `manuscript.tex` does not display author, institution, affiliation, address, or corresponding-author information unless explicitly requested by the user.
[ ] `manuscript.tex` uses empty frontmatter placeholders `\author{}` and `\address{}` when author information is not requested.
[ ] `manuscript.tex` can compile with `references.bib` to produce a PDF.
[ ] Output files are saved under `output/<YYYY-MM-DD_topic-slug>/`.
```

Final response should briefly state:

- Candidate count or final retained count.
- Whether deep verification found missing fields.
- Output directory and files created.
- Any residual manual checks needed.
