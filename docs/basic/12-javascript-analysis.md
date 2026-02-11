# JavaScript 代码实现分析

## 概述

主进程代码位于 `main-BLcwFbOH.js`，约 71,616 行（格式化后）。

## 文件结构

| 文件 | 大小 | 说明 |
|------|------|------|
| main-BLcwFbOH.js | ~2.5MB | 主进程代码 |
| worker-C_GFIvBQ.js | ~726KB | Worker 代码 |
| preload.js | ~2KB | Preload 脚本 |

## 窗口管理

### BrowserWindow 配置
- 使用 `windowManager.createWindow()` 创建窗口
- 支持多窗口管理
- 窗口状态追踪

### 窗口类型
- `BrowserWindowSession` - 浏览器窗口会话
- `MainProcessSession` - 主进程会话

## IPC 通信

### 主要 IPC 处理器
| 通道 | 类型 | 功能 |
|------|------|------|
| `codex_desktop:check-for-updates` | handle | 检查更新 |
| `codex_desktop:show-context-menu` | handle | 显示上下文菜单 |
| `codex_desktop:get-sentry-init-options` | sync | 获取 Sentry 配置 |

### Worker 通信
| 通道 | 方向 | 功能 |
|------|------|------|
| `codex_desktop:worker:{id}:from-view` | 渲染→Worker | 发送消息 |
| `codex_desktop:worker:{id}:for-view` | Worker→渲染 | 接收消息 |

## 数据存储

### SQLite 集成
- 使用 `better-sqlite3` 模块
- 数据库路径: `$CODEX_HOME/sqlite/`

### Automation 存储
- TOML 格式配置文件
- 路径: `$CODEX_HOME/automations/<id>/automation.toml`
- 运行状态存储在 SQLite

## 监控与日志

### Sentry 集成
- 错误监控
- 性能追踪
- 会话 ID 追踪

### 日志通道
- `scope` - 作用域日志
- `envelope` - 封装日志
- `structured-log` - 结构化日志
- `metric` - 指标日志

## 类结构

### 主要类
| 类名 | 行号 | 功能 |
|------|------|------|
| Ii | 9252 | 错误类 |
| Bl | 9259 | 错误类 |
| HU | 22978 | 工具类 |
| YU | 23205 | 工具类 |

## 自动更新

### 更新检查
- IPC 通道: `codex_desktop:check-for-updates`
- 使用 Sparkle 框架 (macOS)

## 代码混淆

### 混淆特征
- 变量名缩短 (如 L, n, r, t)
- 类名混淆 (如 Ii, Bl, HU)
- 函数名混淆

### Source Maps
- 提供 `.map` 文件
- 可用于调试和分析

## 总结

Codex App 的 JavaScript 代码具有以下特点：

1. **Vite 构建** - 使用 Vite 打包
2. **代码混淆** - 变量和类名混淆
3. **IPC 通信** - 完善的进程间通信
4. **SQLite 存储** - 本地数据持久化
5. **Sentry 监控** - 错误和性能监控
