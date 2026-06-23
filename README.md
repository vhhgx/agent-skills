# Agent Skills

本仓库维护一组面向 Codex/Agent 工作流的技能定义，当前包含两个中文工程文档写作类 Skill。

这些 Skill 通过 `SKILL.md` 描述触发场景、写作规则、输出结构和质量要求，并通过 `agents/openai.yaml` 提供面向 OpenAI Agent 界面的展示信息和默认提示词。


## 安装

添加全部

```bash
npx skills add vhhgx/agent-skills
```

添加单个

```bash
npx skills add vhhgx/agent-skills --skill engineering-doc-density-writer
```

或直接克隆后，从本地进行安装


```bash
npx skills add E:\agent-skills\skills
```

## 目录结构

| 路径 | 说明 |
| --- | --- |
| `skills/engineering-doc-density-writer/` | 高信息密度中文工程文档写作 Skill |
| `skills/engineering-doc-density-writer/SKILL.md` | Skill 元信息、适用场景、写作规则、文档结构、自检要求 |
| `skills/engineering-doc-density-writer/agents/openai.yaml` | OpenAI Agent 展示名、简述、默认提示词 |
| `skills/frontend-backend-handoff-writer/` | 前后端接口交接文档生成 Skill |
| `skills/frontend-backend-handoff-writer/SKILL.md` | Skill 元信息、输入来源、输出文件、接口交接文档模板、执行步骤 |
| `skills/frontend-backend-handoff-writer/agents/openai.yaml` | OpenAI Agent 展示名、简述、默认提示词 |

## Skill 总览

| Skill | 用途 | 典型触发场景 |
| --- | --- | --- |
| `engineering-doc-density-writer` | 生成高信息密度中文工程文档，便于人类审阅和 Agent 后续编码执行 | 需求拆分、架构设计、接口文档、权限设计、状态机说明、数据模型说明、Agent 编码任务文档 |
| `frontend-backend-handoff-writer` | 根据前端变更生成后端接口补齐文档 | 前端页面、交互、mock、API client、hooks、stores、types 变更后，需要输出后端 API handoff 文档 |

## engineering-doc-density-writer

该 Skill 的目标是输出“Human First, Agent Friendly”的中文工程文档。

核心规则：

| 类型 | 要求 |
| --- | --- |
| 信息密度 | 删除解释，保留定义、规则、契约 |
| 表达方式 | 能表格表达则不用自然语言段落 |
| 代码块 | 仅用于 SQL、JSON、YAML、TypeScript interface、OpenAPI、Shell、配置和精确 Schema |
| 不确定内容 | 必须标记为“待确认” |
| 语言风格 | 避免“建议”“相关”“等等”等模糊词 |
| 文档目标 | 人类 5 分钟内可审完主文档，Agent 可按接口、字段和规则直接编码 |

推荐文档结构：

| 章节 | 内容 |
| --- | --- |
| 结论 | 3 到 5 行说明交付内容 |
| 范围 | 本期范围、非本期范围 |
| 核心定义 | 状态、权限、实体、字段、错误码 |
| 业务规则 | 只写规则，不写解释 |
| 接口契约 | 方法、路径、权限、请求、响应、调用方 |
| 数据模型 | 实体、字段、关系、索引、租户字段 |
| 页面影响 | 前端字段和接口依赖 |
| 开发顺序 | 按优先级列出任务 |
| 待确认问题 | 问题、影响、默认方案 |

默认提示词：

```text
Use $engineering-doc-density-writer to write a dense Chinese engineering document for human review and agent execution.
```

## frontend-backend-handoff-writer

该 Skill 用于在前端功能完成后，扫描当前工作区变更并生成给后端开发者或后端 Agent 使用的接口补齐文档。

重点输入来源：

| 来源 | 扫描内容 |
| --- | --- |
| Git 工作区 | `git status`、`git diff`、`git diff --cached` |
| 前端文件 | `src/pages/`、`src/views/`、`src/components/`、`src/features/`、`src/api/`、`src/services/`、`src/hooks/`、`src/stores/`、`src/types/` |
| 接口调用 | `fetch`、`axios`、`request`、`useQuery`、`useMutation`、`api.xxx`、`service.xxx`、`client.xxx` |
| 类型定义 | `interface`、`type`、zod schema、DTO、Request、Response、Params、Payload |
| Mock 数据 | mock server、fixtures、MSW handlers、硬编码演示数据 |
| 需求上下文 | 用户原始需求、后续变更、业务规则、待确认问题、前后端约定 |

默认输出路径：

```text
docs/backend-handoff/<feature-name>-api-handoff.md
```

无法判断功能名时使用：

```text
docs/backend-handoff/api-handoff-YYYY-MM-DD.md
```

文档必须包含：

| 章节 | 内容 |
| --- | --- |
| 背景 | 前端完成的功能、页面、组件和业务流程 |
| 前端已完成内容 | Page、Component、API Client、Type、Mock |
| 后端接口总览 | 优先级、方法、路径、用途、状态 |
| 接口详情 | Method、Path、调用位置、用途、优先级、状态、Request、Response、错误处理 |
| 数据模型建议 | 核心实体和字段 |
| 字段映射 | 前端字段和建议后端字段 |
| 待确认问题 | 问题、影响范围、建议默认处理 |
| 后端任务清单 | 可执行 checklist |
| 后端 Agent 开发提示 | 优先级、字段约束、复用现有机制、TODO 规则 |

默认提示词：

```text
Use $frontend-backend-handoff-writer to generate a backend API handoff document from current frontend changes.
```

## 使用方式

在支持 Codex Skill 的环境中，可通过 Skill 名称调用：

```text
Use $engineering-doc-density-writer to write a dense Chinese engineering document for human review and agent execution.
```

```text
Use $frontend-backend-handoff-writer to generate a backend API handoff document from current frontend changes.
```

也可以用中文自然语言触发，例如：

| 需求 | 推荐 Skill |
| --- | --- |
| “把这个功能整理成工程文档并落盘” | `engineering-doc-density-writer` |
| “根据当前前端变更生成后端接口补齐文档” | `frontend-backend-handoff-writer` |
| “写一份权限设计和接口契约” | `engineering-doc-density-writer` |
| “输出前后端交接文档到 docs/backend-handoff” | `frontend-backend-handoff-writer` |

## 维护约定

| 文件 | 维护要求 |
| --- | --- |
| `SKILL.md` | 保持 front matter 的 `name` 与目录名一致；`description` 应覆盖常见触发语；正文应包含目标、适用场景、执行步骤和质量要求 |
| `agents/openai.yaml` | `display_name` 使用可读名称；`short_description` 简洁描述用途；`default_prompt` 使用对应 Skill 名称 |
| 新增 Skill | 放入 `skills/<skill-name>/`；至少包含 `SKILL.md` 和可选的 `agents/openai.yaml` |

## 当前状态

| 项 | 状态 |
| --- | --- |
| Skill 数量 | 2 |
| 自动化测试 | 暂无 |
| 构建脚本 | 暂无 |
| 主要内容语言 | 中文 |
