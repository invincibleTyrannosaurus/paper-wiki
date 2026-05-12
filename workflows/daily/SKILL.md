---
name: paper-wiki-daily
description: Paper Wiki 每日论文推荐工作流。从 arXiv 和 Semantic Scholar 搜索最新论文，根据用户研究方向关键词筛选并按四维度评分，生成当日推荐笔记。触发词：/paper-wiki daily
---

# Paper Wiki — 每日论文推荐（Daily）

## 触发条件

当用户输入 `/paper-wiki daily` 时执行。

---

## 前置依赖

- 已初始化的 Vault（存在 `{VAULT_PATH}\paper-wiki-config.yaml`）
- `start-my-day` skill 的 Python 脚本（search_arxiv.py、scan_existing_notes.py、link_keywords.py）

---

## 路径获取

执行本工作流前，先读取环境变量：

1. 读取 skill 目录下的 `.env` 文件，获取 `PAPER_WIKI_VAULT_PATH`
2. 从 `{VAULT_PATH}\paper-wiki-config.yaml` 中读取业务配置（关键词、分类、评分权重等）
3. 如果 `.env` 和配置文件都找不到，提示用户先运行 `/paper-wiki` 进行初始化

---

## 执行步骤

### 步骤 1: 读取配置

读取 `{VAULT_PATH}\paper-wiki-config.yaml` 获取：
- 关键词列表
- arXiv 分类
- 排除词
- 语言设置

### 步骤 2: 扫描已有笔记

```bash
cd "C:\Users\Zzl\.claude\skills\start-my-day"
python scripts/scan_existing_notes.py \
  --vault "{VAULT_PATH}" \
  --output /tmp/paper_wiki_notes_index.json
```

### 步骤 3: 搜索 arXiv 论文

```bash
cd "C:\Users\Zzl\.claude\skills\start-my-day"
python scripts/search_arxiv.py \
  --config "{VAULT_PATH}\paper-wiki-config.yaml" \
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

### 步骤 4: 生成推荐笔记

读取 `/tmp/paper_wiki_arxiv.json`，生成 `{VAULT_PATH}\10_Daily\YYYY-MM-DD论文推荐.md`

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

### 步骤 5: 自动链接关键词

```bash
cd "C:\Users\Zzl\.claude\skills\start-my-day"
python scripts/link_keywords.py \
  --index /tmp/paper_wiki_notes_index.json \
  --input "{VAULT_PATH}\10_Daily\YYYY-MM-DD论文推荐.md" \
  --output "{VAULT_PATH}\10_Daily\YYYY-MM-DD论文推荐.md"
```

### 步骤 6: 前三篇自动分析

对于评分最高的前3篇论文：
1. 检查是否已有笔记（在 `{VAULT_PATH}\references\*/` 中搜索同名文件夹）
2. 如果没有 → 调用 `extract-paper-images` 提取图片到 `{VAULT_PATH}\references\{Paper_Title}\images/`
3. 如果没有 → 调用 `paper-analyze` 生成详细报告到 `{VAULT_PATH}\references\{Paper_Title}\{Paper_Title}.md`

### 步骤 7: 更新日志

在 `{VAULT_PATH}\log.md` 追加：
```
- [YYYY-MM-DDTHH:MM:SSZ] DAILY papers=10 top_score={最高分}
```

---

## 输出

- 生成 `{VAULT_PATH}\10_Daily\YYYY-MM-DD论文推荐.md`
- 前3篇论文可能附带自动生成的分析报告和图片
