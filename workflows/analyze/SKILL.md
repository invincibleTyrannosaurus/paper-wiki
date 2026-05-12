---
name: paper-wiki-analyze
description: Paper Wiki 单篇论文深度分析工作流。下载论文 PDF，提取图片，生成包含摘要翻译、方法概述、实验结果、深度分析的结构化笔记。触发词：/paper-wiki analyze <论文ID>
---

# Paper Wiki — 深度分析单篇论文（Analyze）

## 触发条件

当用户输入 `/paper-wiki analyze <论文ID>` 时执行。

---

## 前置依赖

- 已初始化的 Vault（存在 `{VAULT_PATH}\paper-wiki-config.yaml`）
- `paper-analyze` skill 的 Python 脚本（generate_note.py、update_graph.py）
- `extract-paper-images` skill 的 Python 脚本（extract_images.py）

---

## 路径获取

执行本工作流前，先读取环境变量：

1. 读取 skill 目录下的 `.env` 文件，获取 `PAPER_WIKI_VAULT_PATH`
2. 从 `{VAULT_PATH}\paper-wiki-config.yaml` 中读取业务配置（研究方向、关键词等）
3. 如果 `.env` 和配置文件都找不到，提示用户先运行 `/paper-wiki` 进行初始化

---

## 执行步骤

### 步骤 1: 解析论文标识

支持的输入格式：
- arXiv ID: `2402.12345`
- 完整ID: `arXiv:2402.12345`
- 论文标题: `"论文标题"`
- URL: `https://arxiv.org/abs/2402.12345`

### 步骤 2: 检查已有笔记

在 `{VAULT_PATH}\references\*/` 目录中搜索：
```bash
grep -r "2402.12345" "{VAULT_PATH}\references\" --include="*.md"
```

如果找到已有笔记，询问用户是否重新分析。

### 步骤 3: 下载 PDF

```bash
# 创建工作目录
mkdir -p /tmp/paper_wiki_analyze

# 下载PDF
curl -L "http://cn.arxiv.org/pdf/2402.12345" -o "/tmp/paper_wiki_analyze/2402.12345.pdf"

# 下载源码包（含图片）
curl -L "http://cn.arxiv.org/e-print/2402.12345" -o "/tmp/paper_wiki_analyze/2402.12345.tar.gz"
tar -xzf "/tmp/paper_wiki_analyze/2402.12345.tar.gz" -C /tmp/paper_wiki_analyze/
```

### 步骤 4: 提取元数据

使用 WebFetch 获取 arXiv 页面元数据：
- 标题
- 作者
- 摘要
- 发布日期
- 分类

### 步骤 5: 提取图片

调用 `extract-paper-images` skill 提取图片：

```bash
cd "C:\Users\Zzl\.claude\skills\extract-paper-images"
python scripts/extract_images.py \
  --arxiv-id "2402.12345" \
  --source-dir "/tmp/paper_wiki_analyze" \
  --output-dir "{VAULT_PATH}\references\{Paper_Title}\images"
```

提取策略（按优先级）：
1. 从 arXiv 源码包（`/tmp/paper_wiki_analyze`）的 `pics/`、`figures/` 目录提取真实论文图
2. 从源码包中的 PDF 图片文件提取
3. 从下载的 PDF 直接提取（过滤 <200px 的图标）

图片保存到 `{VAULT_PATH}\references\{Paper_Title}\images\`。

### 步骤 6: 复制 PDF 到归档

```bash
cp "/tmp/paper_wiki_analyze/2402.12345.pdf" "{VAULT_PATH}\_sources\papers\2402.12345.pdf"
```

### 步骤 7: 生成分析笔记

创建论文目录并生成分析笔记：
```bash
mkdir -p "{VAULT_PATH}\references\{Paper_Title}\images"
```

生成 `{VAULT_PATH}\references\{Paper_Title}\{Paper_Title}.md`

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

### 步骤 8: 更新知识图谱

```bash
cd "C:\Users\Zzl\.claude\skills\paper-analyze"
python scripts/update_graph.py \
  --vault "{VAULT_PATH}" \
  --paper-id "2402.12345" \
  --title "论文标题" \
  --domain "联邦学习后门攻击" \
  --score 8.5
```

### 步骤 9: 更新索引

在 `{VAULT_PATH}\index.md` 中更新统计信息。

### 步骤 10: 输出摘要

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

## 输出

- `{VAULT_PATH}\_sources\papers\{arxiv_id}.pdf` — 归档的 PDF
- `{VAULT_PATH}\references\{Paper_Title}\{Paper_Title}.md` — 分析报告
- `{VAULT_PATH}\references\{Paper_Title}\images\` — 论文配图
