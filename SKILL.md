---
name: paper-wiki
description: >
  论文知识库统一入口。整合每日论文推荐、单篇深度分析、会议追踪、笔记搜索和知识库维护，
  使用 Obsidian 作为存储后端，固定目录 D:\paper-wiki。
  触发词：/paper-wiki、论文知识库、paper wiki、初始化论文库
---

# Paper Wiki — 论文知识库

## 概述

Paper Wiki 是统一的论文知识库管理入口，整合以下功能：
- **每日论文推荐**（arXiv + Semantic Scholar）
- **单篇论文深度分析**（PDF + 图片提取 + 结构化笔记）
- **顶会论文追踪**（DBLP + S2，CVPR/ICLR/NeurIPS 等）
- **已有笔记搜索**（grep 快速搜索）
- **知识库维护**（索引重建、死链检测、manifest 更新）

**Vault 路径**: 固定使用 `D:\paper-wiki`
**PDF 存放**: `_sources/papers/`
**分析报告**: `references/`
**配置文件**: `D:\paper-wiki\paper-wiki-config.yaml`

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

当用户输入 `/paper-wiki` 且 `D:\paper-wiki` 尚未初始化时，自动进入此流程。

#### 步骤 1: 欢迎与方向询问

输出欢迎信息：
```
欢迎使用 Paper Wiki！我将帮你建立一个论文知识库。

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
  D:\paper-wiki\_sources\papers\    (论文PDF)
  D:\paper-wiki\references\          (分析报告)
  D:\paper-wiki\10_Daily\            (每日推荐)
  D:\paper-wiki\concepts\            (概念)
  D:\paper-wiki\entities\            (实体)
  D:\paper-wiki\skills\              (技能)
  D:\paper-wiki\synthesis\           (综合)
  D:\paper-wiki\journal\             (日志)
```

#### 步骤 6: 用户确认

```
确认创建？ (y/n)
```

- 输入 `n` → 返回步骤 2 重新修改
- 输入 `y` → 继续执行初始化

#### 步骤 7: 创建目录结构

```bash
mkdir -p "D:\paper-wiki\_sources\papers"
mkdir -p "D:\paper-wiki\references"
mkdir -p "D:\paper-wiki\10_Daily"
mkdir -p "D:\paper-wiki\concepts"
mkdir -p "D:\paper-wiki\entities"
mkdir -p "D:\paper-wiki\skills"
mkdir -p "D:\paper-wiki\synthesis"
mkdir -p "D:\paper-wiki\journal"
mkdir -p "D:\paper-wiki\20_Research\PaperGraph"
```

#### 步骤 8: 写入配置文件

根据用户输入生成 `D:\paper-wiki\paper-wiki-config.yaml`：

```yaml
version: "1.0"
language: "zh"

research_direction:
  name: "联邦学习后门攻击"
  description: ""
  keywords:
    - "federated learning"
    - "backdoor attack"
    - "model poisoning"
    - "Byzantine attack"
  excluded_keywords:
    - "survey"
    - "workshop"

search:
  arxiv:
    enabled: true
    categories: ["cs.LG", "cs.AI", "cs.CR"]
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
  vault: "D:\\paper-wiki"
  papers: "_sources/papers"
  references: "references"
  daily: "10_Daily"
  graph: "20_Research/PaperGraph"
```

#### 步骤 9: 创建全局索引页

创建 `D:\paper-wiki\index.md`：

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

创建 `D:\paper-wiki\log.md`：

```markdown
## Log

- [2026-05-10T00:00:00Z] INIT vault="D:\paper-wiki" direction="联邦学习后门攻击"
```

#### 步骤 11: 创建 manifest

创建 `D:\paper-wiki\.manifest.json`：

```json
{
  "version": "1.0",
  "created": "2026-05-10T00:00:00Z",
  "vault_path": "D:\\paper-wiki",
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

Vault: D:\paper-wiki
研究方向: 联邦学习后门攻击
关键词: federated learning, backdoor attack, model poisoning, Byzantine attack

目录结构:
  _sources/papers/  — 论文PDF存放
  references/       — 分析报告
  10_Daily/         — 每日推荐

下一步:
  /paper-wiki daily    — 获取今日论文推荐
  /paper-wiki analyze <arXiv ID>  — 深度分析单篇论文
  /paper-wiki conf ICLR 2025      — 追踪顶会论文
```

---

### 工作流 1: daily（每日论文推荐）

当用户输入 `/paper-wiki daily` 时执行。

#### 步骤 1: 读取配置

读取 `D:\paper-wiki\paper-wiki-config.yaml` 获取：
- 关键词列表
- arXiv 分类
- 排除词
- 语言设置

#### 步骤 2: 扫描已有笔记

```bash
cd "C:\Users\Zzl\.claude\skills\start-my-day"
python scripts/scan_existing_notes.py \
  --vault "D:\paper-wiki" \
  --output /tmp/paper_wiki_notes_index.json
```

#### 步骤 3: 搜索 arXiv 论文

```bash
cd "C:\Users\Zzl\.claude\skills\start-my-day"
python scripts/search_arxiv.py \
  --config "D:\paper-wiki\paper-wiki-config.yaml" \
  --output /tmp/paper_wiki_arxiv.json \
  --max-results 200 \
  --top-n 10
```

脚本会自动：
1. 调用 arXiv API 搜索指定分类的最近论文
2. 根据关键词和排除词筛选
3. 计算四维度评分（relevance 40% + recency 20% + popularity 30% + quality 10%）
4. 排除已有笔记的论文
5. 输出前10篇高评分论文到 JSON

#### 步骤 4: 生成推荐笔记

读取 `/tmp/paper_wiki_arxiv.json`，生成 `D:\paper-wiki\10_Daily\YYYY-MM-DD论文推荐.md`

笔记结构：

```markdown
---
keywords: [关键词1, 关键词2]
tags: ["llm-generated", "daily-paper-recommend"]
date: "YYYY-MM-DD"
---

# YYYY-MM-DD 论文推荐

## 今日概览

今日推荐的{数量}篇论文主要聚焦于**{方向1}**、**{方向2}**等前沿方向。

- **总体趋势**：{趋势总结}
- **质量分布**：评分在 {最低分}-{最高分} 之间
- **研究热点**：
  - **{热点1}**：{描述}
  - **{热点2}**：{描述}
- **阅读建议**：{建议}

---

## 推荐论文

### 1. [[Note_Filename|论文标题]]
- **作者**：[作者列表]
- **机构**：[机构]
- **链接**：[arXiv](url) | [PDF](url)
- **来源**：arXiv
- **评分**：{评分}/10
- **笔记**：{[[references/Note_Filename/Note_Filename|已有笔记]] 或 --}

**一句话总结**：{核心贡献}

**核心贡献**：
- {贡献1}
- {贡献2}

![[references/Note_Filename/images/image_filename.png|600]]

**详细报告**：[[references/Note_Filename/Note_Filename|分析报告]]

---

### 2. ...
```

#### 步骤 5: 自动链接关键词

```bash
cd "C:\Users\Zzl\.claude\skills\start-my-day"
python scripts/link_keywords.py \
  --index /tmp/paper_wiki_notes_index.json \
  --input "D:\paper-wiki\10_Daily\YYYY-MM-DD论文推荐.md" \
  --output "D:\paper-wiki\10_Daily\YYYY-MM-DD论文推荐.md"
```

#### 步骤 6: 前三篇自动分析

对于评分最高的前3篇论文：
1. 检查是否已有笔记（在 `references/*/` 中搜索同名文件夹）
2. 如果没有 → 调用 `extract-paper-images` 提取图片到 `references/{Paper_Title}/images/`
3. 如果没有 → 调用 `paper-analyze` 生成详细报告到 `references/{Paper_Title}/{Paper_Title}.md`

#### 步骤 7: 更新日志

在 `log.md` 追加：
```
- [YYYY-MM-DDTHH:MM:SSZ] DAILY papers=10 top_score={最高分}
```

---

### 工作流 2: analyze（深度分析单篇论文）

当用户输入 `/paper-wiki analyze <论文ID>` 时执行。

#### 步骤 1: 解析论文标识

支持的输入格式：
- arXiv ID: `2402.12345`
- 完整ID: `arXiv:2402.12345`
- 论文标题: `"论文标题"`
- URL: `https://arxiv.org/abs/2402.12345`

#### 步骤 2: 检查已有笔记

在 `references/*/` 目录中搜索：
```bash
grep -r "2402.12345" "D:\paper-wiki\references\" --include="*.md"
```

如果找到已有笔记，询问用户是否重新分析。

#### 步骤 3: 下载 PDF

```bash
# 创建工作目录
mkdir -p /tmp/paper_wiki_analyze

# 下载PDF
curl -L "http://cn.arxiv.org/pdf/2402.12345" -o "/tmp/paper_wiki_analyze/2402.12345.pdf"

# 下载源码包（含图片）
curl -L "http://cn.arxiv.org/e-print/2402.12345" -o "/tmp/paper_wiki_analyze/2402.12345.tar.gz"
tar -xzf "/tmp/paper_wiki_analyze/2402.12345.tar.gz" -C /tmp/paper_wiki_analyze/
```

#### 步骤 4: 提取元数据

使用 WebFetch 获取 arXiv 页面元数据：
- 标题
- 作者
- 摘要
- 发布日期
- 分类

#### 步骤 5: 提取图片

调用 `extract-paper-images` skill 提取图片：

```bash
cd "C:\Users\Zzl\.claude\skills\extract-paper-images"
python scripts/extract_images.py \
  --arxiv-id "2402.12345" \
  --source-dir "/tmp/paper_wiki_analyze" \
  --output-dir "D:\paper-wiki\references\{Paper_Title}\images"
```

提取策略（按优先级）：
1. 从 arXiv 源码包（`/tmp/paper_wiki_analyze`）的 `pics/`、`figures/` 目录提取真实论文图
2. 从源码包中的 PDF 图片文件提取
3. 从下载的 PDF 直接提取（过滤 <200px 的图标）

图片保存到 `D:\paper-wiki\references\{Paper_Title}\images\`。

#### 步骤 6: 复制 PDF 到归档

```bash
cp "/tmp/paper_wiki_analyze/2402.12345.pdf" "D:\paper-wiki\_sources\papers\2402.12345.pdf"
```

#### 步骤 7: 生成分析笔记

创建论文目录并生成分析笔记：
```bash
mkdir -p "D:\paper-wiki\references\{Paper_Title}\images"
```

生成 `D:\paper-wiki\references\{Paper_Title}\{Paper_Title}.md`

笔记 frontmatter：
```yaml
---
title: "论文标题"
domain: "联邦学习后门攻击"
tags: [tag1, tag2, tag3]
paper_id: "arXiv:2402.12345"
authors: ["作者1", "作者2"]
venue: "NeurIPS 2024"
date: "2024-02"
status: "已分析"
summary: "一句话摘要"
sources: ["_sources/papers/2402.12345.pdf"]
created: "2026-05-10T00:00:00Z"
updated: "2026-05-10T00:00:00Z"
quality_score: "8.5/10"
---
```

笔记内容结构：
1. 核心信息
2. 摘要翻译（英文原文 + 中文翻译 + 核心要点提炼）
3. 研究背景与动机
4. 研究问题
5. 方法概述（核心思想、方法框架、各模块详细说明、关键创新、数学公式）
6. 实验结果（数据集、实验设置、主要结果、消融实验、实验结果图）
7. 深度分析（研究价值评估、方法优势详解、局限性分析、适用性与场景分析）
8. 与相关论文对比（3篇对比论文，含方法对比表格和性能对比表格）
9. 技术路线定位
10. 未来工作建议
11. 综合评价（价值评分、重点关注、可借鉴点、批判性思考）
12. 相关论文（wikilink）
13. 外部资源

**格式规则**：
- 图片使用相对路径：`![[images/filename.png|800]]`（因为笔记在 `{Paper_Title}/` 文件夹内，images 是其子目录）
- wikilink 使用 `[[File_Name|Display Title]]`
- 不要用 `---` 作为无数据占位符，用 `--`

#### 步骤 8: 更新知识图谱

```bash
cd "C:\Users\Zzl\.claude\skills\paper-analyze"
python scripts/update_graph.py \
  --vault "D:\paper-wiki" \
  --paper-id "2402.12345" \
  --title "论文标题" \
  --domain "联邦学习后门攻击" \
  --score 8.5
```

#### 步骤 9: 更新索引

在 `index.md` 中更新统计信息。

#### 步骤 10: 输出摘要

```
论文分析完成！

论文: 论文标题 (arXiv:2402.12345)
笔记位置: [[references/论文标题/论文标题|分析报告]]
PDF位置: _sources/papers/2402.12345.pdf

综合评分: 8.5/10
- 创新性: 9/10
- 技术质量: 8/10
- 实验充分性: 8/10
- 写作质量: 9/10
- 实用性: 8/10
```

---

### 工作流 3: conf（顶会论文追踪）

当用户输入 `/paper-wiki conf <会议名> <年份>` 时执行。

支持的会议：NeurIPS, ICML, ICLR, CCS, USENIX Security, AAAI, CVPR, ICCV, ECCV

#### 步骤 1: 读取配置

读取 `paper-wiki-config.yaml` 获取关键词和排除词。

#### 步骤 2: 查询 DBLP

```bash
cd "C:\Users\Zzl\.claude\skills\conf-papers"
python scripts/search_conf_papers.py \
  --config "D:\paper-wiki\paper-wiki-config.yaml" \
  --conference "ICLR" \
  --year "2025" \
  --output /tmp/paper_wiki_conf.json
```

脚本会自动：
1. 调用 DBLP API 获取会议论文列表
2. 用标题关键词轻量筛选
3. 调用 S2 API 补充摘要和引用数
4. 计算三维评分（relevance 40% + popularity 40% + quality 20%）
5. 按评分排序

#### 步骤 3: 生成会议推荐笔记

生成 `D:\paper-wiki\10_Daily\ICLR2025论文推荐.md`

#### 步骤 4: 前3篇自动分析

对前3篇有 arXiv ID 的论文，询问用户是否自动分析。

---

### 工作流 4: search（笔记搜索）

当用户输入 `/paper-wiki search <查询>` 时执行。

#### 步骤 1: 解析查询

提取搜索关键词。

#### 步骤 2: 执行搜索

```bash
# 在 references/* 目录中递归搜索
grep -r -i "查询关键词" "D:\paper-wiki\references\" --include="*.md"

# 在 10_Daily/ 目录中搜索
grep -r -i "查询关键词" "D:\paper-wiki\10_Daily\" --include="*.md"
```

#### 步骤 3: 评分排序

- 标题匹配: +10
- 内容匹配: +5
- 作者匹配: +8
- 标签匹配: +3

#### 步骤 4: 输出结果

```
搜索结果（按相关性排序）：

1. [[references/论文标题/论文标题|论文标题]] (相关性: 95%)
   作者: ... | 匹配位置: 标题

2. ...
```

---

### 工作流 5: sync（知识库维护）

当用户输入 `/paper-wiki sync` 时执行。

#### 步骤 1: 扫描文件变更

扫描 `D:\paper-wiki` 下所有 `.md` 文件，对比 `.manifest.json` 记录。

#### 步骤 2: 更新 manifest

更新 `.manifest.json` 中的 pages 列表。

#### 步骤 3: 重建索引

重建 `index.md`：
- 统计各目录文件数量
- 列出最近更新的笔记
- 列出高频标签

#### 步骤 4: 检查死链

检查所有 wikilink 是否指向存在的文件。

#### 步骤 5: 检查孤儿页面

报告无入链的页面。

#### 步骤 6: 输出维护报告

```
知识库维护完成

统计:
- 总页面数: {N}
- 新增页面: {N}
- 修改页面: {N}
- 删除页面: {N}

检查:
- 死链: {N} 个
- 孤儿页面: {N} 个

索引已更新: index.md
```

---

## 目录结构规范

```
D:\paper-wiki\
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

1. **Vault 路径固定**: 始终使用 `D:\paper-wiki`，不可配置
2. **PDF 统一存放**: 所有下载的 PDF 放在 `_sources/papers/`
3. **分析报告统一存放**: 所有分析笔记放在 `references/`
4. **使用 wikilink**: 所有内部链接使用 `[[File_Name|Display Title]]` 格式
5. **图片使用 wikilink 嵌入**: `![[filename.png|800]]`，禁止使用 URL 编码路径
6. **Frontmatter 引号**: 所有字符串值必须用双引号包围
7. **语言一致**: 根据 `paper-wiki-config.yaml` 中的 `language` 设置选择输出语言
8. **自动关键词生成**: 初始化时由系统自动分析生成关键词，用户确认或修改
9. **配置文件单一**: 所有配置集中在 `paper-wiki-config.yaml`
10. **不覆盖手动笔记**: 分析时如检测到已有笔记，询问用户是否覆盖
