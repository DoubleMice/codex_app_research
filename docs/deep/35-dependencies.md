# package.json 依赖和版本信息分析

## 概述

Codex Desktop 是基于 Electron 的桌面应用。

**应用名称**: openai-codex-electron
**产品名称**: Codex
**版本号**: 260208.1016
**构建号**: 571
**构建类型**: prod

## 1. Sparkle 更新源

```
https://persistent.oaistatic.com/codex-app-prod/appcast.xml
```

## 2. Electron 版本

**版本**: 40.0.0

### Electron 40 特性

- Chromium 134
- Node.js 22
- V8 13.4
- 改进的安全性
- 支持 Apple Silicon

## 3. 生产依赖

| 包名 | 版本 | 说明 |
|------|------|------|
| @sentry/electron | ^7.5.0 | Sentry 错误监控 |
| @sentry/node | 10.29.0 | Sentry Node.js SDK |
| @tanstack/react-form | ^1.27.7 | React 表单库 |
| better-sqlite3 | ^12.4.6 | SQLite 数据库 |
| electron-context-menu | ^4.1.1 | 上下文菜单 |
| electron-liquid-glass | 1.1.1 | 液态玻璃效果 |
| electron-squirrel-startup | ^1.0.1 | Windows 安装器 |
| immer | ^10.1.1 | 不可变状态 |
| lodash | ^4.17.21 | 工具库 |
| memoizee | ^0.4.15 | 记忆化 |
| mime-types | ^2.1.35 | MIME 类型 |
| node-pty | ^1.1.0 | 伪终端 |
| shell-env | ^4.0.1 | Shell 环境 |
| shlex | ^3.0.0 | Shell 解析 |
| smol-toml | ^1.5.2 | TOML 解析 |
| zod | ^4.1.13 | Schema 验证 |

### 工作区依赖

```
app-server-types: workspace:*
protocol: workspace:*
shared-node: workspace:*
```

## 4. 开发依赖

| 包名 | 版本 | 说明 |
|------|------|------|
| @electron-forge/cli | ^7.10.2 | Electron Forge CLI |
| @electron-forge/maker-dmg | ^7.10.2 | DMG 打包器 |
| @electron-forge/plugin-vite | ^7.10.2 | Vite 插件 |
| @electron-forge/plugin-fuses | ^7.10.2 | Fuses 插件 |
| @electron/fuses | ^1.8.0 | Electron Fuses |
| @electron/notarize | ^3.1.1 | 公证工具 |
| @sentry/cli | ^3.1.0 | Sentry CLI |
| electron | 40.0.0 | Electron 框架 |
| typescript | ^5.9.3 | TypeScript |
| vite | ^5.4.21 | Vite 构建工具 |
| vitest | ^1.6.0 | 测试框架 |
| prettier | ^3.2.5 | 代码格式化 |
| eslint | ^9.39.1 | 代码检查 |

## 5. 构建脚本

| 脚本 | 说明 |
|------|------|
| dev | 开发模式启动 |
| build | 构建 DMG |
| forge:package | 打包应用 |
| forge:make | 制作安装包 |
| forge:publish | 发布应用 |
| rebuild:sqlite | 重建 SQLite |
| build:sparkle-native | 构建 Sparkle 原生模块 |
