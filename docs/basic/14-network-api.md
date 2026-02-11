# 网络通信分析

## 概述

Codex App 使用多种网络通信方式与后端服务交互。

## API 端点

### OpenAI API
| 端点 | 用途 |
|------|------|
| `https://api.openai.com/auth` | 认证 |
| `https://api.openai.com/profile` | 用户信息 |

### ChatGPT 后端
| 端点 | 用途 |
|------|------|
| `https://chatgpt.com/backend-api` | ChatGPT API |

### 遥测服务
| 端点 | 用途 |
|------|------|
| `https://chat.openai.com/ces/v1/telemetry/intake` | 遥测数据 |

## WebSocket 通信

### 连接配置
- 环境变量: `CODEX_APP_SERVER_WS_URL`
- 用于实时通信

### 错误处理
- 自动重连机制
- 错误日志记录

## Skills 仓库

### 官方仓库
```
https://github.com/openai/skills.git
```

## 文档链接

### 开发者文档
- `https://developers.openai.com/codex/app`
- `https://developers.openai.com/codex/app/automations`
- `https://developers.openai.com/codex/app/local-environments`
- `https://developers.openai.com/codex/app/worktrees`
- `https://developers.openai.com/codex/skills`
- `https://developers.openai.com/codex/mcp`

## 认证机制

### OAuth 认证
- ChatGPT OAuth 登录
- Token 刷新机制

### API Key 认证
- 直接使用 OpenAI API Key
- 通过管道传入

## 总结

Codex App 的网络通信特点：

1. **多端点支持** - OpenAI API 和 ChatGPT
2. **WebSocket** - 实时通信
3. **OAuth 认证** - 安全登录
4. **遥测服务** - 使用数据收集
