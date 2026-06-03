---
name: paper-section-generator
description: 根据论文标题和摘要，使用 WebSearch 检索真实、可核验的学术文献，先生成候选文献列表供用户确认，再基于出版商/官网页面深度核验，最后生成 Elsevier 风格 LaTeX 手稿 manuscript.tex、candidate_literature.md 和 references.bib。适用于写论文引言、相关工作、文献综述、候选参考文献和 BibTeX；支持年份范围、中科院期刊分区、预警期刊排除、文献数量、英文/中文正文和引用顺序控制。
---

# paper-section-generator

Use this skill as a strict literature-grounded paper-section workflow. The central contract is: search real papers through WebSearch, verify them from official/publisher sources, ask the user to confirm candidate papers, then write with only confirmed and deeply verified references. BibTeX generation is a retrieval -> verification -> parsing -> output process, never an LLM metadata-generation process.

This skill must run as a sequential gate workflow. Do not search, verify, write, and generate BibTeX in parallel. If any gate finds missing metadata, conflicting metadata, pending confirmation, unsupported citation evidence, or a BibTeX formatting blocker, stop at that gate, fix or report the blocker, and do not proceed to later gates.

## Command Understanding Protocol

1. Parse the user's command and identify the task mode before acting.
2. Extract title, abstract, domain, target journal, candidate count, year range, CAS partition requirement, writing language, Introduction style, Related Work categories, and output directory.
3. If title or abstract is missing for a new literature task, ask for the missing field and stop.
4. Normalize constraints:
   - Missing candidate count: use 20.
   - Missing year range: no hard year filter, but prefer recent papers and include classics when useful.
   - "Recent 5 years" or similar: resolve against the current year.
   - "中科院二区以上" / "CAS Q2 or above": treat as CAS rank <= 2.
   - CAS partition and JCR quartile are different. Never substitute JCR Q1/Q2 for CAS partition.
5. Load only the reference files needed for the detected mode.
6. Do not generate final prose or final BibTeX until the user confirms the candidate literature list.

## Task Mode Detection

| User intent | Mode | Load references | Required behavior |
|---|---|---|---|
| Provides title/abstract and asks for papers, references, or candidate literature | Candidate Search | `references/main-workflow.md`, `references/search.md` | Search with WebSearch, screen candidates, output candidate list, wait for confirmation. |
| Provides title/abstract and asks directly for Introduction, Related Work, or manuscript | Full Pipeline | `references/main-workflow.md`, `references/search.md`, then later verification/writing/BibTeX files | Still run candidate search first; final writing waits for confirmation. |
| Confirms selected candidate papers | Deep Verification + Writing | `references/verification.md`, `references/writing.md`, `references/bibtex.md`, `references/citation-evidence.md`, `references/quality-check.md` | Deep-verify confirmed papers, build citation evidence cards, write sections, create BibTeX, run checks. |
| Asks to revise sections using already confirmed papers | Revision | `references/writing.md`, `references/quality-check.md` | Reuse confirmed verified references only; preserve citation and BibTeX consistency. |
| Asks to check references, BibTeX, citation order, citation evidence, or missing metadata | Validation | `references/verification.md`, `references/bibtex.md`, `references/citation-evidence.md`, `references/quality-check.md` | Audit truthfulness, fields, citation evidence, comments, and citation order. |
| Specifies year or CAS filters | Filtered Search | `references/search.md`, `references/verification.md` | Enforce filters strictly in the main candidate list; move failures/unknowns to backup. |

## Non-Negotiable Rules

1. Use WebSearch as the discovery entry point for candidate search. After a candidate DOI or official page is found, DOI-resolved metadata, publisher metadata, and Crossref may be queried only for verification and BibTeX parsing. Google Scholar, Semantic Scholar, OpenAlex, and search snippets may be used only for discovery, not as final metadata authority.
2. Do not fabricate papers, DOI values, URLs, authors, years, journal names, volume, issue, pages, abstracts, citation counts, CAS partitions, or research claims.
3. LLMs may recommend papers, judge relevance, extract abstracts, generate cited prose, check BibTeX field completeness, and compare metadata consistency. LLMs must not infer or generate core bibliographic metadata: DOI, title, authors, journal, year, volume, issue, pages, or article number.
4. A paper may become a final chapter reference only if it is real, found through WebSearch, confirmed by the user, has a DOI, and is deeply verified against publisher, DOI, official venue, or official PDF pages.
5. Any paper without a DOI is excluded from the final manuscript and final `references.bib` by default.
6. Any paper that cannot be verified through official sources is excluded from the final manuscript and final `references.bib` by default.
7. Metadata authority is: DOI-resolved metadata and the journal publisher article page as authoritative sources, with Crossref as secondary support. The final authority for bibliographic fields is the journal publisher article page. When sources conflict, prefer the Publisher Page and record conflicts in `reference_validation_report.md`.
8. Never use Google search snippets, WebSearch summaries, aggregator summaries, or LLM-inferred values as final metadata.
9. Candidate literature must be shown to the user and explicitly confirmed before writing Introduction, Related Work, `manuscript.tex`, or final `references.bib`.
10. Aggregators such as Google Scholar, Semantic Scholar, OpenAlex, TRID, OUCI, RePEc, and similar pages are discovery clues only. Do not use them as final authority for any BibTeX metadata field.
11. If the user specifies CAS partition or year filters, the main candidate list must satisfy them strictly. Unverified or nonmatching papers go to backup only.
12. If strict filters leave too few main candidates, run more WebSearch rounds instead of padding with weak, unverified, out-of-range, or wrong-partition papers.
13. Low-confidence papers must not be used in final prose unless the user explicitly confirms retaining them and they pass DOI/official-source verification.
14. Citation numbers in `manuscript.tex`, `candidate_literature.md`, and `references.bib` must follow first appearance order.
15. BibTeX must follow the user-required format in `references/bibtex.md`: no active `url` field inside entries; URL appears after the entry as a commented `% url = {...}` line for search convenience.
16. Final `references.bib` must contain only fully correct entries whose title, author, journal, year, volume, issue handling, and pages/article number match the publisher article page. If any final-reference field other than `number` cannot be verified from the publisher page or official metadata, do not generate the final `references.bib`; report the blocker instead.
17. A BibTeX entry is not valid merely because its DOI and title are real. Every populated field must be checked independently against authoritative metadata before final output.
18. Incorrect populated fields must be corrected automatically from authoritative metadata. If a populated field cannot be verified during verification, leave it blank or mark `NEEDS_VERIFICATION`; never guess.
19. Author metadata must be complete: final author count, author order, and author names must match the publisher article page or DOI-resolved metadata. Truncated author lists such as "et al." are forbidden in final `references.bib`.
20. Every cited claim in Introduction and Related Work must be traceable to a citation evidence card based on the publisher abstract, DOI page, official full text, or other legally accessible authoritative source. If the evidence card does not support the claim, do not write the claim.
21. Write `citation_evidence_report.md` for every final writing or validation task. It must record why each paper was selected, which manuscript claim it supports, what source page supports it, and whether the claim is fully supported, partially supported, or unsupported.
22. A paper that seems suitable but has uncertain metadata, uncertain citation evidence, or pending confirmation must not enter final writing or final BibTeX. Keep it only as a pending replacement candidate and tell the user which section, paragraph, and citation claim it could replace.
23. Do not include active `doi` fields in final BibTeX entries. DOI is mandatory for verification and reports, but it must not be displayed inside `references.bib` entries unless the user explicitly changes the required format.
24. Final outputs must be written to disk. Do not merely show `manuscript.tex` content in chat. Before final response, verify that `candidate_literature.md`, `manuscript.tex`, `references.bib`, `reference_validation_report.md`, and `citation_evidence_report.md` exist in the output directory.
25. `manuscript.tex` is mandatory for every final writing task. A final manuscript delivered as Markdown or prose-only output is incomplete.
26. The workflow must follow Gate 0 through Gate 7 in `references/main-workflow.md`. A later gate may start only after the previous gate passes. Gate failures must be recorded and block downstream outputs.

## Reference Files

- `references/main-workflow.md`: end-to-end workflow, confirmation checkpoint, output files.
- `references/search.md`: query generation, source preference, screening, deduplication, CAS/year filtering.
- `references/verification.md`: candidate-level and deep metadata verification from official sources.
- `references/writing.md`: Introduction, Related Work, literature allocation, citation ordering.
- `references/bibtex.md`: required BibTeX formats and commented URL handling.
- `references/citation-evidence.md`: citation claim evidence cards, paper-selection rationale, publisher-page traceability.
- `references/quality-check.md`: final audit checklist.

## Output Contract

Save final artifacts to:

```text
output/<YYYY-MM-DD_topic-slug>/
```

Required files:

```text
candidate_literature.md
manuscript.tex
references.bib
reference_validation_report.md
citation_evidence_report.md
```

Do not send a final answer until all required files have been created or a concrete blocker is reported.

Final response should briefly report the candidate/final retained count, deep-verification status, output directory, generated files, and any manual checks still needed.
