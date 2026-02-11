# OAuth 认证流程实现分析

## 概述

Codex App 支持多种认证方式：ChatGPT OAuth、API Key 和 MCP OAuth。

## 认证方式

### ChatGPT OAuth
```
ChatGPT OAuth managed by Codex (tokens persisted and refreshed by Codex).
```

### API Key
```
OpenAI API key provided by the caller and stored by Codex.
```

### 认证状态
```typescript
type AuthStatus =
  | "notLoggedIn"
  | "bearerToken"
  | ...
```

## CLI 登录命令

### 登录选项
```
codex login [OPTIONS]
  --with-api-key     # 从 stdin 读取 API Key
  --device-auth      # 使用设备授权流程
  --issuer-base-url  # 自定义 OAuth issuer
  --client-id        # 自定义 OAuth client ID
```

### 登录流程
```
API key login is disabled. Use ChatGPT login instead.
ChatGPT login is disabled. Use API key login instead.
OAuth login is only supported for streamable HTTP servers.
```

## Token 管理

### Token 刷新
```rust
// core/src/auth.rs
codex_core::auth
"Refreshing token"
```

### ChatGPT Token
```rust
// chatgpt/src/chatgpt_token.rs
struct TokenData
```

## MCP OAuth

### MCP 服务器认证
```
codex mcp login <NAME> [--scopes SCOPE,SCOPE]
codex mcp logout <NAME>
```

## 总结

OAuth 认证流程的核心设计：
1. **多认证方式** - ChatGPT OAuth / API Key
2. **Token 持久化** - 本地存储和刷新
3. **MCP OAuth** - 独立的 MCP 服务器认证
4. **设备授权** - 支持 device code 流程
