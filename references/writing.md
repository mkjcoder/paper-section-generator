# Writing

Use only user-confirmed and deeply verified papers. Before writing final prose, build citation evidence cards using `citation-evidence.md`; after writing, verify that every cited claim in Introduction and Related Work maps to an evidence card.

## Frontmatter Author Policy

Do not display author, institution, affiliation, address, or corresponding-author information in `manuscript.tex` unless the user explicitly provides and requests it.

Use empty placeholders:

```latex
\author{}
\address{}
```

Do not generate names, affiliations, departments, universities, correspondence notes, or emails from the title, abstract, prior examples, or assumptions.

## Literature Allocation

Before writing, allocate papers:

```text
Introduction 使用文献：
Related Work 优先使用文献：
允许重复使用文献：
需要补充检索方向：
```

Introduction should use background, problem-importance, review, and foundational papers. Related Work should use method-comparison and topic-specific papers, preferably different from Introduction.

For every allocated paper, record why it was selected:

```text
Paper:
Selected for:
Why this paper fits the manuscript topic:
Specific manuscript claim(s) it can support:
Publisher/DOI evidence source:
Evidence status:
```

## Introduction

Default structure:

1. Research background
2. Existing problem and importance
3. Relevant progress
4. Limitations of existing research
5. This paper's work and contributions

Rules:

- Every paragraph should contain at least one verified citation.
- Use only user-confirmed and deeply verified references.
- Use only citations with `SUPPORTED` or `PARTIALLY_SUPPORTED_WITH_LIMITS` evidence status in `citation_evidence_report.md`.
- Do not cite unconfirmed or low-confidence papers unless the user explicitly retained them.
- Do not invent claims to make prose smoother.
- If a claim lacks support, say more search is needed instead of writing it as fact.
- Prefer 4-8 core references; do not place every candidate paper in Introduction.

## Related Work

Write Related Work as continuous paragraphs without subsection headings. Do not generate small titles such as `2.1`, `\subsection{...}`, or topic labels inside the Related Work section.

Rules:

- Each paragraph must have a clear field-specific theme, but the theme is expressed through the paragraph content rather than a heading.
- Start each paragraph with a transition sentence tied to the domain background, research object, or method family discussed in that paragraph.
- Do not use generic template openings. The opening sentence must be specific to the paragraph topic.
- Suitable opening style examples:
  - `The multi-layer network paradigm has emerged as a powerful framework for capturing the structural complexity of modern supply chains...`
  - `Dual-layer network models have been developed to capture cascading failure propagation in competitive environments...`
  - `Interdependent networks provide a framework for analyzing cyber-physical interactions in supply chains...`
- After the opening transition, describe method/experiment papers in this order when the abstract supports it: research method or model -> validation / experiment / simulation setting -> main result -> optional limitation or application value.
- Keep each paragraph around 3-6 sentences when possible, with complete internal logic.
- Use paragraph-internal connectors such as `Following this approach`, `Building on these concepts`, or `In addition to previous studies` only when they reflect a real thematic or methodological relationship.
- Compare research goals, methods, strengths, limitations, and relation to the user's paper only when supported by the abstract or verified source content.
- End the final Related Work paragraph by identifying the research gap and motivating the user's contribution.
- Prefer references not already used in Introduction.
- Keep Related Work new-reference ratio >= 70% by default.
- Keep overlap with Introduction <= 30% by default.
- Reuse Introduction references only for foundational works, highly relevant methods, or gap framing.
- Use opening citations `[N]` for method, model, framework, experiment, validation, or result-focused papers.
- Use trailing citations `[N]` for conceptual, background, definition, or theory-supporting papers.
- Citation descriptions must be abstract-first and evidence-card constrained. If a field or result is not stated in the abstract or accessible verified full text, omit it or mark `[not stated in abstract]`; do not fabricate details.
- Do not write a cited sentence until its evidence card identifies the source page and supported content.

## Citation Placement and Literature Description Style

Use two citation styles consistently according to the role of each reference.

### Opening Citation: Method or Research-Focused References

Use opening citations when a paper is discussed for a specific method, model, strategy, framework, experiment, validation design, or research result.

Rules:

- Put the citation number at the beginning of the first sentence, e.g. `[4]`.
- Immediately describe the paper's core contribution: method, framework, validation, experiment, result, or finding.
- Expand the description in 3-6 sentences when the paper is important to the argument.
- Keep the focus on the research, not the author. Avoid repeated author names.
- In follow-up sentences, use phrases such as `the proposed method`, `the introduced method`, `this approach`, `this framework`, or `the validation results`.
- Prefer this order: research problem -> method/framework -> validation/experiment -> result/finding -> limitation or applicable condition when useful.

Template:

```text
[N] proposed/presented/developed <method/framework/model> for <research problem>.
The proposed method <key mechanism or design>.
The validation was conducted using <dataset/system/case study/experiment>.
The results showed <main result or finding>.
This approach is useful for <applicable condition>, but <limitation> remains <optional>.
```

Example:

```text
[4] presented a dynamic probabilistic risk assessment method designed for analyzing the cybersecurity risks of electric power grids.
The introduced method aimed to decrease the dependence on expert judgment, encompassed a wide array of components and system dynamics, and modeled the interactions among various contributing entities such as attackers and operators.
The validation of the proposed method was carried out using the IEEE 14-bus system.
```

### Trailing Citation: Conceptual or Supporting Background References

Use trailing citations when a paper supports background, concept definition, theoretical motivation, or a broad conclusion without requiring detailed method discussion.

Rules:

- Put the citation number at the end of the sentence, e.g. `[2]`.
- Usually use one concise sentence.
- Do not expand experiments or methods unless the paper becomes method-focused in the paragraph.
- Use this style for background in Introduction and short conceptual bridges in Related Work.

Template:

```text
<Concept, definition, or broad claim> [N].
```

Example:

```text
Network robustness is a base for more complex and high-level robustness measurement such as network controllability robustness [2].
```

### Consistency Rules

- Method, model, framework, experiment, and result references use opening citations.
- Background, definition, theory, and supporting references use trailing citations.
- Do not switch styles randomly within a paragraph.
- Avoid author-centered phrasing unless the user's target style explicitly requests it.
- When multiple papers support the same conceptual statement, use a trailing multi-citation such as `[2,3]`.

## Abstract-First Evidence Policy

When writing Introduction or Related Work, the abstract is the primary evidence source for describing a cited paper. If full text is unavailable, all descriptions must be grounded in the abstract. Do not write claims that are more specific than the abstract supports.

### Source Priority

For each cited paper, use evidence in this order:

1. Publisher abstract
2. DOI page abstract
3. Author-provided abstract from arXiv or an institutional repository
4. Database abstract from Crossref, PubMed, IEEE, Springer, Elsevier, Wiley, ACM, Semantic Scholar, or OpenAlex
5. Full text, only when legally and clearly accessible

If full text is not accessible, do not infer details beyond the abstract.

### Evidence Extraction Before Writing

Before describing each cited paper, extract an evidence card:

```text
Paper:
Abstract source:
Research objective:
Research problem:
Proposed method / framework:
Experimental setting:
Dataset / network / case study:
Main findings:
Limitations:
Information not mentioned in abstract:
Publisher article page:
DOI landing page:
Why selected:
Supported manuscript claim(s):
Claim support status:
```

Only evidence-card information may be used in the manuscript. If a field is not mentioned in the abstract, omit it from the citation paragraph or explicitly mark it as unavailable from the abstract.

After writing, create `citation_evidence_report.md` and compare each cited sentence or citation cluster against its evidence card. If a manuscript claim is broader than the evidence, revise the claim or mark it as `UNSUPPORTED_CLAIM` and remove it from final prose.

### Allowed Content

Only write content explicitly supported by the abstract or legally accessible full text:

- Research objective
- Research problem
- Proposed method or framework
- Experimental setting
- Dataset, network, or case study
- Main findings
- Limitations

### Forbidden Content

Do not invent:

- Experimental results
- Numerical improvements
- Comparison baselines
- Datasets
- Case studies
- Algorithm details
- Parameter settings
- Limitations
- Method details not mentioned in the abstract

Never infer details from the title, domain knowledge, similar papers, or unrelated papers.

### Safe Wording

Use cautious expressions unless the abstract explicitly supports stronger wording:

- `focused on`
- `addressed`
- `investigated`
- `proposed`
- `introduced`
- `developed`
- `reported`
- `suggested`

Avoid strong claims unless the abstract explicitly states them:

- `significantly outperformed`
- `achieved optimal performance`
- `proved effectiveness`
- `demonstrated superior results`

### Abstract-Constrained Literature Review

Every citation paragraph must be verifiable from the abstract. If a reviewer reads only the abstract, they should be able to confirm every claim in the paragraph. If the abstract does not support a claim, the claim must not appear.

### Highest Priority Rule

Citation content authenticity is more important than citation richness. It is better to write a shorter but accurate citation description than a detailed but unsupported one.

## Citation Numbering

Citation numbers must follow first appearance order:

- First cited paper is `[1]`.
- Second first-appearing paper is `[2]`.
- No skipped numbers.
- No citation such as `[5]` before `[4]`.
- Multi-citation groups must be ascending, e.g. `[1,2,3]` or `[1-3]`.
- `references.bib` entries must follow final manuscript citation order.
- `candidate_literature.md` final numbering must match final citation order.
