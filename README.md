# LLM Wiki Agent

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

**一个面向编码 Agent 的技能。** 把源文档丢进 `raw/`,告诉 Agent 去导入 — 它会读取文档、抽取知识,并构建一个持续互联的 Wiki。每加入一份新源,Wiki 就更丰富。你不用写一行字。

> 大多数知识工具让你自己翻笔记。这一个会读遍你收集的所有资料,然后写出一份**会随时间复利**的结构化 Wiki —— 交叉引用已建好、矛盾已标记、综合已就绪。

```
ingest raw/papers/attention-is-all-you-need.md
```

```
wiki/
├── index.md          所有页面的目录 —— 每次导入都更新
├── log.md            仅追加的操作记录
├── overview.md       跨所有源的活态综合
├── sources/          每个源文档一个摘要页
├── entities/         人物、公司、项目 —— 自动创建
├── concepts/         思想、框架、方法 —— 自动创建
└── syntheses/        查询答案归档为 Wiki 页面
graph/
├── graph.json        持久化的节点/边数据(SHA256 缓存)
└── graph.html        可交互的 vis.js 可视化 —— 任意浏览器打开
```

## Sigma 导师(内置)

本仓库自带 `/sigma-with-wiki` —— 一个基于 Wiki 的 Bloom 2-Sigma 掌握度导师。Wiki **就是**课程:

```
/sigma-with-wiki                # 自动:有会话则恢复,无则新开
开始学习                         # 新开
继续学习                         # 恢复
/sigma-with-wiki --visual       # 每轮带丰富可视化
```

**工作原理:**
- **不需要传主题** —— 技能启动时读取 `wiki/overview.md` + `wiki/index.md`,自动推导出完整的学习路线图(范围、顺序、每个概念对应的 Wiki 页面),然后开始教学
- **Wiki 读取门控** —— 教学任一概念前,技能会重新读取该概念对应的 Wiki 页面。教学永远不脱离源材料
- **每个仓库一个会话** —— 状态保存在 `sigma-with-wiki/session/`,而非按主题分子目录

若 `wiki/overview.md` 或 `wiki/index.md` 缺失,技能中止 —— 此时回退到 `/sigma` 进行无主题锚定的辅导。

完整技能定义见 `.claude/skills/sigma-with-wiki/SKILL.md`。

## 安装

**前置依赖:** [Claude Code](https://claude.ai/code)、[Codex](https://openai.com/codex),或任何会读取配置文件的 Agent。

```bash
git clone https://github.com/SamurAIGPT/llm-wiki-agent.git
cd llm-wiki-agent
```

在你的 Agent 中打开 —— 无需 API 密钥,无需 Python 环境:

```bash
claude      # 读取 CLAUDE.md + .claude/commands/(可用 slash 命令)
codex       # 读取 AGENTS.md
opencode    # 读取 AGENTS.md
```


## Slash 命令(Claude Code)

| 命令 | 用法 |
|---|---|
| `/wiki-ingest` | `ingest raw/my-article.md` |
| `/wiki-query` | `query: 主题概览有哪些?` |
| `/wiki-health` | `health`(快速,每个会话都跑) |
| `/wiki-lint` | `lint the wiki`(耗时,周期性跑) |
| `/wiki-graph` | `build the knowledge graph` |

也可以直接用自然语言描述需求:

- *"导入这个文件: raw/papers/attention-is-all-you-need.md"*
- *"Wiki 里关于 transformer 模型都讲了些什么?"*
- *"检查 Wiki 是否有孤立页面和内容矛盾"*
- *"构建知识图谱,展示与 RAG 关联的节点"*

Claude Code 会自动读取本文件,并按下方工作流执行。


## 用法

所有 Agent 都能理解自然语言和简短触发:

```
ingest raw/papers/my-paper.md              # 导入 markdown 源
ingest report.pdf                          # 自动转 .md 再导入
ingest slides.pptx notes.docx              # 批量混合格式
query: what are the main themes?           # 从 Wiki 综合答案
lint                                       # 找孤立页、矛盾、缺口
build graph                                # 从所有 wikilink 构建 graph.html
```

纯自然语言也行:

```
"Ingest this paper: raw/papers/llama2.md"
"What does the wiki say about attention mechanisms?"
"Check for contradictions across sources"
"Build the knowledge graph and tell me the most connected nodes"
```

**Claude Code** 还通过 `.claude/commands/` 提供 `/wiki-ingest`、`/wiki-query`、`/wiki-lint`、`/wiki-graph` 作为 slash 命令。这些是 Claude Code 专属,其他 Agent 用上方自然语言触发(效果一致)。

支持 Markdown、PDF、DOCX、PPTX、XLSX、HTML、TXT、CSV、JSON、XML、RST、EPUB 等更多格式。非 Markdown 文件在导入时通过 [markitdown](https://github.com/microsoft/markitdown) 自动转换 —— 无需额外步骤。

## 你将得到

**持久化 Wiki** —— 结构化的 Markdown 页面,跨会话累积。与聊天不同,内容不会丢失。

**Entity 页面** —— 每个跨源被提及的人物、公司、项目自动创建。每有新源引用都会更新。

**Concept 页面** —— 每个关键思想或框架自动创建,并交叉引用到所有讨论它的源。

**活态概览** —— `wiki/overview.md` 在每次导入时修订,反映当前所有已读内容的综合。

**矛盾标记** —— 当新源与已有论断矛盾时,在导入时立即标记,而不是埋到查询时才暴露。

**知识图谱** —— `graph.html` 把每个 Wiki 页面显示为节点,每个 `[[wikilink]]` 显示为边,Claude 推断的隐式关系显示为虚线边。社区检测自动把相关主题聚成一团。

**Lint 报告** —— 孤立页、断链、缺失的 entity 页面、数据缺口及建议补充的源。

## 使用场景

### 研究

花几周深入钻研一个主题 —— 读论文、文章、报告。

```
/wiki-ingest raw/papers/attention-is-all-you-need.md
/wiki-ingest raw/papers/llama2.md
/wiki-ingest raw/papers/rag-survey.md

# Wiki 自动建立 entity 页面(Meta AI、Google Brain)
# 与 concept 页面(Attention、RLHF、Context Window)。

/wiki-query "What are the main approaches to reducing hallucination?"
/wiki-query "How has context window size evolved across models?"

/wiki-lint
# → "No sources on mixture-of-experts — consider the Mixtral paper"
```

结束时你拥有的是一份结构化、互联的参考资料 —— 而不是一个你再也不会打开的 PDF 文件夹。

---

### 读一本书

边读边归档每一章。为人物、主题、论点建出页面。

```
/wiki-ingest raw/book/chapter-01.md
/wiki-ingest raw/book/chapter-02.md

# Wiki 自动创建 entity 与主题页面。

/wiki-query "How has the protagonist's motivation evolved?"
/wiki-query "What contradictions exist in the author's argument so far?"

/wiki-graph   # → graph.html 展示每个人物/主题及其关联
```

想象成托尔金百科(Tolkien Gateway)那样的粉丝 Wiki —— 在你阅读的同时构建,所有交叉引用由 Agent 完成。

---

### 个人知识库

追踪目标、健康、习惯、自我提升 —— 归档日记、文章、播客笔记。

```
/wiki-ingest raw/journal/2026-01-week1.md
/wiki-ingest raw/articles/huberman-sleep-protocol.md
/wiki-ingest raw/articles/atomic-habits-summary.md

/wiki-query "What patterns show up in my journal entries about energy?"
/wiki-query "What habits have I tried and what was the outcome?"
```

Wiki 随时间构建出一幅结构化图景。像"Sleep"、"Exercise"、"Deep Work"这样的概念,会从所有归档源中累积证据。

---

### 业务 / 团队情报

喂入会话转录、项目文档、客户访谈记录。

```
/wiki-ingest raw/meetings/q1-planning-transcript.md
/wiki-ingest raw/docs/product-roadmap-2026.md
/wiki-ingest raw/calls/customer-interview-acme.md

/wiki-query "What feature requests have come up most across customer calls?"
/wiki-query "What decisions were made in Q1 and what was the rationale?"

/wiki-lint
# → "Project X mentioned in 5 pages but no dedicated page"
# → "Roadmap contradicts customer interview on priority of feature Y"
```

Wiki 保持新鲜,因为没人想做的维护工作由 Agent 完成。

---

### 竞品分析

持续追踪一家公司、一个市场或一项技术。

```
/wiki-ingest raw/competitors/openai-announcements.md
/wiki-ingest raw/market/ai-funding-report-q1.md

/wiki-query "How do OpenAI and Anthropic differ on safety approach?"
/wiki-query "Which companies announced multimodal models in the last 6 months?"
/wiki-query "Competitive landscape summary as of today"
# → Agent 展示答案,然后询问是否归档为 synthesis 页面
```

## 图谱

两遍构建:

1. **确定性** —— 解析 Wiki 页面中所有 `[[wikilinks]]` → 标记为 `EXTRACTED` 的边
2. **语义** —— Agent 推断 wikilink 未捕捉的隐式关系 → 标记为 `INFERRED`(带置信度分数)或 `AMBIGUOUS` 的边

Louvain 社区检测按主题把节点聚团。SHA256 缓存意味着只重处理变更过的页面。输出是自包含的 `graph.html` —— 无需服务器,任意浏览器打开。

## CLAUDE.md / AGENTS.md

Schema 文件告诉 Agent 如何维护 Wiki —— 页面格式、import/query/lint/graph 工作流、命名规范。这是关键的配置文件。可按你的领域定制行为。

| Agent | Schema 文件 |
|---|---|
| Claude Code | `CLAUDE.md` |
| Codex / OpenCode | `AGENTS.md` |

## 与 RAG 有什么不同

| RAG | LLM Wiki Agent |
|---|---|
| 每次查询重新推导知识 | 一次编译,持续更新 |
| 原始 chunk 作为检索单元 | 结构化的 Wiki 页面 |
| 没有交叉引用 | 引用已预建 |
| 矛盾在查询时(也许)才浮出 | 在导入时标记 |
| 没有累积 | 每个源都让 Wiki 更丰富 |

## Obsidian 集成

Wiki 设计为可在 [Obsidian](https://obsidian.md) 中无缝浏览。因为 Agent 维护一致的 `[[wikilinks]]`,你的 Vault 里会自然生长出一张知识图谱。

### Vault 软链接模式

若想把 LLM Wiki Agent 仓库与个人主 Vault 分开,可用软链接:
1. 将工作 Agent 仓库放在如 `~/llm-wiki-agent`
2. 在主 Obsidian Vault 中创建软链接:
   ```bash
   ln -sfn ~/llm-wiki-agent/wiki ~/your-obsidian-vault/wiki
   ```
3. 用 [Obsidian Web Clipper](https://obsidian.md/clipper) 或直接写到 Agent 仓库的 `raw/` 来排队待导入内容。

> **注意:** 若移动了本地仓库目录,记得更新软链接,否则 `wiki/` 目录在 Obsidian 中会显示为缺失。

### 推荐的 .obsidian 配置

- **Graph View:** 过滤掉 `index.md` 和 `log.md`(如 `-file:index.md -file:log.md`),避免它们在 Obsidian 图谱中成为引力井。
- **Dataview:** 使用社区插件 [Dataview](https://blacksmithgu.github.io/obsidian-dataview/) 查询 Agent 自动注入的 YAML frontmatter(如 `type: source`、`tags: [diary]`)。

## 多格式导入

直接把任意支持的文件丢进 `ingest` —— 无需单独转换步骤:

```bash
# 以下全部可用 —— 导入时自动转换
ingest report.pdf
ingest meeting-notes.docx
ingest slides.pptx
ingest data.xlsx
ingest page.html
ingest raw/mixed-folder/          # 递归查找所有支持的文件
```

**支持的格式:**
`.md` `.pdf` `.docx` `.pptx` `.xlsx` `.xls` `.html` `.htm` `.txt` `.csv` `.json` `.xml` `.rst` `.rtf` `.epub` `.ipynb` `.yaml` `.yml` `.tsv` `.wav` `.mp3`

非 Markdown 文件通过 [markitdown](https://github.com/microsoft/markitdown) 自动转换。使用 `--no-convert` 跳过自动转换,只处理 `.md` 文件。

### arXiv 论文(高级)

arXiv 论文用 `tools/pdf2md.py` 可获得更高保真度的转换:

```bash
python tools/pdf2md.py 2401.12345                      # 按 arXiv ID
python tools/pdf2md.py https://arxiv.org/abs/2401.12345 # 按 URL
python tools/pdf2md.py paper.pdf --backend marker       # 复杂多栏 PDF
```

然后导入生成的 `.md`:

```
ingest raw/papers/my-paper.md
```

### 批量目录转换(高级)

预转换整个目录(适合批量导入):

```bash
python tools/file_to_md.py --input_dir raw/imports/
python tools/file_to_md.py --input_dir raw/imports/ --delete_source  # 删除原始文件
```

### 可选依赖

| 包 | 安装 | 用途 |
|---|---|---|
| [markitdown](https://github.com/microsoft/markitdown) | `pip install markitdown` | 自动转换非 .md 文件(多格式导入必需) |
| [arxiv2md](https://github.com/ryansingman/arxiv2md) | `pip install arxiv2markdown` | 通过结构化源获取 arXiv 论文 |
| [Marker](https://github.com/VikParuchuri/marker) | `pip install marker-pdf` | 复杂多栏学术 PDF |
| [PyMuPDF4LLM](https://github.com/pymupdf/RAG) | `pip install pymupdf4llm` | 快速 PDF 抽取(无需 GPU) |
| [tqdm](https://github.com/tqdm/tqdm) | `pip install tqdm` | 批量目录转换的进度条 |

## 小贴士

- 直接把文件(PDF、DOCX 等)丢进 `raw/` 然后 `ingest` —— 转换是自动的
- 对于 arXiv 论文,`tools/pdf2md.py` 输出比通用 markitdown 保真度更高
- 查询答案先展示 —— 然后 Agent 会询问是否归档为 synthesis 页面。你的探索会和导入的源一样**复利累积**
- Wiki 本身就是 git 仓库 —— 免费获得版本历史
- `tools/` 下的独立 Python 脚本无需编码 Agent 也可使用(需 `ANTHROPIC_API_KEY`)

## 技术栈

NetworkX + Louvain + Claude + vis.js。无服务器、无数据库,完全本地运行。一切都是纯 Markdown 文件。

---
