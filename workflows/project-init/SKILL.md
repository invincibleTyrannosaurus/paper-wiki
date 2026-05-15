---
name: paper-wiki-project-init
description: Paper Wiki 项目级研究内容初始化工作流。询问用户是否有自研方法和代码，创建项目目录结构和模板文件。触发词：/paper-wiki project-init
---

# Paper Wiki — 项目研究内容初始化（Project Init）

## 触发条件

以下情况触发：
- 主初始化流程（工作流 0）执行到步骤 7b 时自动调用
- 用户输入 `/paper-wiki project-init`
- 用户输入 "初始化项目"、"创建项目方法"、"添加研究代码"

---

## 前置依赖

- Vault 已初始化（存在 `{VAULT_PATH}\paper-wiki-config.yaml`）
- `projects/` 目录已创建

---

## 执行步骤

### 步骤 1: 询问是否有自研方法和代码

```
你是否有正在进行的自主研究项目或已有的方法/代码需要追踪？

例如：
  - 正在开发的算法/模型
  - 实验代码仓库
  - 已跑通的基线实现
  - 导师指导的研究方向

(y/n)
```

- 输入 `n` → 跳过项目初始化，直接返回主流程
- 输入 `y` → 继续步骤 2

### 步骤 2: 收集项目信息

#### 2a. 项目名称

```
项目名称（用于目录命名）：
```

记录为 `{PROJECT_NAME}`。

#### 2b. 项目研究方向

```
项目所属的研究方向（与 Vault 全局方向的关系）：

1. 与 Vault 主方向一致（{当前 Vault 的研究方向}）
2. 子方向/细分方向
3. 跨方向/新方向

请选择（1/2/3）：
```

#### 2c. 方法信息

```
方法名称：
```

```
代码仓库路径（如有，如：D:\\MyProject、/home/user/code）：
```

```
当前方法状态：
  1. 构思中（仅有想法）
  2. 已实现（代码可运行）
  3. 实验中（已有部分结果）
  4. 待复盘（需要分析问题）
  5. 已废弃（有复盘价值）

请选择（1/2/3/4/5）：
```

记录为 `{METHOD_STATUS}`。

### 步骤 3: 创建项目目录结构

```bash
mkdir -p "{VAULT_PATH}\projects\{PROJECT_NAME}\methods"
mkdir -p "{VAULT_PATH}\projects\{PROJECT_NAME}\journal"
mkdir -p "{VAULT_PATH}\projects\{PROJECT_NAME}\experiments"
```

### 步骤 4: 创建方法模板文件

根据 `{METHOD_STATUS}` 创建对应模板：

#### 状态 1（构思中）→ 创建 `methods/{method_name}.md`

```markdown
---
title: "{method_name}"
description: "{PROJECT_NAME} 项目自研方法"
project: "{PROJECT_NAME}"
tags:
  - method
  - draft
  - {PROJECT_NAME}
status: draft
---

# {method_name}

> 项目：[[projects/{PROJECT_NAME}/index]]
> 状态：构思中
> 代码路径：`{代码仓库路径}`

---

## 1. 核心想法

（用 1-2 句话描述方法的核心 insight）

## 2. 动机

- 解决什么问题？
- 与现有方法的区别？

## 3. 方法设计

### 3.1 输入/输出

### 3.2 算法流程

```
步骤 1：...
步骤 2：...
步骤 3：...
```

### 3.3 关键组件

## 4. 预期优势

## 5. 待验证问题

- [ ] 理论可行性
- [ ] 实验可行性
- [ ] 与基线的对比

## 6. 相关论文

- 
```

#### 状态 2/3（已实现/实验中）→ 创建 `methods/{method_name}.md`

```markdown
---
title: "{method_name}"
description: "{PROJECT_NAME} 项目自研方法"
project: "{PROJECT_NAME}"
tags:
  - method
  - implemented
  - {PROJECT_NAME}
status: implemented
---

# {method_name}

> 项目：[[projects/{PROJECT_NAME}/index]]
> 状态：已实现 / 实验中
> 代码路径：`{代码仓库路径}`

---

## 1. 方法动机

## 2. 方法设计

### 2.1 核心思想

### 2.2 算法流程

### 2.3 关键超参数

```yaml
# 在此处记录关键超参数
```

## 3. 实验设置

- 数据集：
- 模型：
- 基准方法：

## 4. 实验结果

| 指标  | 基线  | {method_name} | 提升  |
| --- | --- | ------------- | --- |
|     |     |               |     |

## 5. 当前问题

- [ ] 问题 1
- [ ] 问题 2

## 6. 下一步计划

## 7. 关键代码位置

| 功能 | 文件 | 函数 |
|------|------|------|
| | | |
```

#### 状态 4（待复盘）→ 创建 `journal/{method_name}-postmortem.md`

```markdown
---
title: "{method_name} Postmortem"
description: "{method_name} 方法的问题复盘与改进方向"
project: "{PROJECT_NAME}"
tags:
  - journal
  - postmortem
  - {PROJECT_NAME}
---

# {method_name} Postmortem

> 记录时间：{当前日期}
> 方法档案：[[projects/{PROJECT_NAME}/methods/{method_name}]]
> 项目主页：[[projects/{PROJECT_NAME}/index]]

---

## 1. 核心结论

（用 1-3 句话总结方法是否可行、核心问题是什么）

## 2. 失败现象

### 2.1 观察到的现象

### 2.2 根因链

```
原因 → 结果 → 影响
```

## 3. 根因深度分析

## 4. 方法的系统性问题

## 5. 改进方向

### 方向 A
### 方向 B
### 方向 C（导师建议）

## 6. 可保留资产

| 资产 | 复用价值 | 说明 |
|------|----------|------|
| | | |

## 7. 导师反馈

### 导师原话（关键摘录）
>

### 核心问题
- [ ] 问题 1
- [ ] 问题 2

### 改进方向
- [ ] 方向 1
- [ ] 方向 2
```

#### 状态 5（已废弃）→ 同时创建 `methods/{method_name}.md` 和 `journal/{method_name}-postmortem.md`

方法文件标记 `status: deprecated`，日志文件记录复盘。

### 步骤 5: 创建项目主页

创建 `{VAULT_PATH}\projects\{PROJECT_NAME}\index.md`：

```markdown
---
title: "{PROJECT_NAME}"
category: project
tags: [{PROJECT_NAME}, research]
---

# {PROJECT_NAME}

## 项目概述

（简述项目目标、背景和当前状态）

## 知识地图

### 自研方法
- [[projects/{PROJECT_NAME}/methods/{method_name}]] — {method_name} 详细设计

### 研究日志
- [[projects/{PROJECT_NAME}/journal/{method_name}-postmortem]] — 问题复盘（如有）

### 实验记录
- *（暂无）*

## 论文管线

- [ ] Stage 0：确定研究问题
- [ ] Stage 1：文献调研
- [ ] Stage 2：方法设计
- [ ] Stage 3：实验验证
- [ ] Stage 4：论文写作
- [ ] Stage 5：审稿与修订

## Open Questions

- 
```

### 步骤 6: 更新全局索引

更新 `{VAULT_PATH}\index.md`：
- 在 "研究项目" 区域添加新项目链接
- 更新项目统计数字

### 步骤 7: 输出确认信息

```
项目初始化完成！

项目：{PROJECT_NAME}
方法：{method_name}
状态：{METHOD_STATUS}

已创建：
  projects/{PROJECT_NAME}/
  ├── index.md              — 项目主页
  ├── methods/
  │   └── {method_name}.md  — 方法设计
  ├── journal/
  │   └── {method_name}-postmortem.md — 研究日志（如适用）
  └── experiments/          — 实验记录目录

下一步：
  1. 填写方法文件中的待办项
  2. 运行实验后将结果写入 experiments/
  3. 需要复盘时更新 journal/
```

---

## 规则

1. **不覆盖已有文件**：如检测到同名项目或方法文件，询问用户是否覆盖
2. **支持多方法**：一个项目可包含多个方法文件，重复调用本工作流即可
3. **状态可迁移**：方法状态变化时（如从"构思中"到"已实现"），手动修改 frontmatter 的 `status` 字段
4. **代码路径记录**：在方法文件中记录代码路径，但不直接管理代码文件
