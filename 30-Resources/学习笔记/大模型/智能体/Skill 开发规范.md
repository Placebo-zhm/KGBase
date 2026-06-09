# Skill 开发规范

## 使用目标

Skill 是给智能体使用的能力模块。它不是面向人的完整教程，而是让智能体在特定任务中快速获得必要流程、约束、工具和领域上下文。

这篇笔记用于沉淀 Skill 开发时的规范，重点回答：

- 什么内容适合做成 Skill。
- 一个 Skill 应该包含哪些文件。
- `SKILL.md` 应该怎么写。
- 什么时候把内容拆到 `scripts`、`references`、`assets`。
- 开发完成后如何校验和迭代。

## Skill 适合解决什么问题

适合做成 Skill 的场景：

- 某类任务有稳定、可复用的操作流程。
- 某个工具、文件格式、业务系统或 API 有特殊使用规则。
- 任务需要反复读取同一批领域知识、表结构、接口说明或公司规范。
- 任务中存在容易出错的步骤，需要固定脚本或明确约束。
- 智能体单靠通用能力能做，但每次都要重新摸索，成本较高。

不适合做成 Skill 的场景：

- 只是一次性的临时提示词。
- 内容主要是给人看的教程、说明书或项目复盘。
- 规则还不稳定，缺少可复用流程。
- 只是堆放大量资料，没有明确的触发场景和执行方法。

判断标准：

```text
如果未来会多次让智能体做同一类事，并且希望它按稳定方式执行，就适合做 Skill。
```

## 标准目录结构

一个 Skill 是一个独立文件夹，最小结构如下：

```text
skill-name/
  SKILL.md
```

推荐结构如下：

```text
skill-name/
  SKILL.md
  agents/
    openai.yaml
  scripts/
  references/
  assets/
```

各部分职责：

- `SKILL.md`：必需。保存 Skill 的元数据和核心使用指令。
- `agents/openai.yaml`：推荐。保存面向界面展示的名称、简介和默认提示。
- `scripts/`：可选。保存需要稳定执行的脚本。
- `references/`：可选。保存需要按需读取的详细参考资料。
- `assets/`：可选。保存用于产出的模板、图片、字体、示例工程等资源。

不要把 Skill 做成一个普通项目仓库。Skill 目录里只放支持智能体完成任务所必需的内容。

## 命名规范

Skill 名称建议使用：

```text
lowercase-kebab-case
```

命名要求：

- 只使用小写字母、数字和连字符。
- 文件夹名和 Skill 名称保持一致。
- 名称尽量短，能表达动作或能力。
- 如果 Skill 强依赖某个工具，可以带工具前缀，例如 `gh-address-comments`。
- 避免泛化名称，例如 `helper`、`tools`、`agent`。

好名称示例：

```text
pdf-editor
docx-redline
github-pr-review
bigquery-analytics
frontend-webapp-builder
```

## SKILL.md 基本结构

`SKILL.md` 由两部分组成：

```markdown
---
name: skill-name
description: 描述这个 Skill 做什么，以及什么时候应该使用它。
---

# Skill Title

这里写智能体执行任务时需要遵循的流程、规则和资源使用方式。
```

### Frontmatter

Frontmatter 只放两个必需字段：

- `name`：Skill 名称。
- `description`：触发说明。

不要在 frontmatter 里扩展无关字段。智能体主要依赖 `name` 和 `description` 判断是否加载 Skill，因此 `description` 是最重要的字段。

### description 写法

`description` 要同时说明：

- 这个 Skill 能做什么。
- 什么时候应该使用。
- 典型触发任务是什么。
- 涉及哪些文件类型、系统、领域或工作流。

不要把“什么时候使用”只写在正文里。正文只有在 Skill 被触发后才会加载，触发前智能体看不到正文。

示例：

```yaml
description: Create, edit, and validate Codex skills with SKILL.md frontmatter, bundled scripts, references, assets, and validation workflow. Use when building a new skill, updating an existing skill, or reviewing whether a skill follows Codex skill development conventions.
```

## 正文写作原则

Skill 正文面向智能体，不是面向人类读者。写作目标是让智能体更稳定地完成任务。

核心原则：

- 保持简洁，只写智能体不知道或容易忘的内容。
- 使用命令式或流程式表达，减少背景介绍。
- 先写核心工作流，再写特殊情况。
- 用短示例替代长篇解释。
- 明确哪些资源什么时候读取。
- 避免把大量资料直接塞进 `SKILL.md`。

推荐结构：

```markdown
# Skill Name

## Workflow

1. 确认输入和目标。
2. 选择处理路径。
3. 使用脚本或参考资料。
4. 生成结果。
5. 校验结果。

## Resources

- For API details, read `references/api.md`.
- For deterministic conversion, run `scripts/convert.py`.
- For output templates, copy from `assets/template/`.

## Validation

- Run ...
- Check ...
```

## 渐进披露原则

Skill 应该按需加载信息，避免一开始占用过多上下文。

三层加载模型：

```text
元数据 name + description -> 始终可见
SKILL.md 正文              -> Skill 触发后加载
bundled resources          -> 智能体按需读取或执行
```

设计方式：

- `SKILL.md` 只放核心流程和导航。
- 详细资料放到 `references/`。
- 稳定、重复、容易出错的操作放到 `scripts/`。
- 输出时会用到的模板或素材放到 `assets/`。

当 `SKILL.md` 接近 500 行时，优先拆分到引用文件。长引用文件应在顶部写目录，方便智能体快速判断是否需要继续读取。

## 资源目录使用规范

### scripts

适合放入脚本的内容：

- 重复执行的转换、解析、生成、校验逻辑。
- 容易因为手写代码出错的操作。
- 需要稳定输出的机械步骤。

要求：

- 脚本参数要清晰。
- 脚本应能独立运行。
- 添加脚本后至少跑一次代表性测试。
- 不要把只运行一次的临时代码塞进 Skill。

### references

适合放入参考资料的内容：

- API 文档摘要。
- 数据库表结构。
- 业务术语和规则。
- 公司或项目规范。
- 大量示例和边界情况说明。

要求：

- `SKILL.md` 中必须说明什么时候读取对应引用文件。
- 避免同一内容同时出现在 `SKILL.md` 和 `references/`。
- 大文件要有目录或检索提示。

### assets

适合放入资产的内容：

- 文档模板。
- 图片、图标、字体。
- 前端样板工程。
- 输出需要复用的固定文件。

要求：

- `assets/` 里的内容通常不需要读入上下文。
- 智能体可以复制、修改或引用这些资产来生成结果。

## 开发流程

推荐按以下顺序开发 Skill：

1. 明确 Skill 要支持的具体任务。
2. 收集 3 到 5 个真实使用示例。
3. 判断哪些内容应该进入 `SKILL.md`、`scripts/`、`references/`、`assets/`。
4. 初始化 Skill 目录。
5. 编写 `SKILL.md`。
6. 添加必要的脚本、引用资料或资产。
7. 运行校验。
8. 用真实任务测试并迭代。

设计时先问：

- 用户会怎么请求这个 Skill？
- 哪些步骤每次都一样？
- 哪些步骤需要智能体根据上下文判断？
- 哪些错误最常见？
- 哪些资料只有在特定情况才需要读取？

## 自由度设计

Skill 不是越详细越好。应根据任务风险决定约束程度。

### 高自由度

适合文本处理、方案设计、分析判断等任务。

写法：

- 给原则。
- 给判断标准。
- 给几个示例。

### 中自由度

适合有推荐流程但需要按情况调整的任务。

写法：

- 给标准流程。
- 给分支条件。
- 给伪代码或命令模板。

### 低自由度

适合容易出错、需要稳定结果的任务。

写法：

- 给固定脚本。
- 限定参数。
- 明确校验步骤。
- 避免让智能体重新手写关键逻辑。

## 校验规范

开发完成后至少检查：

- `SKILL.md` 是否存在。
- Frontmatter 是否只有 `name` 和 `description`。
- Skill 名称是否符合小写连字符命名。
- `description` 是否包含明确触发条件。
- 正文是否写清楚工作流。
- 资源文件是否都能从 `SKILL.md` 中找到入口。
- 脚本是否跑过代表性测试。
- 是否存在不必要的 README、安装说明、变更日志等辅助文档。

如果环境提供校验脚本，可以运行：

```powershell
quick_validate.py path/to/skill-folder
```

或使用对应 Skill 工具链中的校验命令。

## 常见反模式

### 把 Skill 写成教程

问题：

- 大量背景知识占用上下文。
- 智能体真正执行任务时抓不到重点。

改法：

- 保留执行步骤。
- 把详细背景放到 `references/`。

### description 太短

问题：

- 智能体不知道什么时候应该触发 Skill。

改法：

- 在 `description` 中写清楚任务类型、触发场景和典型对象。

### 资源有文件但没有导航

问题：

- 智能体不知道什么时候读取哪个文件。

改法：

- 在 `SKILL.md` 中增加资源索引和读取条件。

### 所有内容都塞进 SKILL.md

问题：

- Skill 一触发就占用大量上下文。
- 与当前任务无关的信息也被加载。

改法：

- 核心流程留在 `SKILL.md`。
- 详细资料拆到 `references/`。

### 脚本没有验证

问题：

- Skill 依赖的确定性步骤本身不可靠。

改法：

- 每个脚本至少准备一个代表性输入并实际运行。

## 最小可用模板

```markdown
---
name: example-skill
description: Do a specific class of work. Use when Codex needs to handle X, Y, or Z, especially for files/systems/tasks like A and B.
---

# Example Skill

## Workflow

1. Confirm the input and expected output.
2. Choose the relevant path based on the task.
3. Read only the needed reference files.
4. Use scripts for deterministic steps.
5. Validate the result before responding.

## Resources

- Read `references/domain.md` when the task involves domain rules.
- Run `scripts/validate.py` before final output when generated files are changed.
- Use `assets/template/` when creating a new artifact.

## Validation

- Check that required files exist.
- Run the relevant script or test command.
- Summarize any validation gaps.
```

## 实践清单

创建或更新 Skill 前：

- 明确具体使用场景。
- 写出触发这个 Skill 的用户请求示例。
- 判断是否真的需要脚本、引用资料或资产。

编写 Skill 时：

- 先写 `description`。
- 再写最短可执行工作流。
- 最后补充资源导航和校验方式。

完成后：

- 运行校验。
- 用真实任务试跑。
- 删除多余文件。
- 根据失败点迭代 `SKILL.md` 或资源文件。

## 参考来源

- Codex `skill-creator` Skill 开发指南。
