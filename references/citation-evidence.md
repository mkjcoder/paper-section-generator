# Citation Evidence

Use this file whenever writing or validating Introduction, Related Work, or any cited literature review prose. The goal is to make every citation defensible: the user should be able to open the recorded publisher page or DOI page and see why the paper was selected and what manuscript claim it supports.

## Core Rule

Do not cite a paper merely because it is real or topically similar. A paper may support final prose only when a citation evidence card shows:

- Why the paper was selected.
- Which manuscript claim it supports.
- Which authoritative source page supports that claim.
- Whether the claim is fully supported, partially supported with limits, or unsupported.

## Authority for Citation Content

Use evidence in this order:

1. Publisher article page abstract.
2. DOI landing page abstract.
3. Publisher-hosted full text or official PDF when legally accessible.
4. Author/institution-hosted full text when legally accessible.
5. Crossref metadata only for bibliographic metadata, not detailed research claims.

Google Scholar, Semantic Scholar, OpenAlex, search snippets, and generic web summaries are discovery clues only. They must not be used as final evidence for manuscript claims.

## Evidence Card Format

Create one evidence card for every cited paper:

```text
Ref:
BibTeX key:
Paper title:
Publisher article page:
DOI:
DOI landing page:
Evidence source type: Publisher abstract / DOI abstract / Publisher full text / Official PDF / Author full text
Why selected:
Topic fit:
Method fit:
Dataset/network fit:
Contribution fit:
Supported manuscript section: Introduction / Related Work / Both
Supported manuscript claim(s):
Evidence summary:
Limits of evidence:
Fields or claims not supported by the source:
Status: SUPPORTED / PARTIALLY_SUPPORTED_WITH_LIMITS / UNSUPPORTED_CLAIM / EXCLUDED_PENDING_CONFIRMATION
```

## Claim-Level Comparison

After drafting, create a claim-level table in `citation_evidence_report.md`:

```text
| Ref | Section | Manuscript claim | Evidence source | Evidence summary | Support status | Action |
```

Rules:

- `SUPPORTED`: the source explicitly supports the manuscript claim.
- `PARTIALLY_SUPPORTED_WITH_LIMITS`: the source supports the general idea, but the manuscript wording must be narrowed.
- `UNSUPPORTED_CLAIM`: the source does not support the claim; remove or rewrite the claim before final delivery.
- `EXCLUDED_PENDING_CONFIRMATION`: the paper or field is not yet verified; do not use it in final prose or final BibTeX. Keep it only as a pending replacement candidate with a suggested replacement target.

## Pending Replacement Evidence

For papers that appear relevant but cannot be confirmed, create a pending replacement card instead of using them in final prose:

```text
Pending ref:
Paper title:
Publisher/DOI page:
Unconfirmed field or evidence:
Reason excluded from final prose/BibTeX:
Suggested replacement section:
Suggested replacement paragraph:
Suggested replacement citation key or manuscript claim:
Why this candidate may fit better:
What the user must confirm:
Status: EXCLUDED_PENDING_CONFIRMATION
```

Rules:

- Do not cite pending replacement candidates in final `manuscript.tex`.
- Do not include pending replacement candidates in final `references.bib`.
- The report must make it easy for the user to manually swap the candidate in by identifying the exact section and paragraph where it may fit.

## Selection Rationale

For each selected paper, record why this paper is useful instead of merely listing it:

```text
Selection reason:
Relation to user paper:
Why it is better than weaker alternatives:
Specific concept/method/result borrowed:
Role in manuscript logic:
```

Do not write vague reasons such as "relevant to the topic" without explaining the concrete match.

## Final Report Requirements

`citation_evidence_report.md` must include:

- A paper-level evidence card for every final cited paper.
- A claim-level comparison table for Introduction and Related Work.
- Publisher article page links for every final cited paper.
- DOI landing page links when DOI is available.
- A list of removed, rewritten, or pending claims.
- A pending replacement candidate section with suggested section, paragraph, and citation claim replacement targets.
- A short explanation of why each final reference was selected.

Final manuscript delivery is incomplete if `citation_evidence_report.md` is missing or if any final cited claim remains `UNSUPPORTED_CLAIM` or `EXCLUDED_PENDING_CONFIRMATION`.
