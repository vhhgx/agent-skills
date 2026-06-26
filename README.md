# Agent Skills

本仓库维护一组面向 Codex/Agent 工作流的技能定义，当前包含 4 个 Zephyr 工程协作类 Skill。

这些 Skill 通过 `SKILL.md` 描述触发场景、执行流程、输出结构和质量要求，并通过 `agents/openai.yaml` 提供面向 OpenAI Agent 界面的展示信息和默认提示词。

## 安装

添加全部：

```bash
npx skills add vhhgx/agent-skills
```

添加单个：

```bash
npx skills add vhhgx/agent-skills --skill zephyr-repo-evidence-first
npx skills add vhhgx/agent-skills --skill zephyr-feature-spec
npx skills add vhhgx/agent-skills --skill zephyr-fe-be-handoff
npx skills add vhhgx/agent-skills --skill zephyr-eng-doc-style
```

或直接克隆后，从本地进行安装：

```bash
npx skills add E:\agent-skills\skills
```

## Skill 能力树

```text
zephyr-repo-evidence-first
  └── 强制 Agent 在回答问题或开发前先阅读源码
  └── 核对当前任务相关代码、配置、依赖、调用链与测试
  └── 只基于可定位证据输出结论
  └── 将未确认内容与已确认结论分离

zephyr-feature-spec
  └── 生成功能规格与实施计划文档
  └── 定义功能规格文档章节
  └── 编写范围、现状、规则、影响面、计划和验收标准
  └── 按项目规范确定输出目录与文件命名

zephyr-fe-be-handoff
  └── 扫描前端页面、交互、接口调用、类型和 Mock 变更
  └── 核对后端已有能力、字段差异和行为缺口
  └── 列出后端待补接口与非接口能力
  └── 定义接口交接至少包含的字段、参数、响应、权限、错误处理和联调验收

zephyr-eng-doc-style
  └── 定义、规则、解释应如何写
  └── 表格、列表、代码块怎么用
  └── 中文如何压缩
  └── 文档如何自检
```

## References 约定

`SKILL.md` 保留触发后的主流程、强制前置条件、Reference 读取规则和完成标准。细分模板、长示例、检查清单和场景规则放入 `references/`。

使用 Skill 时，Agent 必须先读取 `SKILL.md`，再按其中的 “Reference 读取规则” 只加载当前任务需要的 reference；不得为了保险一次性读取全部 reference。

## Skill 总览

| Skill | 用途 | 典型触发场景 |
| --- | --- | --- |
| `zephyr-repo-evidence-first` | 在回答、设计、修改或审查现有项目之前，先基于工作区代码、配置、依赖、调用链、测试与同类实现形成证据结论 | 新功能开发、Bug 修复、重构、接口修改、架构分析、技术方案评审 |
| `zephyr-feature-spec` | 基于已确认需求和当前工作区事实，生成功能规格与实施计划文档 | 新功能开发前设计、跨端功能拆分、需求落盘、现有功能扩展、流程改造 |
| `zephyr-fe-be-handoff` | 根据前端页面、交互、接口调用、类型、Mock 和后端现状，生成前端到后端的接口级交接文档 | 前端完成页面、Mock、API client、types 后，需要后端补齐或核对接口能力 |
| `zephyr-eng-doc-style` | 生成、改写或审查高信息密度中文工程文档 | 文档落盘、需求拆分、接口文档、架构设计、数据模型说明、权限设计、状态机说明 |

## 默认提示词

```text
Use $zephyr-repo-evidence-first to analyze current workspace code, configuration, call chains, tests, and similar implementations before drawing project conclusions.
```

```text
Use $zephyr-feature-spec to generate an evidence-based feature specification and implementation plan from confirmed requirements and current workspace facts.
```

```text
Use $zephyr-fe-be-handoff to generate a backend API handoff document from current frontend changes.
```

```text
Use $zephyr-eng-doc-style to write or review a dense Chinese engineering document for human review and agent execution.
```

## 使用方式

在支持 Codex Skill 的环境中，可通过 Skill 名称调用，也可以用中文自然语言触发。

| 需求 | 推荐 Skill |
| --- | --- |
| “先看代码再回答这个项目怎么实现” | `zephyr-repo-evidence-first` |
| “把这个需求整理成功能规格和实施计划” | `zephyr-feature-spec` |
| “根据当前前端变更生成后端接口交接文档” | `zephyr-fe-be-handoff` |
| “把这份文档压缩成高信息密度工程文档” | `zephyr-eng-doc-style` |

## 维护约定

| 文件 | 维护要求 |
| --- | --- |
| `SKILL.md` | 保持 frontmatter 只包含 `name` 和 `description`；`name` 与目录名一致；`description` 覆盖常见触发语；正文包含目标、执行步骤和质量要求 |
| `agents/openai.yaml` | `display_name` 使用可读名称；`short_description` 简洁描述用途；`default_prompt` 使用对应 Skill 名称 |
| `references/` | 存放按需读取的细分模板、长示例、检查清单和场景规则；必须由 `SKILL.md` 明确说明读取时机 |
| 新增 Skill | 放入 `skills/<skill-name>/`；至少包含 `SKILL.md`，推荐包含 `agents/openai.yaml` |

## 当前状态

| 项 | 状态 |
| --- | --- |
| Skill 数量 | 4 |
| 自动化测试 | 暂无 |
| 构建脚本 | 暂无 |
| 主要内容语言 | 中文 |
