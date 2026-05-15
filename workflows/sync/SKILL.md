---
name: paper-wiki-sync
description: Paper Wiki 知识库维护工作流。扫描文件变更、更新 manifest、重建索引、检查死链和孤儿页面，输出维护报告。触发词：/paper-wiki sync
---

# Paper Wiki — 知识库维护（Sync）

## 触发条件

当用户输入 `/paper-wiki sync` 时执行。

---

## 前置依赖

- 已初始化的 Vault（存在 `{VAULT_PATH}\paper-wiki-config.yaml` 和 `{VAULT_PATH}\.manifest.json`）

---

## 路径获取

执行本工作流前，先读取环境变量：

1. 读取 skill 目录下的 `.env` 文件，获取 `PAPER_WIKI_VAULT_PATH`
2. 从 `{VAULT_PATH}\paper-wiki-config.yaml` 中确认配置一致性
3. 如果 `.env` 和配置文件都找不到，提示用户先运行 `/paper-wiki` 进行初始化

---

## 执行步骤

### 步骤 1: 扫描文件变更

扫描 `{VAULT_PATH}` 下所有 `.md` 文件（包括 `projects/` 子目录中的项目级笔记），对比 `{VAULT_PATH}\.manifest.json` 记录。

扫描范围：
- 全局目录：`10_Daily/`、`references/`、`concepts/`、`entities/`、`synthesis/`、`journal/`、`_sources/papers/`
- 项目目录：`projects/*/`（包含 `methods/`、`journal/`、`experiments/` 等子目录）

### 步骤 2: 更新 manifest

更新 `{VAULT_PATH}\.manifest.json` 中的 pages 列表。

### 步骤 3: 重建索引

重建 `{VAULT_PATH}\index.md`：
- 统计各目录文件数量（含 `projects/` 下的项目级内容）
- 列出最近更新的笔记
- 列出高频标签
- 汇总各项目的方法、日志、实验记录数量

### 步骤 4: 检查死链

检查所有 wikilink 是否指向存在的文件。

检查范围包括：
- 全局笔记中的 wikilink
- 项目级笔记（`projects/{project_name}/**/*.md`）中的 wikilink
- 跨项目/全局的 wikilink（如 `[[projects/fl-backdoor/methods/rfcba]]` 指向全局路径）

### 步骤 5: 检查孤儿页面

报告无入链的页面。

分别统计：
- 全局孤儿页面（无全局或其他页面引用）
- 项目内孤儿页面（无项目内或其他页面引用）

### 步骤 6: 输出维护报告

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

## 输出

- 更新后的 `{VAULT_PATH}\.manifest.json`
- 更新后的 `{VAULT_PATH}\index.md`
- 维护报告（含死链和孤儿页面统计）
