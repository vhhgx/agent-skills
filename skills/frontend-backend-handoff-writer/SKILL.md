---
name: frontend-backend-handoff-writer
description: "Generate frontend-to-backend API handoff documents from current frontend changes, mock data, API clients, TypeScript types, and user discussion context. Use when the user asks to create a backend handoff doc, generate a backend API completion document, 根据前端变动生成后端接口补齐文档, 生成前后端交接文档, 接口补齐文档落盘, backend handoff, API handoff, scan git diff for frontend API needs, or output docs/backend-handoff/*.md after frontend pages, interactions, mocks, services, hooks, stores, or types changed."
---

# Skill: frontend-backend-handoff-writer

## 目标

当完成前端页面、交互逻辑、接口调用、mock 数据或与用户讨论后的需求变更后，自动扫描当前工作区代码变动与上下文，生成一份给后端开发者或后端 Agent 使用的接口补齐文档。

该文档必须让后端能够明确知道：

* 需要新增哪些接口
* 需要修改哪些接口
* 每个接口的用途
* 请求方法、路径、入参、出参
* 字段类型、是否必填、含义
* 前端页面在哪里调用
* 当前是否已有 mock 数据
* 后端开发优先级
* 兼容性与风险点

## 适用场景

当前端 Agent 已经完成以下任意工作时，必须使用本 Skill：

* 新增页面
* 修改页面
* 新增接口调用
* 修改接口调用
* 新增 mock 数据
* 修改 TypeScript 类型
* 新增 API client 方法
* 根据用户需求讨论调整了业务逻辑
* 前端依赖某些后端尚未实现的数据

## 输入来源

执行时应尽量收集以下信息：

1. Git 工作区变动

```bash
git status
git diff
git diff --cached
```

2. 最近修改的前端文件

重点扫描：

```text
src/pages/
src/views/
src/components/
src/features/
src/api/
src/services/
src/hooks/
src/stores/
src/types/
mock/
mocks/
```

3. 接口调用代码

识别：

```text
fetch
axios
request
useQuery
useMutation
api.xxx
service.xxx
client.xxx
```

4. 类型定义

识别：

```text
interface
type
zod schema
DTO
Request
Response
Params
Payload
```

5. Mock 数据

识别：

```text
mock data
fixtures
MSW handlers
mock server
hardcoded demo data
```

6. 与用户/Agent 的讨论结论

如果当前环境可以读取聊天记录或任务上下文，应提取：

* 用户原始需求
* 后续变更
* 已确认的业务规则
* 待确认问题
* 前后端约定

## 输出文件

默认生成：

```text
docs/backend-handoff/<feature-name>-api-handoff.md
```

如果无法判断 feature-name，则使用：

```text
docs/backend-handoff/api-handoff-YYYY-MM-DD.md
```

## 文档结构

生成的文档必须包含以下章节。

---

# Backend API Handoff: <功能名称>

## 1. 背景

说明本次前端开发完成了什么功能，涉及哪些页面、组件和业务流程。

## 2. 前端已完成内容

列出已完成的前端内容：

| 类型         | 文件 | 说明 |
| ---------- | -- | -- |
| Page       |    |    |
| Component  |    |    |
| API Client |    |    |
| Type       |    |    |
| Mock       |    |    |

## 3. 后端需要补齐的接口总览

| 优先级 | 方法    | 路径               | 用途   | 状态    |
| --- | ----- | ---------------- | ---- | ----- |
| P0  | GET   | /api/example     | 获取列表 | 新增    |
| P0  | POST  | /api/example     | 创建数据 | 新增    |
| P1  | PATCH | /api/example/:id | 更新数据 | 修改/新增 |

## 4. 接口详情

每个接口必须按照以下格式输出。

---

## 4.x <接口名称>

### 基本信息

| 字段     | 内容                                |
| ------ | --------------------------------- |
| Method | GET / POST / PUT / PATCH / DELETE |
| Path   | /api/...                          |
| 前端调用位置 | 文件路径 + 函数名                        |
| 用途     | 说明该接口解决什么业务问题                     |
| 优先级    | P0 / P1 / P2                      |
| 状态     | 新增 / 修改 / 替换 mock                 |

### Request Params

适用于 query/path params。

| 字段   | 类型     | 必填 | 说明 | 示例 |
| ---- | ------ | -- | -- | -- |
| page | number | 否  | 页码 | 1  |

### Request Body

适用于 POST/PUT/PATCH。

```json
{
  "name": "example"
}
```

字段说明：

| 字段   | 类型     | 必填 | 说明 | 示例        |
| ---- | ------ | -- | -- | --------- |
| name | string | 是  | 名称 | "example" |

### Response Body

```json
{
  "code": 0,
  "message": "success",
  "data": {}
}
```

字段说明：

| 字段      | 类型     | 必填 | 说明    | 示例        |
| ------- | ------ | -- | ----- | --------- |
| code    | number | 是  | 业务状态码 | 0         |
| message | string | 是  | 提示信息  | "success" |
| data    | object | 是  | 响应数据  | {}        |

### 前端依赖说明

说明前端如何消费该接口返回值，包括：

* 哪些字段用于渲染 UI
* 哪些字段用于判断状态
* 哪些字段用于按钮禁用/显示隐藏
* 哪些字段用于分页、筛选、排序
* 哪些字段会影响后续接口调用

### 当前 Mock 数据

如果前端已有 mock，贴出 mock 数据或说明 mock 文件路径。

```json
{}
```

### 错误处理要求

说明前端期望后端返回哪些错误状态：

| 场景   | HTTP Status | code           | message |
| ---- | ----------- | -------------- | ------- |
| 参数错误 | 400         | INVALID_PARAMS | 参数错误    |
| 未登录  | 401         | UNAUTHORIZED   | 请先登录    |
| 无权限  | 403         | FORBIDDEN      | 无权限     |

### 备注

列出边界情况、兼容性要求、待确认问题。

---

## 5. 数据模型建议

汇总所有接口涉及的核心实体。

```ts
interface Example {
  id: string
  name: string
  createdAt: string
}
```

## 6. 前后端字段映射

如果前端字段名与后端字段名可能不同，需要列出映射关系。

| 前端字段      | 建议后端字段     | 说明   |
| --------- | ---------- | ---- |
| createdAt | created_at | 创建时间 |

## 7. 待确认问题

列出需要产品、人类开发者或后端 Agent 确认的问题。

| 问题 | 影响范围 | 建议默认处理 |
| -- | ---- | ------ |
|    |      |        |

## 8. 后端开发任务清单

以 checklist 形式输出。

```markdown
- [ ] 新增 GET /api/...
- [ ] 新增 POST /api/...
- [ ] 补齐参数校验
- [ ] 补齐权限校验
- [ ] 补齐分页逻辑
- [ ] 补齐错误码
- [ ] 增加接口测试
```

## 9. 给后端 Agent 的开发提示

用明确的命令式语言告诉后端 Agent 应该如何开发：

* 优先实现 P0 接口
* 严格按照 Response Body 字段返回
* 不要随意修改字段名
* 如果字段无法提供，请在实现前标记 TODO
* 保持与现有项目接口风格一致
* 复用现有 auth、pagination、error handler、validation 机制

## 质量要求

生成文档时必须遵守：

1. 不凭空编造接口。
2. 接口必须能从代码变动、mock、类型、接口调用或聊天上下文中推导出来。
3. 不确定的内容必须标记为「待确认」。
4. 所有字段必须尽量标注类型。
5. 如果已有 OpenAPI、Swagger、API convention，应优先遵循现有规范。
6. 文档必须让后端 Agent 可以直接按章节执行开发。
7. 输出应偏工程化，避免产品话术。
8. 不要只写总结，要写到接口级别。
9. 如果发现前端直接 hardcode 数据，应推导其真实后端接口需求。
10. 如果发现接口已经存在但字段不足，应标记为「修改接口」而非「新增接口」。

## 执行步骤

1. 扫描 git diff。
2. 找出新增/修改的页面、组件、hooks、services、api、types、mock。
3. 提取所有接口调用点。
4. 提取所有 request/response 类型。
5. 对比 mock 数据与真实接口调用。
6. 判断哪些接口后端尚未实现。
7. 整理每个接口的入参、出参、字段含义。
8. 标记不确定项。
9. 生成 backend handoff 文档。
10. 给出后端开发 checklist。

## 推荐命令名

```text
/backend-handoff
```

## 示例调用

```text
请根据当前工作区代码变动、前端接口调用、mock 数据和本轮需求讨论，生成后端接口补齐文档。
输出到 docs/backend-handoff/user-management-api-handoff.md。
```