---
name: paper-wiki-search
description: Paper Wiki 笔记搜索工作流。在已整理的论文笔记中执行关键词搜索，支持标题、作者、内容匹配，并按相关性排序。触发词：/paper-wiki search <查询>
---

# Paper Wiki — 笔记搜索（Search）

## 触发条件

当用户输入 `/paper-wiki search <查询>` 时执行。

---

## 前置依赖

- 已初始化的 Vault（存在 `{VAULT_PATH}\paper-wiki-config.yaml`）

---

## 路径获取

执行本工作流前，先读取环境变量：

1. 读取 skill 目录下的 `.env` 文件，获取 `PAPER_WIKI_VAULT_PATH`
2. 如果 `.env` 不存在，尝试查找系统中的 `paper-wiki-config.yaml` 并读取 `directories.vault`
3. 如果都找不到，提示用户先运行 `/paper-wiki` 进行初始化

---

## 执行步骤

### 步骤 1: 解析查询

提取搜索关键词。

### 步骤 2: 执行搜索

```bash
# 在 references/* 目录中递归搜索
grep -r -i "查询关键词" "{VAULT_PATH}\references\" --include="*.md"

# 在 10_Daily/ 目录中搜索
grep -r -i "查询关键词" "{VAULT_PATH}\10_Daily\" --include="*.md"
```

### 步骤 3: 评分排序

- 标题匹配: +10
- 内容匹配: +5
- 作者匹配: +8
- 标签匹配: +3

### 步骤 4: 输出结果

```
搜索结果（按相关性排序）：

1. [[references/论文标题/论文标题|论文标题]] (相关性: 95%)
   作者: ... | 匹配位置: 标题

2. ...
```

---

## 输出

- 搜索结果列表（按相关性排序），附带匹配位置标注
