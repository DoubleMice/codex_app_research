# main-BLcwFbOH.js 逆向分析

## 概述

主进程 JS 文件是 Electron 应用的核心，负责窗口管理、IPC 通信、进程调度等功能。

**文件**: `extracted/app/.vite/build/main-BLcwFbOH.js`
**大小**: ~2.1MB
**混淆**: Vite/Rollup 打包 + Terser 压缩

## 1. 类定义列表

### 错误类

| 行号 | 类名 | 说明 |
|------|------|------|
| 9252 | Ii | 自定义错误类 |
| 9259 | Bl | 自定义错误类 |
| 27681 | Ye | 自定义错误类 |

### 核心类

| 行号 | 类名 | 说明 |
|------|------|------|
| 22978 | HU | 工具类 |
| 23205 | YU | 工具类 |
| 23774 | HH | 工具类 |
| 31091 | UZ | 数据处理类 |
| 31140 | FZ | 数据处理类 |

### 管理器类

| 行号 | 类名 | 说明 |
|------|------|------|
| 41651 | Xa | 管理器类 |
| 41913 | SJ | 管理器类 |
| 42059 | IJ | 管理器类 |
| 43535 | GI | 管理器类 |
| 43984 | RQ | 管理器类 |

## 2. IPC 处理器

### ipcMain.handle 注册

```javascript
// 行 37177: 通用 IPC 处理器注册
L.ipcMain.handle(g0, n)

// 行 59706: 更新检查
L.ipcMain.handle("codex_desktop:check-for-updates", async (l) => {...})

// 行 71579: 消息处理
L.ipcMain.handle(h7, async (t, e) => {...})

// 行 71599: 动态通道注册
L.ipcMain.handle(T7(t), async (n, r) => {...})

// 行 71603: 状态查询
L.ipcMain.handle(_7, (t) => {...})
```

## 3. 事件监听器

### 消息事件

```javascript
// 行 2504-2505: 通用消息监听
t.addEventListener("message", S, !1)

// 行 43693: Worker 消息
.on("message", () => {...})

// 行 53903: 调试器消息
t.debugger.on("message", (i, a, o) => {...})

// 行 54415-54419: 进程消息
r.on("message", (a) => {...})
s.on("message", (c) => {...})

// 行 54619: Worker 线程消息
o.on("message", (s) => {...})
```

## 4. 模块导入分析

### Electron 模块

```javascript
// 核心模块
const L = require("electron");
L.app
L.BrowserWindow
L.ipcMain
L.ipcRenderer
L.shell
L.dialog
```

### Node.js 模块

```javascript
// 文件系统
const fs = require("fs");
const path = require("path");

// 子进程
const { spawn, exec } = require("child_process");

// Worker 线程
const { Worker } = require("worker_threads");
```

## 5. 混淆特征

### 变量名混淆

- 单字母变量: `t`, `e`, `n`, `r`, `i`, `a`, `o`, `s`
- 双字母变量: `Ii`, `Bl`, `Ye`, `Xa`
- 组合变量: `HU`, `YU`, `HH`, `UZ`, `FZ`

### 函数名混淆

- 短函数名: `g0`, `h7`, `T7`, `_7`
- 类名混淆: `SJ`, `IJ`, `GI`, `RQ`

### 字符串保留

- IPC 通道名保留: `codex_desktop:*`
- 错误消息保留
- API 端点保留
