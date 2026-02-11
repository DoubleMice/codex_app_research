# IPC 消息路由实现分析

## 概述

Codex App 使用 Electron IPC 机制实现主进程与渲染进程之间的通信。

## IPC 通道定义

### 核心通道
```javascript
// 行号: 36946-36951
h7 = "codex_desktop:message-from-view"    // 渲染进程 -> 主进程
bt = "codex_desktop:message-for-view"     // 主进程 -> 渲染进程
g0 = "codex_desktop:show-context-menu"    // 右键菜单
g7 = "codex_desktop:get-sentry-init-options"  // Sentry 配置
v7 = "codex_desktop:get-build-flavor"     // 构建类型
_7 = "codex_desktop:trigger-sentry-test"  // Sentry 测试
```

### Worker 通道
```javascript
// 行号: 36957-36960
function T7(t) {
  return `codex_desktop:worker:${t}:from-view`;
}
function x7(t) {
  return `codex_desktop:worker:${t}:for-view`;
}
```

## electronBridge API

### Preload 脚本暴露的 API
```javascript
// preload.js
const R = {
  windowType: "electron",
  sendMessageFromView: async (e) => {
    await n.ipcRenderer.invoke(f, e)
  },
  getPathForFile: (e) => {
    return n.webUtils.getPathForFile(e)
  },
  sendWorkerMessageFromView: async (e, t) => {
    await n.ipcRenderer.invoke(M(e), t)
  },
  subscribeToWorkerMessages: (e, t) => {
    // 订阅 Worker 消息
  },
  showContextMenu: async (e) => {
    return n.ipcRenderer.invoke(g, e)
  },
  triggerSentryTestError: async () => {
    await n.ipcRenderer.invoke(w)
  },
  getSentryInitOptions: () => _,
  getAppSessionId: () => _.codexAppSessionId,
  getBuildFlavor: () => x
}
```

### Context Bridge 暴露
```javascript
n.contextBridge.exposeInMainWorld("codexWindowType", "electron");
n.contextBridge.exposeInMainWorld("electronBridge", R);
```

## 消息订阅机制

### Worker 消息订阅
```javascript
subscribeToWorkerMessages: (workerId, callback) => {
  // 使用 Map 存储订阅者
  let subscribers = r.get(workerId);
  if (!subscribers) {
    subscribers = new Set();
    r.set(workerId, subscribers);
  }
  // 注册 IPC 监听器
  n.ipcRenderer.on(p(workerId), handler);
  subscribers.add(callback);
  // 返回取消订阅函数
  return () => { /* cleanup */ };
}
```

### 主进程消息分发
```javascript
// 行号: 36946-36947
// 渲染进程发送: codex_desktop:message-from-view
// 主进程响应: codex_desktop:message-for-view
n.ipcRenderer.on(v, (e, t) => {
  window.dispatchEvent(new MessageEvent("message", { data: t }));
});
```

## 主进程 IPC 处理器

### Sentry 相关
```javascript
// 行号: 71262-71268
L.ipcMain.on(g7, (t) => {
  if (!Ji(t)) {
    t.returnValue = null;
    return;
  }
  t.returnValue = ma;  // Sentry 配置
});
```

### 更新检查
```javascript
// 行号: 59706
L.ipcMain.handle("codex_desktop:check-for-updates", async (l) => {
  this.options.isTrustedIpcEvent(l) && s();
});
```

### 右键菜单
```javascript
// 行号: 37177
L.ipcMain.handle(g0, n);  // show-context-menu
```

## 关键代码位置

| 功能 | 行号 | 说明 |
|------|------|------|
| 通道定义 | 36946-36951 | IPC 通道常量 |
| Worker 通道 | 36957-36960 | 动态 Worker 通道 |
| 右键菜单 | 37177 | 上下文菜单处理 |
| Sentry IPC | 71262-71268 | Sentry 配置获取 |
| 更新检查 | 59706 | Sparkle 更新 |

## 总结

IPC 消息路由的核心设计：
1. **双向通道** - from-view/for-view 模式
2. **Worker 隔离** - 每个 Worker 独立通道
3. **Context Bridge** - 安全暴露 API
4. **事件分发** - 使用 MessageEvent 转发
