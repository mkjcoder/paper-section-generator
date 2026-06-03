# Main Workflow

Follow this workflow as a strict sequential gate system. Do not work on multiple gates at the same time. Do not start a later gate while an earlier gate has missing information, unresolved conflicts, or pending confirmation.

## Gate Discipline

Each gate has:

- Input
- Allowed actions
- Required output
- Pass condition
- Block condition

If a block condition occurs, stop at that gate, write the blocker in the relevant report, and do not proceed. Missing or conflicting metadata must be completed or corrected before the next gate starts.

Forbidden workflow behavior:

- Do not draft manuscript sections while reference metadata is still unresolved.
- Do not generate BibTeX while any required metadata field is missing or mismatched.
- Do not create citation evidence after final prose as a cosmetic appendix; evidence cards must exist before cited prose is finalized.
- Do not mark an item `VERIFIED` while it still has pending publisher-page, DOI, author-order, volume, pages, or issue checks.
- Do not keep working "in parallel" on writing, BibTeX, and verification when a blocker appears.
- Do not silently discard a relevant-but-uncertain paper. Move it to pending replacement candidates and state what it could replace.

## Gate 0: Understand the Research Topic

Input:

- User title and abstract.

Allowed actions:

- Extract research object, problem, method, application scenario, key terms, synonyms, adjacent directions, likely Introduction themes, and likely Related Work categories.

Required output:

- Internal topic map and search plan.

Pass condition:

- Title and abstract are available.

Block condition:

- Missing title or abstract for a new literature task. Ask for the missing field and stop.

## Gate 1: Candidate Search and Screening

Use `search.md`. Default target is 20 deduplicated candidates unless the user specifies another count. Search in multiple rounds and vary queries.

Input:

- Topic map from Gate 0.

Allowed actions:

- WebSearch-based discovery.
- Initial DOI/publisher-page discovery.
- Initial relevance screening.
- Initial CAS/year/warning-journal screening.
- Candidate selection-rationale recording.

Required output:

- `candidate_literature.md` draft containing main candidates, backups, pending replacement candidates, search queries, and concrete selection rationale for each main candidate.

Pass condition:

- Every main candidate has a DOI or is clearly marked as backup/pending.
- Every main candidate has a concrete reason for selection and a potential manuscript claim it can support.
- Filter requirements are satisfied or failures are moved to backup.

Block condition:

- Too few verified candidates after strict filters.
- Candidate lacks DOI and cannot be moved to backup without dropping below required count.
- Candidate lacks a concrete supportable manuscript claim.

If blocked, run more search rounds or ask the user how to proceed. Do not write final prose.

For suitable but uncertain papers, do not treat them as blockers if enough verified main candidates remain. Move them to `Pending Replacement Candidates` with suggested section, paragraph, citation key/claim, why they may be a better fit, and what the user must confirm before use.

## Gate 2: Candidate Confirmation

Before user confirmation, output only the candidate literature list and selection guidance. Do not write final sections or final BibTeX.

Use this candidate format:

```text
检索到 X 篇主候选文献（去重后，按相关性排序）

[1] 标题：
作者：
年份：
来源：
DOI：
URL：
可信度：高 / 中 / 低
中科院分区：不限制 / 中科院一区 / 中科院二区 / [分区待人工核验]
分区年份与来源：
相关性说明：
可用于章节：Introduction / Related Work / Both
核验状态：
```

If filters exclude papers or metadata is uncertain, add:

```text
备选但待人工核验 / 不满足过滤条件的文献
```

For pending replacement candidates, use:

```text
待确认但可能适合替换的候选文献

[P1] 标题：
不确定字段/证据：
未进入最终正文原因：
建议替换章节：
建议替换段落：
建议替换的引用或 claim：
为什么可能更合适：
用户确认前需要检查：
论文主页/DOI：
状态：EXCLUDED_PENDING_CONFIRMATION
```

End by asking the user to confirm which papers to retain, remove, or add.

Pass condition:

- User confirms retained papers, or the user explicitly requests an automatic retained set.

Block condition:

- Candidate list is not confirmed.
- User asks to add/remove papers and the list has not been updated.

Do not generate Introduction, Related Work, final `manuscript.tex`, or final `references.bib` until the user confirms retained papers.

## Gate 3: Metadata Field Verification

After confirmation, verify every retained paper using `verification.md`. Do not skip any retained paper.

Only papers with a DOI and publisher-page-backed official-source verification may proceed to final writing and final BibTeX. If a confirmed paper has no DOI or cannot be verified from the publisher article page plus DOI-resolved metadata and Crossref cross-checks, move it to the excluded section of `candidate_literature.md` and mark it `UNVERIFIED` in `reference_validation_report.md`.

Final `references.bib` is an all-or-blocked artifact. If any retained reference has an unresolved DOI mismatch, title mismatch, author mismatch, journal mismatch, year mismatch, volume mismatch, or pages/article-number mismatch, do not generate final `references.bib`. Generate or update `reference_validation_report.md` with the blocker and ask for replacement references or manual correction from the publisher page.

Before final output, validate every populated BibTeX field independently. A real DOI and real title are not enough. If a populated author, journal, year, volume, issue, pages, article number, or publisher-derived value conflicts with authoritative metadata, correct it automatically from the publisher article page or DOI-resolved metadata. If it cannot be verified during validation, leave it blank or mark `NEEDS_VERIFICATION` in the report, and do not treat the entry as final-valid.

Required output:

- `reference_validation_report.md` with field-level evidence and statuses.

Pass condition:

- Every retained final reference is `VERIFIED`, or `PARTIALLY_VERIFIED` only because `number` requires documented Stage 2 normalization.
- DOI, title, author list/order/count, journal, year, volume, and pages/article number are verified against publisher article page and DOI-resolved metadata.
- All populated incorrect fields have been corrected and logged.

Block condition:

- DOI mismatch.
- Title mismatch beyond capitalization/punctuation normalization.
- Incomplete author list, wrong author order, or wrong author count.
- Journal, year, volume, pages, or article number missing or mismatched.
- Publisher page unavailable and no authoritative DOI-resolved metadata can verify the field.
- Any field is marked `NEEDS_VERIFICATION` other than `number` awaiting Stage 2 normalization.

If blocked, stop here. Do not create `manuscript.tex`, final `references.bib`, or final citation prose.

## Gate 4: Citation Evidence Construction

Use `citation-evidence.md`.

Input:

- Retained papers that passed Gate 3.

Allowed actions:

- Extract publisher abstract, DOI abstract, official full text, or official PDF evidence.
- Create paper-level evidence cards.
- Create claim-level support candidates.
- Decide which papers can support Introduction and Related Work claims.

Required output:

- `citation_evidence_report.md` draft with paper-level cards and planned claim support.

Pass condition:

- Every planned citation has a supportable claim.
- Every evidence card has publisher article page or DOI landing page.
- No retained claim is `UNSUPPORTED_CLAIM` or `EXCLUDED_PENDING_CONFIRMATION`.

Block condition:

- A paper is real but does not support the intended manuscript claim.
- Evidence source does not mention the method, dataset, result, or limitation the prose intends to state.
- A paper remains pending confirmation.

If blocked, remove the paper from final prose, rewrite the planned claim, or move the paper to pending replacement candidates with a suggested replacement target. Do not draft final prose until this gate passes.

## Gate 5: Manuscript Writing

Use `writing.md`.

Input:

- Gate 3 verified references.
- Gate 4 citation evidence cards.

Allowed actions:

- Write Introduction.
- Write Related Work.
- Assign citation numbers by first appearance.

Required output:

- `manuscript.tex` draft.
- Updated `citation_evidence_report.md` with claim-level comparison against actual written claims.

Pass condition:

- Every cited sentence or citation cluster maps to a `SUPPORTED` or narrowed `PARTIALLY_SUPPORTED_WITH_LIMITS` evidence card.
- Citation numbers follow first appearance order.
- No unverified paper appears in final prose.

Block condition:

- Any `UNSUPPORTED_CLAIM`.
- Any cited method/result/dataset/limitation not present in the evidence source.
- Any citation to a paper that failed Gate 3 or Gate 4.

If blocked, revise prose or return to Gate 4. Do not generate final BibTeX yet.

## Gate 6: BibTeX Generation

Use `bibtex.md`.

Input:

- Gate 3 verified metadata.
- Gate 5 final citation order.

Allowed actions:

- Generate `references.bib` in first citation order.
- Apply Stage 2 `number = {}` to `number = {1}` normalization only when allowed.

Required output:

- `references.bib`.
- Updated `reference_validation_report.md` with Stage 2 normalization notes.

Pass condition:

- `references.bib` contains no active `doi` fields.
- Every entry field matches Gate 3 verified metadata.
- `number` normalization is explicitly reported per entry.
- Entry order matches first citation appearance.

Block condition:

- Any field except `number` is blank, mismatched, guessed, or `NEEDS_VERIFICATION`.
- `number = {1}` appears without Stage 2 normalization record.
- BibTeX contains active DOI fields.

If blocked, return to Gate 3 or Gate 5 as needed. Do not claim final completion.

## Gate 7: Final Audit and Delivery

Use `quality-check.md`.

This is a mandatory file-generation step. Create the files on disk; do not only return their contents in the chat response.

Save:

```text
output/<YYYY-MM-DD_topic-slug>/
candidate_literature.md
manuscript.tex
references.bib
reference_validation_report.md
citation_evidence_report.md
```

`candidate_literature.md` must include title, search date, constraints, search queries, candidate list, user-retained papers, backup papers, missing-field notes, confidence labels, CAS partition verification when relevant, and literature-use allocation.

`manuscript.tex` must be a directly compilable Elsevier-style LaTeX file using `elsarticle` and numeric citation style:

```latex
\documentclass[preprint,12pt]{elsarticle}

\bibliographystyle{elsarticle-num}

\begin{document}

\begin{frontmatter}
\title{Paper Title}
\author{}
\address{}
\end{frontmatter}

\section{Introduction}

...

\section{Related Work}

...

\bibliography{references}

\end{document}
```

`references.bib` must include generation date, paper title, and entries ordered by first citation appearance. It must not contain active `doi` fields. DOI values are recorded in `candidate_literature.md` and `reference_validation_report.md` for verification only.

Each final BibTeX entry must include a commented canonical DOI URL generated from the verified DOI:

```bibtex
% url = {https://doi.org/<DOI>}
```

Do not use ScienceDirect PII URLs, MDPI article URLs, publisher temporary URLs, aggregator URLs, repository URLs, or search-result URLs as final BibTeX URL comments. If DOI is unavailable, do not generate a URL and do not include the paper in final `references.bib`.

`manuscript.tex` must not display author, affiliation, address, institution, or corresponding-author information unless the user explicitly provides and requests it. Use empty frontmatter author/address placeholders:

```latex
\author{}
\address{}
```

`reference_validation_report.md` must include every retained-or-excluded confirmed paper, official metadata sources consulted, source-priority decisions, conflict notes, automatic field corrections, author-count/order checks, issue normalization notes, and this minimum table:

```text
| Ref | DOI | Title | Author | Journal | Year | Volume | Issue | Pages | Status |
```

Status values are:

- `VERIFIED`: DOI, title, author, journal, year, volume, issue, and pages/article number pass official-source verification.
- `PARTIALLY_VERIFIED`: DOI/title/author and P1 fields pass, but `number` is missing or requires final normalization. These papers may enter final `references.bib` only when the sole unresolved field is `number` and final output marks `NORMALIZED_IN_FINAL_OUTPUT`.
- `UNVERIFIED`: DOI, title, author, journal, year, or volume is missing, inconsistent, or not confirmed by official sources. These papers must not enter final `references.bib`.

`citation_evidence_report.md` must include every cited paper and every Introduction/Related Work citation claim. It must record paper-selection rationale, source URLs, supported manuscript claims, evidence source type, and claim-support status. Use `references/citation-evidence.md`.

If a paper is retained only pending confirmation, or if a cited claim lacks direct support, keep it out of final prose and mark it in `citation_evidence_report.md` as `EXCLUDED_PENDING_CONFIRMATION` or `UNSUPPORTED_CLAIM`. For every `EXCLUDED_PENDING_CONFIRMATION` paper, include the suggested replacement section, paragraph, and citation claim.

Before the final response:

- Confirm `candidate_literature.md` exists.
- Confirm `manuscript.tex` exists.
- Confirm `references.bib` exists.
- Confirm `reference_validation_report.md` exists.
- Confirm `citation_evidence_report.md` exists.
- Confirm `manuscript.tex` contains `\documentclass[preprint,12pt]{elsarticle}`.
- Confirm `manuscript.tex` contains `\bibliographystyle{elsarticle-num}` and `\bibliography{references}`.
- If a LaTeX compiler is available, compile `manuscript.tex` to PDF and report the result. If no compiler is available, report that compilation was not run and why.

If any required file is missing, do not claim the task is complete.
