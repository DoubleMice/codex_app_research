# preload.js 逆向分析

## 概述

Preload 脚本是 Electron 安全边界的关键组件，负责在渲染进程中暴露受限的 API。

**文件**: `extracted/app/.vite/build/preload.js`
**大小**: ~2KB

## 1. IPC 通道定义

```javascript
// 上下文菜单
const g = "codex_desktop:show-context-menu";

// Sentry 配置
const u = "codex_desktop:get-sentry-init-options";
const S = "codex_desktop:get-build-flavor";
const w = "codex_desktop:trigger-sentry-test";

// 消息通道
const f = "codex_desktop:message-from-view";
const v = "codex_desktop:message-for-view";

// Worker 通道生成函数
function M(e) {
  return `codex_desktop:worker:${e}:from-view`;
}
function p(e) {
  return `codex_desktop:worker:${e}:for-view`;
}
```

## 2. electronBridge API

```javascript
const R = {
  // 窗口类型
  windowType: "electron",

  // 消息发送
  sendMessageFromView: async (e) => {
    await ipcRenderer.invoke(f, e);
  },

  // 文件路径获取
  getPathForFile: (e) => {
    return webUtils.getPathForFile(e) || null;
  },

  // Worker 消息发送
  sendWorkerMessageFromView: async (e, t) => {
    await ipcRenderer.invoke(M(e), t);
  },

  // Worker 消息订阅
  subscribeToWorkerMessages: (e, t) => { ... },

  // 上下文菜单
  showContextMenu: async (e) => {
    return ipcRenderer.invoke(g, e);
  },

  // Sentry 测试
  triggerSentryTestError: async () => {
    await ipcRenderer.invoke(w);
  },

  // 配置获取
  getSentryInitOptions: () => _,
  getAppSessionId: () => _.codexAppSessionId,
  getBuildFlavor: () => x
};
```

## 3. contextBridge 暴露

```javascript
// 暴露窗口类型
contextBridge.exposeInMainWorld("codexWindowType", "electron");

// 暴露 electronBridge API
contextBridge.exposeInMainWorld("electronBridge", R);
```

## 4. 消息转发机制

```javascript
// 主进程消息转发到 window
ipcRenderer.on(v, (e, t) => {
  window.dispatchEvent(new MessageEvent("message", { data: t }));
});
```

## 5. 安全配置分析

### 暴露的 API 列表

| API | 说明 | 风险等级 |
|-----|------|----------|
| sendMessageFromView | 发送消息到主进程 | 低 |
| getPathForFile | 获取文件路径 | 低 |
| sendWorkerMessageFromView | 发送 Worker 消息 | 低 |
| subscribeToWorkerMessages | 订阅 Worker 消息 | 低 |
| showContextMenu | 显示上下文菜单 | 低 |
| getSentryInitOptions | 获取 Sentry 配置 | 低 |
| getAppSessionId | 获取会话 ID | 低 |
| getBuildFlavor | 获取构建类型 | 低 |

### 安全特性

- **contextIsolation**: 启用 (通过 contextBridge)
- **nodeIntegration**: 禁用 (未直接暴露 Node API)
- **IPC 白名单**: 仅暴露 codex_desktop:* 通道
