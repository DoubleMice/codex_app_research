# 工具系统分析

## 概述

Codex App 提供多种内置工具供 AI Agent 使用。

## 1. 内置工具列表

### 文件操作工具

| 工具 | 参数结构 | 说明 |
|------|----------|------|
| list_dir | ListDirArgs (4 elements) | 列出目录 |
| read_file | ReadFileArgs (5 elements) | 读取文件 |
| grep_files | GrepFilesArgs (4 elements) | 搜索文件 |
| apply_patch | ApplyPatchToolArgs (1 element) | 应用补丁 |

### 命令执行工具

| 工具 | 参数结构 | 说明 |
|------|----------|------|
| shell | ShellToolCallParams (6 elements) | Shell 命令 |
| exec_command | ExecCommandArgs (10 elements) | 执行命令 |
| write_stdin | WriteStdinArgs (4 elements) | 写入 stdin |
| send_input | SendInputArgs (3 elements) | 发送输入 |

### 其他工具

| 工具 | 参数结构 | 说明 |
|------|----------|------|
| view_image | ViewImageArgs (1 element) | 查看图片 |
| web_search | - | 网络搜索 |
| spawn_agent | SpawnAgentArgs (2 elements) | 启动子 Agent |
| close_agent | CloseAgentArgs (1 element) | 关闭 Agent |
| get_memory | GetMemoryArgs (1 element) | 获取记忆 |

## 2. apply_patch 工具详解

### 补丁格式

```
*** Begin Patch
*** Update File: path/to/file.py
@@ def example():
- pass
+ return 123
*** End Patch
```

### 使用方式

```json
{"command": ["apply_patch", "*** Begin Patch\n..."]}
```

### 验证规则

- 首行必须是 `*** Begin Patch`
- 末行必须是 `*** End Patch`

## 3. Shell 工具

### ShellToolCallParams

```rust
struct ShellToolCallParams {
  command: String,
  cwd: String,
  timeout: Option<u64>,
  // 共 6 个字段
}
```

### 审批流程

```
exec-approval -> 用户确认 -> 执行
```

## 4. Web Search 工具

### 配置

```toml
[tools]
web_search = "live"  # 启用实时搜索
```

### 事件

| 事件 | 说明 |
|------|------|
| web_search_begin | 搜索开始 |
| WebSearchRequest | 搜索请求 |

## 5. MCP 工具扩展

### 动态工具注册

```rust
// dynamic_tools.rs
struct DynamicToolCallRequest {
  // 4 个字段
}
```

### MCP 工具调用事件

| 事件 | 说明 |
|------|------|
| McpToolCallBegin | 调用开始 |
| McpToolCallError | 调用错误 |
| item/mcpToolCall/progress | 调用进度 |

## 6. 工具注解

### ToolAnnotations

```rust
struct ToolAnnotations {
  readOnlyHint: bool,
  destructiveHint: bool,
  idempotentHint: bool,
  openWorldHint: bool,
  // 共 5 个字段
}
```

