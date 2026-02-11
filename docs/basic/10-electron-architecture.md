# Electron 应用架构分析

## 概述

Codex App 基于 Electron 40.0.0 构建，使用 Vite 作为构建工具。

## 应用结构

```
Codex.app/Contents/
├── MacOS/Codex          # 主入口
├── Frameworks/          # 框架依赖
├── Resources/
│   ├── app.asar         # 应用代码包
│   └── codex            # CLI 二进制
└── Info.plist           # 应用配置
```

## 主进程 (Main Process)

### 入口文件
- `main.js` → `main-BLcwFbOH.js`
- 代码行数: ~71,616 行（格式化后）

### 主要依赖
```javascript
require("electron")
require("node:fs")
require("node:path")
require("child_process")
require("node:crypto")
```

## 渲染进程 (Renderer Process)

### Preload 脚本
- `preload.js` - 桥接主进程和渲染进程

### 暴露的 API (electronBridge)
```javascript
window.electronBridge = {
  windowType: "electron",
  sendMessageFromView: async (msg) => {},
  getPathForFile: (file) => {},
  sendWorkerMessageFromView: async (workerId, msg) => {},
  subscribeToWorkerMessages: (workerId, callback) => {},
  showContextMenu: async (options) => {},
  triggerSentryTestError: async () => {},
  getSentryInitOptions: () => {},
  getAppSessionId: () => ,
  getBuildFlavor: () => {}
}
```

## IPC 通信机制

### 通信通道
| 通道名称 | 方向 | 用途 |
|---------|------|------|
| `codex_desktop:message-from-view` | 渲染→主 | 视图消息发送 |
| `codex_desktop:message-for-view` | 主→渲染 | 视图消息接收 |
| `codex_desktop:worker:{id}:from-view` | 渲染→Worker | Worker 消息发送 |
| `codex_desktop:worker:{id}:for-view` | Worker→渲染 | Worker 消息接收 |
| `codex_desktop:show-context-menu` | 渲染→主 | 显示上下文菜单 |
| `codex_desktop:get-sentry-init-options` | 同步 | 获取 Sentry 配置 |
| `codex_desktop:get-build-flavor` | 同步 | 获取构建类型 |

## Worker 进程

### 文件结构
- `worker.js` - Worker 入口
- `worker-C_GFIvBQ.js` - Worker 主逻辑 (~726KB)

### Worker 依赖
```javascript
require("node:worker_threads")
require("node:fs")
require("node:path")
require("child_process")
require("node:crypto")
require("node:child_process")
require("node:buffer")
require("node:os")
require("node:net")
require("node:fs/promises")
```

## 原生模块 (Native Modules)

### 模块列表
| 模块 | 路径 | 用途 |
|------|------|------|
| better-sqlite3 | `node_modules/better-sqlite3/` | SQLite 数据库 |
| node-pty | `node_modules/node-pty/` | 终端模拟 |
| electron-liquid-glass | `node_modules/electron-liquid-glass/` | macOS 毛玻璃效果 |
| sparkle | `native/sparkle.node` | 自动更新 (Sparkle) |

### 平台支持
- better-sqlite3: darwin-arm64, darwin-x64
- node-pty: darwin-arm64 (ABI 143)
- electron-liquid-glass: darwin-arm64, darwin-x64

## 构建配置

### Vite 配置
- 构建工具: Vite
- 输出目录: `.vite/build/`
- Source Maps: 启用 (`.map` 文件)

### 主要构建产物
| 文件 | 大小 | 说明 |
|------|------|------|
| main-BLcwFbOH.js | ~2.5MB | 主进程代码 |
| worker-C_GFIvBQ.js | ~726KB | Worker 代码 |
| preload.js | ~2KB | Preload 脚本 |

## 安全配置

### Context Isolation
- 启用 contextIsolation
- 使用 contextBridge 暴露 API
- 渲染进程无法直接访问 Node.js

### 监控集成
- Sentry 错误监控
- 应用会话 ID 追踪
- 构建类型标识 (Build Flavor)
