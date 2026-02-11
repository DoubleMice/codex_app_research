# App Server Protocol 协议分析

## 概述

Codex App 使用 JSON-RPC 风格的协议进行组件间通信。

## 1. 协议版本

| 版本 | 说明 |
|------|------|
| v1 | 旧版协议 |
| v2 | 新版协议，支持动态工具 |

```rust
"dynamic tool calls require api v2"
```

## 2. 生命周期事件

### Thread 事件

| 事件 | 说明 |
|------|------|
| thread/started | 线程启动 |
| thread/name/updated | 线程名称更新 |
| thread/tokenUsage/updated | Token 使用量更新 |
| thread/compacted | 线程压缩 |
| thread/archive | 线程归档 |
| thread/unarchive | 线程取消归档 |

### Turn 事件

| 事件 | 说明 |
|------|------|
| turn/started | 回合开始 |
| turn/completed | 回合完成 |
| turn/diff/updated | Diff 更新 |
| turn/plan/updated | 计划更新 |

### Item 事件

| 事件 | 说明 |
|------|------|
| item/started | 项目开始 |
| item/completed | 项目完成 |
| item/agentMessage/delta | Agent 消息增量 |
| item/plan/delta | 计划增量 |
| item/commandExecution/outputDelta | 命令输出增量 |
| item/commandExecution/terminalInteraction | 终端交互 |
| item/fileChange/outputDelta | 文件变更增量 |
| item/mcpToolCall/progress | MCP 工具调用进度 |
| item/reasoning/summaryTextDelta | 推理摘要增量 |

### Account 事件

| 事件 | 说明 |
|------|------|
| account/updated | 账户更新 |
| account/rateLimits/updated | 速率限制更新 |
| authStatusChange | 认证状态变更 |

## 3. 请求/响应类型

### Client 请求

| 类型 | 说明 |
|------|------|
| InitializeParams | 初始化参数 |
| ThreadListParams | 线程列表 |
| ThreadReadParams | 读取线程 |
| ThreadForkParams | 分叉线程 |
| ConfigReadParams | 读取配置 |
| SkillsListParams | 技能列表 |
| GetAccountParams | 获取账户 |

### Server 响应

| 类型 | 说明 |
|------|------|
| UserInfoResponse | 用户信息 |
| AppsListResponse | 应用列表 |
| ConfigRequirementsReadResponse | 配置要求 |
| GetConversationSummaryResponse | 会话摘要 |

## 4. 数据结构

### ProfileV2

```typescript
struct ProfileV2 {
  // 用户配置文件 v2
}
```

### ThreadItem

```typescript
struct ThreadItem {
  threadId: string;
  name: string;
  // ...
}
```

### TurnStatus

```typescript
enum TurnStatus {
  started,
  completed
}
```

## 5. 特殊字段标识

### Codex 内部字段

| 字段 | 说明 |
|------|------|
| codex_event_id | 事件 ID |
| codex_call_id | 调用 ID |
| codex_command | 命令 |
| codex_cwd | 工作目录 |
| codex_reason | 原因 |
| codex_changes | 变更 |
| codex_elicitation | 引出请求 |
| codex_mcp_tool_call_id | MCP 工具调用 ID |

## 6. 传输层

### WebSocket

```javascript
// 消息格式
ws.send(JSON.stringify(message));
```

### stdio

```
// CLI 通信
stdin -> Codex CLI -> stdout
```

### SSE (Server-Sent Events)

```
// AI 响应流
OpenAI API -> SSE Stream -> Codex CLI
```

