# 常见结构表达方式

## 权限映射

| 权限字符 | 说明 |
| --- | --- |
| `account:view` | 查看账号 |
| `account:login` | 发起账号登录 |
| `account:reconnect` | 重连账号 |
| `account:proxy:update` | 修改账号代理 |

权限字符属于定义，必须完整保留，不得用概括性表达替代。

## 状态枚举

| 状态 | 说明 |
| --- | --- |
| `PENDING_LOGIN` | 待登录 |
| `PAIRING` | 配对中 |
| `ONLINE` | 在线 |
| `OFFLINE` | 离线 |
| `RECONNECTING` | 重连中 |
| `DISABLED` | 人工停用 |

## 状态机

禁止使用流水账描述状态流转。

| 当前状态 | 事件 | 下一状态 |
| --- | --- | --- |
| 新建 | 创建账号 | `PENDING_LOGIN` |
| `PENDING_LOGIN` | 请求二维码或配对码 | `PAIRING` |
| `PAIRING` | 登录成功 | `ONLINE` |
| `ONLINE` | 连接断开 | `OFFLINE` |
| `OFFLINE` | 开始重连 | `RECONNECTING` |
| `RECONNECTING` | 重连成功 | `ONLINE` |

异常或终止状态使用单独表表达。

## 字段映射

| 字段 | 类型 | 说明 |
| --- | --- | --- |
| `lifecycleStatus` | string | 生命周期状态 |
| `connectionStatus` | string | 连接状态 |
| `lastOnlineAt` | datetime | 最近在线时间 |
| `ownerUserId` | string | 归属用户 ID |

前后端字段名不一致时，必须明确映射。

| 前端字段 | 后端字段 | 说明 |
| --- | --- | --- |
| `createdAt` | `created_at` | 创建时间 |
| `pageSize` | `page_size` | 每页数量 |

## 接口摘要

在非接口契约文档中，只需使用接口摘要表。

| 方法 | 路径 | 用途 | 关键约束 |
| --- | --- | --- | --- |
| `GET` | `/api/accounts` | 查询账号列表 | 分页、数据范围过滤 |
| `POST` | `/api/accounts` | 创建账号 | 参数校验、审计 |
| `POST` | `/api/accounts/{id}/reconnect` | 重连账号 | 状态校验 |

完整请求、响应、错误码、幂等与并发规则应在专门接口契约文档中定义。
