# 错误处理机制分析

## 概述

Codex App 实现了完整的错误处理体系，包括错误分类、传播和用户提示。

## 1. 错误事件类型

| 事件 | 说明 |
|------|------|
| StreamErrorEvent | 流错误事件 |
| ErrorEvent | 通用错误事件 |
| ErrorNotification | 错误通知 |
| McpToolCallError | MCP 工具调用错误 |

## 2. 错误信息结构

### CodexErrorInfo

```rust
codex_error_info  // 错误信息载体
http_status_code  // HTTP 状态码
user_facing_hint  // 用户提示信息
```

## 3. 错误分类

### 网络错误

| 错误 | 说明 |
|------|------|
| websocket closed | WebSocket 连接关闭 |
| empty sse stream | SSE 流为空 |
| ConnectionClosed | 连接已关闭 |
| No route to host | 无法路由到主机 |

### API 错误

| 错误 | 说明 |
|------|------|
| Invalid request | 无效请求 |
| rate limit | 速率限制 |
| authentication required | 需要认证 |

### 执行错误

| 错误 | 说明 |
|------|------|
| operation not permitted | 操作不允许 |
| read-only file system | 只读文件系统 |
| timeout | 超时 |

## 4. Turn 中断机制

### TurnAbortedEvent

```rust
TurnAbortedEvent  // Turn 中断事件
codex_reason      // 中断原因
```

### 中断原因

| 原因 | 说明 |
|------|------|
| user_interrupt | 用户中断 |
| timeout | 超时 |
| error | 错误 |

## 5. 错误传播机制

### 错误传播路径

```
CLI (Rust) → App Server → Main Process → Renderer → UI
```

### HTTP 状态码转发

```
When an upstream HTTP status is available (for example, from the Responses API
or a provider), it is forwarded in `httpStatusCode` on the relevant
`codexErrorInfo` variant.
```

## 6. 配置错误处理

| 错误 | 说明 |
|------|------|
| failed to load configuration | 配置加载失败 |
| failed to parse config.toml | 配置解析失败 |
| invalid configuration | 无效配置 |
| config_write_error_code | 配置写入错误 |

