# 硬编码 URL 和 API 端点分析

## 概述

Codex Desktop 和 CLI 中包含多个硬编码的 URL 和 API 端点。

## 1. OpenAI API 端点

### 核心 API

| URL | 说明 |
|-----|------|
| `https://api.openai.com/v1` | OpenAI API 基础端点 |
| `https://api.openai.com/auth` | 认证端点 |
| `https://api.openai.com/profile` | 用户配置端点 |
| `https://chatgpt.com/backend-api` | ChatGPT 后端 API |
| `https://chatgpt.com/backend-api/codex` | Codex 专用后端 |

### 认证相关

| URL | 说明 |
|-----|------|
| `https://auth.openai.com` | OAuth 认证服务 |
| `https://platform.openai.com` | 平台入口 |
| `https://platform.api.openai.org` | 平台 API |

## 2. 遥测和监控

### Sentry

```javascript
// Sentry DSN
"https://17de4aef18bcd467f0a250b004f16737@o33249.ingest.us.sentry.io/4510507758977024"
```

### OpenTelemetry

| URL | 说明 |
|-----|------|
| `https://ab.chatgpt.com/otlp/v1/metrics` | OTLP 指标端点 |
| `http://localhost:4317` | 本地 OTLP gRPC |
| `http://localhost:4318` | 本地 OTLP HTTP |

### 遥测

```javascript
const rY = "https://chat.openai.com/ces/v1/telemetry/intake"
```

## 3. 文档和帮助

| URL | 说明 |
|-----|------|
| `https://developers.openai.com/codex/app` | 应用文档 |
| `https://developers.openai.com/codex/app/automations` | 自动化文档 |
| `https://developers.openai.com/codex/app/local-environments` | 本地环境文档 |
| `https://developers.openai.com/codex/app/worktrees` | Worktree 文档 |
| `https://developers.openai.com/codex/skills` | Skills 文档 |
| `https://developers.openai.com/codex/mcp` | MCP 文档 |
| `https://developers.openai.com/codex/app/troubleshooting` | 故障排除 |

## 4. GitHub 相关

### Skills 仓库

```javascript
j9 = "https://github.com/openai/skills.git"
```

### GitHub API

| URL | 说明 |
|-----|------|
| `https://api.github.com/repos/{repo}/contents/{path}` | 内容 API |
| `https://codeload.github.com/{owner}/{repo}/zip/{ref}` | 下载 ZIP |
| `https://github.com/{repo}/tree/{ref}/{path}` | 仓库浏览 |

### GitHub Copilot MCP

```
url: "https://api.githubcopilot.com/mcp/"
```

## 5. 更新检查

### Sparkle (macOS)

```
https://persistent.oaistatic.com/codex-app-prod/appcast.xml
```

### Homebrew

```
https://formulae.brew.sh/api/cask/codex.json
```

### GitHub Releases

```
https://api.github.com/repos/openai/codex/releases/latest
```

## 6. CDN 和静态资源

| URL | 说明 |
|-----|------|
| `https://cdn.openai.com` | OpenAI CDN |
| `https://ab.chatgpt.com` | A/B 测试服务 |

## 7. 用户界面链接

### 设置和管理

| URL | 说明 |
|-----|------|
| `https://chatgpt.com/#settings` | 设置页面 |
| `https://chatgpt.com/codex/settings/usage` | 使用量设置 |
| `https://chatgpt.com/apps/` | 应用管理 |

### 反馈

| URL | 说明 |
|-----|------|
| `https://github.com/openai/codex/issues/new?template=2-bug-report.yml` | Bug 报告 |
| `https://github.com/openai/codex` | 项目主页 |
| `http://go/codex-feedback-internal` | 内部反馈 |

## 8. 第三方服务

### Google

```javascript
// Chrome 扩展更新
const p = `https://clients2.google.com/service/update2/crx?response=redirect&acceptformat=crx2,crx3&x=id%3D${o}%26uc&prodversion=${process.versions.chrome}`

// 搜索
const h = new URL("https://www.google.com/search")
```

### JSON Schema

```javascript
c.$schema = "https://json-schema.org/draft/2020-12/schema"
```

## 9. 本地服务

| URL | 说明 |
|-----|------|
| `http://localhost:` | 本地登录服务器 |
| `http://localhost:4317` | OTLP gRPC |
| `http://localhost:4318` | OTLP HTTP |

## 10. CSP 允许的域名

```
connect-src 'self' https://ab.chatgpt.com https://cdn.openai.com
```

## 11. 产品公告

```
https://openai.com/index/introducing-gpt-5-2-codex
```

## 12. 文档引用

### Starlark 文档

```
https://github.com/bazelbuild/starlark/blob/master/spec.md#*
```

### Python 文档

```
https://docs.python.org/3.9/library/stdtypes.html#str.removeprefix
https://docs.python.org/3.9/library/stdtypes.html#str.removesuffix
```

### Chromium 沙箱参考

```
https://source.chromium.org/chromium/chromium/src/+/main:sandbox/policy/mac/common.sb
https://source.chromium.org/chromium/chromium/src/+/main:sandbox/policy/mac/renderer.sb
https://source.chromium.org/chromium/chromium/src/+/main:sandbox/policy/mac/network.sb
```

## 13. 错误和帮助链接

| URL | 说明 |
|-----|------|
| `https://stuk.github.io/jszip/documentation/howto/read_zip.html` | JSZip 文档 |
| `https://github.com/ollama/ollama?tab=readme-ov-file#ollama` | Ollama 安装 |
| `https://docs.rs/rustls/latest/rustls/manual/_03_howto/index.html#unexpected-eof` | Rustls 文档 |
| `https://docs.rs/getrandom#nodejs-es-module-support` | getrandom 文档 |
| `https://github.com/nodejs/require-in-the-middle/issues` | Node.js 问题 |
| `https://github.com/clap-rs/clap/issues` | Clap 问题 |
| `https://github.com/Aleph-Alpha/ts-rs` | ts-rs 生成器 |

## 14. GitHub Token 设置

```
https://github.com/settings/personal-access-tokens
```

## 15. URL 分类汇总

| 类别 | 数量 | 说明 |
|------|------|------|
| OpenAI API | 5 | 核心 API 端点 |
| 认证 | 3 | OAuth 和平台认证 |
| 遥测 | 4 | Sentry/OTLP |
| 文档 | 7 | 开发者文档 |
| GitHub | 5 | Skills 和 API |
| 更新 | 3 | 版本检查 |
| CDN | 2 | 静态资源 |
| 用户界面 | 4 | 设置和反馈 |
| 第三方 | 3 | Google 等 |
| 本地 | 3 | localhost |

