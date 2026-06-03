# Paper Section Generator

基于真实文献检索与出版商页面深度核验的学术论文 Introduction / Related Work 自动生成工具。

## 概述

`paper-section-generator` 是一个 **Claude Code Skill**，它严格按照以下流程工作：

1. **WebSearch 文献检索** — 从标题和摘要中提取多组关键词，多轮搜索真实论文
2. **候选文献筛选** — 中科院分区核验、年份过滤、去重、可信度标记
3. **用户确认** — 候选列表展示，用户保留/删除/补充
4. **深度核验** — 逐篇追溯出版商页面，验证全部 9 项 BibTeX 字段
5. **引用证据构建** — 为每篇文献创建证据卡片，确保引用可追溯
6. **正文撰写** — 生成 Elsevier `elsarticle` 格式 LaTeX 手稿
7. **BibTeX 生成** — 按首次引用顺序输出，不含活跃 DOI 字段
8. **质量审计** — 百余项检查清单，确保输出完整可编译

## 安装

### 前置条件

- **Claude Code**（CLI、桌面版、Web 版或 IDE 插件）
- 启用 **WebSearch** 工具

### 安装步骤

1. 将整个 `paper-section-generator` 目录复制到 Claude Code 的 skills 目录：

```
~/.claude/skills/paper-section-generator/
├── SKILL.md
├── references/
│   ├── main-workflow.md
│   ├── search.md
│   ├── verification.md
│   ├── writing.md
│   ├── bibtex.md
│   ├── citation-evidence.md
│   └── quality-check.md
└── README.md
```

2. 重新加载 skills：

```
/skills reload
```

3. 使用 skill：

```
/paper-section-generator
```

## 快速开始

在对话中输入 `/paper-section-generator`，然后提供论文信息：

```
论文标题：Exploring the robustness of intercity transportation network 
          under typhoon disasters with cascading failures

摘要：The large-scale, multi-modal, and combined travel characteristics...

用户要求：
- 文献时间范围：近五年（2021-2026）
- 期刊分区要求：中科院二区及以上
- 文献数量：20篇
```

Skill 将自动完成：检索 → 筛选 → 候选列表 → 等待确认 → 深度核验 → 生成手稿。

## 工作流程（Gate 0 — Gate 7）

Skill 采用严格的**顺序门控（Sequential Gate）**架构，后一阶段只能在上一阶段通过后启动：

| Gate | 名称 | 输入 | 输出 |
|------|------|------|------|
| 0 | 主题理解 | 用户标题 + 摘要 | 主题图谱、检索计划 |
| 1 | 候选检索与筛选 | 主题图谱 | `candidate_literature.md`（草稿） |
| 2 | **用户确认** | 候选列表 | 确认/调整后的保留文献 |
| 3 | 元数据字段核验 | 保留文献 | `reference_validation_report.md` |
| 4 | 引用证据构建 | 核验通过的文献 | `citation_evidence_report.md` |
| 5 | 正文撰写 | 证据卡片 | `manuscript.tex`（草稿） |
| 6 | BibTeX 生成 | 核验元数据 + 引用顺序 | `references.bib` |
| 7 | 最终审计与交付 | 所有产物 | 5 个必需文件的磁盘写入 |

**Gate 2 是核心检查点**：在用户确认候选文献之前，不进行深度核验、不写正文、不生成 BibTeX。

## 引用文件说明

`references/` 目录下的 7 个文件各自定义了一个子流程：

| 文件 | 用途 |
|------|------|
| `main-workflow.md` | 端到端 Gate 0-7 流程、文件清单、LaTeX 模板 |
| `search.md` | 关键词生成、WebSearch 策略、去重、分区/年份过滤 |
| `verification.md` | 两阶段核验（Stage 1 真实性 → Stage 2 一致性）、字段优先级、`number` 归一化规则 |
| `writing.md` | Introduction 5 段式结构、Related Work 连续段落风格、开篇引用/尾注引用规范、摘要优先证据策略 |
| `bibtex.md` | `@article` / `@inproceedings` 格式、URL 注释化、DOI 不活跃字段规则、两阶段 BibTeX 处理 |
| `citation-evidence.md` | 证据卡片格式、声明级对比、待替换候选文献管理 |
| `quality-check.md` | 最终审计检查清单（100+ 项） |

## 输出文件

每次完成后的输出目录结构：

```
output/<YYYY-MM-DD_topic-slug>/
├── candidate_literature.md          # 候选文献列表与用户选择记录
├── manuscript.tex                   # Elsevier elsarticle 格式 LaTeX 手稿
├── references.bib                   # 按首次引用顺序排列的 BibTeX
├── reference_validation_report.md   # 字段级核验状态与修正日志
└── citation_evidence_report.md      # 每篇引用文献的证据卡片
```

## 核心规则摘要

### 元数据来源权威性

```
出版商页面 > DOI 解析元数据 > Crossref（辅助）
```

**禁止** 使用 Google Scholar、Semantic Scholar、OpenAlex、搜索摘要作为最终元数据。

### LLM 边界

LLM **可以**：推荐论文、判断相关性、提取摘要、撰写引用段落、检查字段完整性。

LLM **不能**：生成 DOI、标题、作者、期刊、年份、卷号、期号、页码等核心元数据。

### BibTeX 规范

- 无活跃 `doi` 字段（DOI 仅记录在验证报告中）
- URL 作为注释行 `% url = {https://doi.org/<DOI>}` 放在条目外
- 作者列表完整，禁止 `et al.`
- `number = {1}` 仅在 Stage 2 归一化时应用
- 9 项必填字段：`author`、`title`、`journal`、`year`、`volume`、`number`、`pages` + 注释 URL

### 引用规范

- 按首次出现顺序编号 `[1]` → `[2]` → …，不跳号不乱序
- Related Work 新增引用比例 ≥ 70%
- 方法/模型/框架引用 → 开篇引用 `[N]`
- 背景/概念/定义引用 → 尾注引用 `[N]`
- 引用内容不得超出摘要或可访问全文所支持的范围

### 论文准入条件

一篇论文成为最终参考文献必须满足：

1. 真实存在，通过 WebSearch 发现
2. 有 DOI
3. 用户确认保留
4. 经出版商页面深度核验，全部 P0/P1 字段通过
5. 有支持声明级引用证据卡片

## 常见问题

**Q: 没有 DOI 的论文可以使用吗？**
A: 默认排除。除非用户明确表示仅用于候选参考，不纳入最终参考文献。

**Q: 如何指定中科院分区要求？**
A: 在用户要求中明确，例如"中科院二区及以上"。Skill 会通过 LetPub 等来源核验分区。

**Q: Related Work 可以有小标题吗？**
A: 默认不设置子标题，以连续段落形式组织，每段以领域特定的过渡句开头。

**Q: WebFetch 无法访问出版商页面怎么办？**
A: Skill 会自动切换到 `site:` 限定 WebSearch 进行定向检索。

## 许可证

本项目为个人学术辅助工具，可自由修改和使用。
