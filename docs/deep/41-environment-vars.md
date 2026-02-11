# 环境变量定义分析

## 概述

Codex Desktop 和 CLI 使用多种环境变量进行配置。

## 1. CODEX_ 前缀变量

### 核心配置

| 变量 | 说明 |
|------|------|
| CODEX_HOME | Codex 主目录 (~/.codex) |
| CODEX_API_KEY | OpenAI API 密钥 |
| CODEX_THREAD_ID | 当前线程 ID |

### 沙箱配置

| 变量 | 说明 |
|------|------|
| CODEX_SANDBOX | 沙箱模式 |
| CODEX_SANDBOX_NETWORK_DISABLED | 禁用网络访问 |

### Cloud Tasks

| 变量 | 说明 |
|------|------|
| CODEX_CLOUD_TASKS_MODE | Cloud Tasks 模式 |
| CODEX_CLOUD_TASKS_BASE_URL | Cloud Tasks 基础 URL |
| CODEX_CLOUD_TASKS_FORCE_INTERNAL | 强制内部模式 |

### OSS 配置

| 变量 | 说明 |
|------|------|
| CODEX_OSS_BASE_URL | OSS 基础 URL |
| CODEX_OSS_PORT | OSS 端口 |

### 认证

| 变量 | 说明 |
|------|------|
| CODEX_CONNECTORS_TOKEN | 连接器令牌 |
| CODEX_GITHUB_PERSONAL_ACCESS_TOKEN | GitHub PAT |
| CODEX_REFRESH_TOKEN_URL_OVERRIDE | 刷新令牌 URL 覆盖 |

### TUI 配置

| 变量 | 说明 |
|------|------|
| CODEX_TUI_ROUNDED | 圆角 UI |
| CODEX_TUI_RECORD_SESSION | 录制会话 |
| CODEX_TUI_SESSION_LOG_PATH | 会话日志路径 |

### 调试和测试

| 变量 | 说明 |
|------|------|
| CODEX_CI | CI 环境标识 |
| CODEX_TRACE_SHORTCUT | 追踪快捷键 |
| CODEX_RS_SSE_FIXTURE | SSE 测试夹具 |
| CODEX_STARTING_DIFF | 起始 diff |

### 内部变量

| 变量 | 说明 |
|------|------|
| CODEX_INTERNAL_ORIGINATOR_OVERRIDE | 内部来源覆盖 |
| CODEX_APPLY_GIT_CFG | 应用 Git 配置 |
| CODEX_POWERSHELL_PAYLOAD | PowerShell 载荷 |
| CODEX_MANAGED_BY_NPM | NPM 管理标识 |
| CODEX_MANAGED_BY_BUN | Bun 管理标识 |

## 2. 日志配置

| 变量 | 说明 |
|------|------|
| CODEX_MAX_LOG_LEVEL | 最大日志级别 |
| RUST_LOG | Rust 日志级别 (默认: warn) |
| DEBUG | 调试模式 |
| DEBUG_FD | 调试文件描述符 |

## 3. 构建配置

| 变量 | 说明 |
|------|------|
| BUILD_FLAVOR | 构建类型 (prod/dev) |
| NODE_ENV | Node.js 环境 |
| CODEX_BUILD_NUMBER | 构建号 |
| VITEST | Vitest 测试环境 |

## 4. OpenAI 配置

| 变量 | 说明 |
|------|------|
| OPENAI_API_KEY | OpenAI API 密钥 |

## 5. OpenTelemetry 配置

| 变量 | 说明 |
|------|------|
| OTEL_EXPORTER_OTLP_ENDPOINT | OTLP 端点 |
| OTEL_EXPORTER_OTLP_TIMEOUT | OTLP 超时 |
| OTEL_EXPORTER_OTLP_COMPRESSION | OTLP 压缩 |
| OTEL_EXPORTER_OTLP_HEADERS | OTLP 头部 |
| OTEL_EXPORTER_OTLP_LOGS_ENDPOINT | 日志端点 |
| OTEL_EXPORTER_OTLP_TRACES_ENDPOINT | 追踪端点 |
| OTEL_EXPORTER_OTLP_METRICS_ENDPOINT | 指标端点 |

## 6. 云平台检测

| 变量 | 说明 |
|------|------|
| VERCEL | Vercel 环境 |
| VERCEL_REGION | Vercel 区域 |
| AWS_REGION | AWS 区域 |
| AWS_EXECUTION_ENV | AWS 执行环境 |

## 7. 系统变量

| 变量 | 说明 |
|------|------|
| PATH | 系统路径 |
| PATHEXT | 可执行扩展名 (Windows) |
| TERM | 终端类型 |
| PWD | 当前工作目录 |
| HOME | 用户主目录 |
| SHELL | 默认 Shell |

## 8. 终端清理变量

node-pty 清理的变量:

| 变量 | 说明 |
|------|------|
| TMUX | tmux 会话 |
| TMUX_PANE | tmux 窗格 |
| STY | screen 会话 |
| WINDOW | screen 窗口 |
| WINDOWID | 窗口 ID |
| TERMCAP | 终端能力 |
| COLUMNS | 列数 |
| LINES | 行数 |

## 9. 代码证据

### CODEX_HOME 默认值

```javascript
return process.env.CODEX_HOME ?? Us.join(dn.homedir(), ".codex");
```

### 日志级别

```javascript
const e = b9(process.env.CODEX_MAX_LOG_LEVEL);
const t = process.env.RUST_LOG ?? "warn";
```

### 构建类型检测

```javascript
const t = process.env.BUILD_FLAVOR;
// 或
process.env.NODE_ENV === "production"
```

### 追踪快捷键

```javascript
D5 = process.env.CODEX_TRACE_SHORTCUT != null
```

## 10. 变量分类汇总

| 类别 | 数量 | 说明 |
|------|------|------|
| CODEX_ 核心 | 3 | 主目录、API、线程 |
| CODEX_ 沙箱 | 2 | 沙箱模式配置 |
| CODEX_ Cloud | 3 | Cloud Tasks |
| CODEX_ OSS | 2 | OSS 配置 |
| CODEX_ 认证 | 3 | 令牌和认证 |
| CODEX_ TUI | 3 | TUI 配置 |
| CODEX_ 调试 | 4 | 调试和测试 |
| CODEX_ 内部 | 5 | 内部使用 |
| 日志 | 4 | 日志配置 |
| 构建 | 4 | 构建配置 |
| OTEL | 8 | OpenTelemetry |
| 云平台 | 4 | 云环境检测 |
| 系统 | 7 | 系统变量 |
| 终端清理 | 8 | PTY 清理 |

