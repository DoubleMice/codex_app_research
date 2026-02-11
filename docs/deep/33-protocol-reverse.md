# App Server 协议消息格式逆向分析

## 概述

App Server Protocol 是 Codex Desktop 与 CLI 之间的通信协议，基于 JSON-RPC 格式。

**协议版本**: v1, v2
**传输层**: WebSocket, stdio

## 1. 请求类型 (ClientRequest)

### 会话管理

| 类型 | 说明 |
|------|------|
| Initialize | 初始化会话 |
| SessionConfigured | 会话配置完成 |
| Shutdown | 关闭会话 |

### Thread 操作

| 类型 | 说明 |
|------|------|
| ThreadList | 列出线程 |
| ThreadRead | 读取线程 |
| ThreadFork | 分叉线程 |
| ThreadRollback | 回滚线程 |
| SetThreadName | 设置线程名 |
| Compact | 压缩上下文 |

### Turn 操作

| 类型 | 说明 |
|------|------|
| TurnStart | 开始 Turn |
| TurnSteer | 引导 Turn |
| TurnAbort | 中止 Turn |

### 审批操作

| 类型 | 说明 |
|------|------|
| ExecApproval | 执行审批 |
| PatchApproval | 补丁审批 |
| ResolveElicitation | 解决引出 |
| UserInputAnswer | 用户输入回答 |

### 配置操作

| 类型 | 说明 |
|------|------|
| ConfigRead | 读取配置 |
| ConfigRequirementsRead | 读取配置要求 |

### 认证操作

| 类型 | 说明 |
|------|------|
| LoginApiKey | API Key 登录 |
| LoginChatGPT | ChatGPT 登录 |
| GetAccount | 获取账户信息 |
| Logout | 登出 |

### 工具操作

| 类型 | 说明 |
|------|------|
| ListMcpTools | 列出 MCP 工具 |
| RefreshMcpServers | 刷新 MCP 服务器 |
| ListModels | 列出模型 |

## 2. 通知类型 (ServerNotification)

### Turn 事件

| 类型 | 说明 |
|------|------|
| TurnStartedEvent | Turn 开始 |
| TurnCompleteEvent | Turn 完成 |
| TurnAbortedEvent | Turn 中止 |

### 消息事件

| 类型 | 说明 |
|------|------|
| AgentMessageEvent | Agent 消息 |
| AgentMessageDeltaEvent | Agent 消息增量 |
| UserMessageEvent | 用户消息 |

### 执行事件

| 类型 | 说明 |
|------|------|
| ExecCommandBeginEvent | 命令执行开始 |
| ExecCommandEndEvent | 命令执行结束 |
| ExecCommandOutputDeltaEvent | 命令输出增量 |

### 审批请求事件

| 类型 | 说明 |
|------|------|
| ExecApprovalRequestEvent | 执行审批请求 |
| ApplyPatchApprovalRequestEvent | 补丁审批请求 |
| ElicitationRequestEvent | 引出请求 |
| RequestUserInputEvent | 用户输入请求 |

### MCP 事件

| 类型 | 说明 |
|------|------|
| McpStartupUpdateEvent | MCP 启动更新 |
| McpStartupCompleteEvent | MCP 启动完成 |
| McpToolCallBeginEvent | MCP 工具调用开始 |
| McpToolCallEndEvent | MCP 工具调用结束 |

## 3. codex_ 前缀字段

```
codex_elicitation        # 引出请求
codex_mcp_tool_call_id   # MCP 工具调用 ID
codex_event_id           # 事件 ID
codex_call_id            # 调用 ID
codex_command            # 命令
codex_cwd                # 当前工作目录
codex_reason             # 原因
codex_changes            # 变更
codex/event              # 事件通道
```

## 4. 参数结构体

### InitializeParams

```rust
struct InitializeParams with 2 elements
```

### TurnStartParams

```rust
struct TurnStartParams with 11 elements
```

### ThreadForkParams

```rust
struct ThreadForkParams with 10 elements
```

### ThreadListParams

```rust
struct ThreadListParams with 6 elements
```

## 5. 错误类型

```
ContextWindowExceeded      # 上下文窗口超出
ThreadNotFound             # 线程未找到
AgentLimitReached          # Agent 限制达到
UsageLimitReached          # 使用限制达到
ResponseStreamFailed       # 响应流失败
InternalServerError        # 内部服务器错误
SandboxLandlock            # 沙箱 Landlock 错误
RefreshTokenFailed         # 刷新令牌失败
TransportClosed            # 传输关闭
```
