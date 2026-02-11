# MCP 协议实现分析

## 概述

Codex App 实现了 Model Context Protocol (MCP) 用于工具扩展。

## MCP 服务器

### 模块位置
```
codex-rs/mcp-server/
└── codex_mcp_server
```

### 客户端标识
```
codex-mcp-client
codex-mcp-server
```

## MCP 事件类型

### 启动事件
```
McpStartupStatus   # 启动状态
McpStartupUpdate   # 启动更新
```

### 工具调用事件
```
McpToolCallBegin   # 工具调用开始
McpToolCallError   # 工具调用错误
```

## MCP 认证状态

### 状态类型
```typescript
// McpAuthStatus.ts
type McpAuthStatus =
  | "notLoggedIn"
  | "bearerToken"
  | ...
```

## MCP 连接管理

### 连接管理器
```rust
// core/src/mcp_connection_manager.rs
codex_core::mcp_connection_manager
```

## 总结

MCP 协议实现的核心设计：
1. **独立服务器** - codex_mcp_server 模块
2. **事件驱动** - 启动/调用/错误事件
3. **OAuth 集成** - 支持认证状态管理
4. **工具扩展** - 动态工具注册
