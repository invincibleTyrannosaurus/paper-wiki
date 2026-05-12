---
name: paper-wiki
description: 论文知识库统一入口。整合每日论文推荐、单篇深度分析、会议追踪、笔记搜索和知识库维护，使用 Obsidian 作为存储后端。触发词：/paper-wiki、论文知识库、paper wiki、初始化论文库
---

# Paper Wiki — 论文知识库

## 概述

Paper Wiki 是统一的论文知识库管理入口，整合以下功能：
- **每日论文推荐**（arXiv + Semantic Scholar）
- **单篇论文深度分析**（PDF + 图片提取 + 结构化笔记）
- **顶会论文追踪**（DBLP + S2，CVPR/ICLR/NeurIPS 等）
- **已有笔记搜索**（grep 快速搜索）
- **知识库维护**（索引重建、死链检测、manifest 更新）

**Vault 路径**: 首次初始化时由用户指定，后续所有操作从配置文件中读取
**配置文件**: `{VAULT_PATH}\paper-wiki-config.yaml`
**PDF 存放**: `_sources/papers/`
**分析报告**: `references/`

---

## 触发条件

当用户输入以下任一内容时触发：
- `/paper-wiki`
- `/paper-wiki daily`
- `/paper-wiki analyze <论文ID>`
- `/paper-wiki conf <会议名> <年份>`
- `/paper-wiki search <查询>`
- `/paper-wiki sync`
- "论文知识库"
- "paper wiki"
- "初始化论文库"

---

## 语言设置

默认使用简体中文（zh-CN）。如需英文输出，在配置文件中设置 `language: "en"`。

---

## 核心工作流

### 工作流 0: 初始化流程（首次使用）

当用户输入 `/paper-wiki` 且尚未检测到已初始化的知识库时，自动进入此流程。

#### 步骤 0: 询问知识库存储位置

```
欢迎使用 Paper Wiki！我将帮你建立一个论文知识库。

请指定知识库的存储位置（默认：D:\paper-wiki）：
```

- 用户直接回车 → 使用默认路径 `D:\paper-wiki`
- 用户输入路径 → 使用该路径（如 `D:\Research\paper-wiki`、`C:\Users\name\Documents\paper-wiki`）

路径格式支持：
- Windows 路径：`D:\paper-wiki` 或 `D:/paper-wiki`
- 相对路径：基于当前工作目录解析

记录用户选择的路径为 `{VAULT_PATH}`，后续所有步骤均使用此变量。

#### 步骤 1: 欢迎与方向询问

```
Paper Wiki 将创建在：{VAULT_PATH}

请告诉我你想跟踪的论文研究方向（例如：联邦学习后门攻击、大模型安全、多模态理解等）：
```

等待用户输入研究方向名称（如"联邦学习后门攻击"）。

#### 步骤 2: 自动生成核心关键词

**基于用户输入的研究方向名称，自动推断并生成英文关键词列表**。

生成规则：
- 将中文研究方向翻译为英文核心术语
- 提取关键技术名词（2-5个）
- 添加相关扩展词

示例：
- 用户输入"联邦学习后门攻击" → 生成 `["federated learning", "backdoor attack", "model poisoning", "Byzantine attack", "federated learning security"]`
- 用户输入"大模型安全" → 生成 `["large language model", "LLM safety", "AI alignment", "red teaming", "jailbreak"]`
- 用户输入"多模态理解" → 生成 `["multimodal understanding", "vision-language model", "VLM", "cross-modal", "image-text"]`

展示关键词并询问修改：
```
我为你生成了以下关键词：
  - federated learning
  - backdoor attack
  - model poisoning
  - Byzantine attack
  - federated learning security

是否需要添加、删除或修改？（直接输入修改后的关键词列表，用逗号分隔，或回车确认）
```

#### 步骤 3: 询问排除关键词（可选）

```
是否有需要排除的关键词？（如：survey, workshop, review。用逗号分隔，或回车跳过）
```

#### 步骤 4: 询问 arXiv 分类（可选，有默认值）

```
arXiv 分类（默认：cs.LG, cs.AI, cs.CR）：
```

默认分类：
- `cs.LG` (Machine Learning)
- `cs.AI` (Artificial Intelligence)
- `cs.CR` (Cryptography and Security)

#### 步骤 5: 生成配置预览

展示即将创建的完整配置：
```
=== 配置预览 ===

存储位置: {VAULT_PATH}
研究方向: 联邦学习后门攻击
关键词:
  - federated learning
  - backdoor attack
  - model poisoning
  - Byzantine attack
排除词:
  - survey
  - workshop
arXiv分类:
  - cs.LG
  - cs.AI
  - cs.CR

即将创建的目录结构:
  {VAULT_PATH}\_sources\papers\    (论文PDF)
  {VAULT_PATH}\references\          (分析报告)
  {VAULT_PATH}\10_Daily\            (每日推荐)
  {VAULT_PATH}\concepts\            (概念)
  {VAULT_PATH}\entities\            (实体)
  {VAULT_PATH}\skills\              (技能)
  {VAULT_PATH}\synthesis\           (综合)
  {VAULT_PATH}\journal\             (日志)
```

#### 步骤 6: 用户确认

```
确认创建？ (y/n)
```

- 输入 `n` → 返回步骤 2 重新修改
- 输入 `y` → 继续执行初始化

#### 步骤 7: 创建目录结构

```bash
mkdir -p "{VAULT_PATH}\_sources\papers"
mkdir -p "{VAULT_PATH}\references"
mkdir -p "{VAULT_PATH}\10_Daily"
mkdir -p "{VAULT_PATH}\concepts"
mkdir -p "{VAULT_PATH}\entities"
mkdir -p "{VAULT_PATH}\skills"
mkdir -p "{VAULT_PATH}\synthesis"
mkdir -p "{VAULT_PATH}\journal"
mkdir -p "{VAULT_PATH}\20_Research\PaperGraph"
```

#### 步骤 8: 写入配置文件

**8a. 生成业务配置** `{VAULT_PATH}\paper-wiki-config.yaml`：

```yaml
version: "1.0"
language: "zh"

research_domains:
  "联邦学习后门攻击":
    keywords:
      - "federated learning"
      - "backdoor attack"
      - "model poisoning"
      - "Byzantine attack"
      - "federated learning security"
    arxiv_categories: ["cs.LG", "cs.AI", "cs.CR"]
    priority: 10

excluded_keywords:
  - "survey"
  - "workshop"
  - "review"

search:
  arxiv:
    enabled: true
    max_results: 50
    days_back: 7
  semantic_scholar:
    enabled: true
    max_results: 30
    api_key: null
  dblp:
    enabled: true
    conferences:
      - "NeurIPS"
      - "ICML"
      - "ICLR"
      - "CCS"
      - "USENIX Security"
      - "AAAI"

scoring:
  daily:
    relevance: 0.40
    recency: 0.20
    popularity: 0.30
    quality: 0.10
    threshold: 7.0
  conf:
    relevance: 0.40
    popularity: 0.40
    quality: 0.20
    threshold: 7.0

directories:
  vault: "{VAULT_PATH}"
  papers: "_sources/papers"
  references: "references"
  daily: "10_Daily"
  graph: "20_Research/PaperGraph"
```

**8b. 生成环境配置** `.env`（写入 skill 根目录 `C:\Users\Zzl\.claude\skills\paper-wiki\.env`）：

```bash
cat > "C:\Users\Zzl\.claude\skills\paper-wiki\.env" << 'EOF'
# Paper Wiki configuration
# Auto-generated on initialization

# =============================================================================
# Required
# =============================================================================

# Vault root — where the paper wiki lives (Obsidian opens this directory)
PAPER_WIKI_VAULT_PATH={VAULT_PATH}

# =============================================================================
# Optional — defaults are derived from VAULT_PATH if not set
# =============================================================================

# Paper Wiki config file path (default: %VAULT_PATH%\paper-wiki-config.yaml)
PAPER_WIKI_CONFIG_PATH={VAULT_PATH}\paper-wiki-config.yaml

# Raw sources directory — where downloaded PDFs are archived
PAPER_WIKI_SOURCES_DIR={VAULT_PATH}\_sources\papers

# Research domains — comma-separated list of active research directions
# Must match keys in paper-wiki-config.yaml -> research_domains
PAPER_WIKI_DOMAINS=联邦学习后门攻击

# Active conferences to track (comma-separated)
PAPER_WIKI_CONFERENCES=NeurIPS,ICML,ICLR,CCS,USENIX Security,AAAI

# Default language for generated notes (zh | en)
PAPER_WIKI_LANGUAGE=zh

# =============================================================================
# API Keys (optional — leave empty to use free tiers)
# =============================================================================

# Semantic Scholar API key (increases rate limits)
SEMANTIC_SCHOLAR_API_KEY=
EOF
```

#### 步骤 9: 创建全局索引页

创建 `{VAULT_PATH}\index.md`：

```markdown
---
title: "Paper Wiki Index"
category: index
tags: [paper-wiki, index]
created: "2026-05-10T00:00:00Z"
updated: "2026-05-10T00:00:00Z"
---

# Paper Wiki 索引

## 研究方向
- 联邦学习后门攻击

## 目录
- [[10_Daily]] — 每日论文推荐
- [[references]] — 论文分析报告
- [[concepts]] — 核心概念
- [[entities]] — 人物、会议、工具
- [[synthesis]] — 综合与综述
- [[journal]] — 研究日志

## 统计
- 已分析论文: 0
- 今日推荐: 0
- 知识图谱节点: 0

---
*最后更新: 2026-05-10*
```

#### 步骤 10: 创建操作日志

创建 `{VAULT_PATH}\log.md`：

```markdown
## Log

- [2026-05-10T00:00:00Z] INIT vault="{VAULT_PATH}" direction="联邦学习后门攻击"
```

#### 步骤 11: 创建 manifest

创建 `{VAULT_PATH}\.manifest.json`：

```json
{
  "version": "1.0",
  "created": "2026-05-10T00:00:00Z",
  "vault_path": "{VAULT_PATH}",
  "sources": [],
  "pages": [
    {"path": "index.md", "source": null, "created": "2026-05-10T00:00:00Z"},
    {"path": "log.md", "source": null, "created": "2026-05-10T00:00:00Z"}
  ]
}
```

#### 步骤 12: 输出成功信息

```
Paper Wiki 初始化完成！

Vault: {VAULT_PATH}
研究方向: 联邦学习后门攻击
关键词: federated learning, backdoor attack, model poisoning, Byzantine attack

目录结构:
  _sources/papers/  — 论文PDF存放
  references/       — 分析报告
  10_Daily/         — 每日推荐

配置文件: {VAULT_PATH}\paper-wiki-config.yaml

下一步:
  /paper-wiki daily    — 获取今日论文推荐
  /paper-wiki analyze <arXiv ID>  — 深度分析单篇论文
  /paper-wiki conf ICLR 2025      — 追踪顶会论文
```

---

### 工作流 1: daily（每日论文推荐）

**触发**: `/paper-wiki daily`

从 arXiv 和 Semantic Scholar 搜索最新论文，根据关键词和排除词筛选，按相关性/新近性/热门度/质量四维度评分，生成当日推荐笔记到 `10_Daily/`。

**前置依赖**: 已初始化的 Vault，且能读取到 `{VAULT_PATH}\paper-wiki-config.yaml`

**详细步骤**: 见 [workflows/daily/SKILL.md](workflows/daily/SKILL.md)

---

### 工作流 2: analyze（深度分析单篇论文）

**触发**: `/paper-wiki analyze <论文ID>`

支持 arXiv ID、完整ID、论文标题、URL 四种输入格式。下载 PDF 和源码包，提取图片，生成包含摘要翻译、方法概述、实验结果、深度分析的结构化笔记到 `references/`。

**前置依赖**: 已初始化的 Vault，且能读取到 `{VAULT_PATH}\paper-wiki-config.yaml`

**详细步骤**: 见 [workflows/analyze/SKILL.md](workflows/analyze/SKILL.md)

---

### 工作流 3: conf（顶会论文追踪）

**触发**: `/paper-wiki conf <会议名> <年份>`

支持会议：NeurIPS, ICML, ICLR, CCS, USENIX Security, AAAI, CVPR, ICCV, ECCV

从 DBLP 获取会议论文列表，用关键词筛选并补充 S2 引用数据，生成会议专题推荐报告到 `10_Daily/`。

**前置依赖**: 已初始化的 Vault，且能读取到 `{VAULT_PATH}\paper-wiki-config.yaml`

**详细步骤**: 见 [workflows/conf/SKILL.md](workflows/conf/SKILL.md)

---

### 工作流 4: search（笔记搜索）

**触发**: `/paper-wiki search <查询>`

在已整理的论文笔记（`references/` 和 `10_Daily/`）中执行关键词搜索，按标题/内容/作者/标签匹配并排序。

**前置依赖**: 已初始化的 Vault，且能读取到 `{VAULT_PATH}\paper-wiki-config.yaml`

**详细步骤**: 见 [workflows/search/SKILL.md](workflows/search/SKILL.md)

---

### 工作流 5: sync（知识库维护）

**触发**: `/paper-wiki sync`

扫描文件变更、更新 `.manifest.json`、重建 `index.md` 索引、检查死链和孤儿页面，输出维护报告。

**前置依赖**: 已初始化的 Vault，且能读取到 `{VAULT_PATH}\paper-wiki-config.yaml`

**详细步骤**: 见 [workflows/sync/SKILL.md](workflows/sync/SKILL.md)

---

## 路径解析规则

Paper Wiki 的配置分为两层：

- **`.env`** — 环境变量，定义 Vault 路径等基础配置（位于 skill 目录 `.claude/skills/paper-wiki/.env`）
- **`paper-wiki-config.yaml`** — 业务配置，定义研究方向、关键词、评分权重等（位于 Vault 根目录）

所有工作流在执行时，按以下优先级获取 Vault 路径：

1. **环境变量 `PAPER_WIKI_VAULT_PATH`**（推荐）：从 `.env` 文件读取
2. **从业务配置回读**：读取 `{VAULT_PATH}\paper-wiki-config.yaml` 中的 `directories.vault` 字段
3. **默认回退**：如果以上都不可用，回退到 `D:\paper-wiki`

工作流执行时，先读取 `.env` 获取基础路径，再加载 `paper-wiki-config.yaml` 获取业务配置。

---

## 目录结构规范

```
{VAULT_PATH}\
├── _sources\
│   └── papers\           # 论文 PDF 存放
├── references\           # 分析报告（每篇论文一个文件夹）
│   ├── index.md          # 论文索引
│   └── {Paper_Title}\    # 论文文件夹
│       ├── {Paper_Title}.md   # 分析报告
│       └── images\       # 论文图片
├── 10_Daily\            # 每日推荐
│   └── YYYY-MM-DD论文推荐.md
├── 20_Research\         # 研究核心
│   └── PaperGraph\      # 知识图谱
│       └── graph_data.json
├── concepts\            # 全局概念
├── entities\            # 人物、会议、工具
├── skills\              # 操作指南
├── synthesis\           # 综合与综述
├── journal\             # 时间戳观察
├── index.md             # 全局索引
├── log.md               # 操作日志
├── paper-wiki-config.yaml  # 配置文件
└── .manifest.json       # 来源追踪
```

---

## 与其他 Skills 的关系

| 本 Skill 命令 | 复用的 Skill | 复用内容 |
|--------------|-------------|---------|
| `daily` | start-my-day | search_arxiv.py, scan_existing_notes.py, link_keywords.py |
| `analyze` | paper-analyze | generate_note.py, update_graph.py |
| `analyze` | extract-paper-images | extract_images.py |
| `conf` | conf-papers | search_conf_papers.py |
| `search` | paper-search | grep 搜索逻辑 |
| `sync` | llm-wiki | manifest 管理、索引重建、死链检测 |

---

## 重要规则

1. **Vault 路径可配置**: 首次初始化时询问用户，后续从配置文件中读取
2. **PDF 统一存放**: 所有下载的 PDF 放在 `{VAULT_PATH}\_sources\papers/`
3. **分析报告统一存放**: 所有分析笔记放在 `{VAULT_PATH}\references/`
4. **使用 wikilink**: 所有内部链接使用 `[[File_Name|Display Title]]` 格式
5. **图片使用 wikilink 嵌入**: `![[filename.png|800]]`，禁止使用 URL 编码路径
6. **Frontmatter 引号**: 所有字符串值必须用双引号包围
7. **语言一致**: 根据 `paper-wiki-config.yaml` 中的 `language` 设置选择输出语言
8. **自动关键词生成**: 初始化时由系统自动分析生成关键词，用户确认或修改
9. **配置文件单一**: 所有配置集中在 `{VAULT_PATH}\paper-wiki-config.yaml`
10. **不覆盖手动笔记**: 分析时如检测到已有笔记，询问用户是否覆盖
