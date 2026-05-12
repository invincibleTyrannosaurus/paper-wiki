---
name: paper-wiki-conf
description: Paper Wiki 顶会论文追踪工作流。从 DBLP 获取指定会议论文列表，用关键词筛选并补充 S2 引用数据，生成会议专题推荐报告。触发词：/paper-wiki conf <会议名> <年份>
---

# Paper Wiki — 顶会论文追踪（Conf）

## 触发条件

当用户输入 `/paper-wiki conf <会议名> <年份>` 时执行。

支持的会议：NeurIPS, ICML, ICLR, CCS, USENIX Security, AAAI, CVPR, ICCV, ECCV

---

## 前置依赖

- 已初始化的 Vault（存在 `{VAULT_PATH}\paper-wiki-config.yaml`）
- `conf-papers` skill 的 Python 脚本（search_conf_papers.py）

---

## 路径获取

执行本工作流前，先读取环境变量：

1. 读取 skill 目录下的 `.env` 文件，获取 `PAPER_WIKI_VAULT_PATH`
2. 从 `{VAULT_PATH}\paper-wiki-config.yaml` 中读取业务配置（关键词、会议列表等）
3. 如果 `.env` 和配置文件都找不到，提示用户先运行 `/paper-wiki` 进行初始化

---

## 执行步骤

### 步骤 1: 读取配置

读取 `{VAULT_PATH}\paper-wiki-config.yaml` 获取关键词和排除词。

### 步骤 2: 查询 DBLP

```bash
cd "C:\Users\Zzl\.claude\skills\conf-papers"
python scripts/search_conf_papers.py \
  --config "{VAULT_PATH}\paper-wiki-config.yaml" \
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

### 步骤 3: 生成会议推荐笔记

生成 `{VAULT_PATH}\10_Daily\ICLR2025论文推荐.md`

### 步骤 4: 前3篇自动分析

对前3篇有 arXiv ID 的论文，询问用户是否自动分析。

---

## 输出

- `{VAULT_PATH}\10_Daily\{会议名}{年份}论文推荐.md` — 会议专题推荐报告
