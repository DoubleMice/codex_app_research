# Worker 线程任务处理分析

## 概述

Codex App 使用 Node.js Worker Threads 处理后台任务。

## Worker 初始化

### 入口代码
```javascript
// 行号: 301
const { workerId, sentryInitOptions, ... } = zu.workerData;
const Lh = zu.parentPort;
if (!Lh)
  throw new Error("worker-entry must be run in a worker thread");
```

### 消息监听
```javascript
Lh.on("message", e => {
  // 处理消息
});
```

## 消息类型

### 请求类型
```javascript
switch (e.type) {
  case "worker-sentry-user-update":
    // 更新 Sentry 用户信息
    break;
  case "worker-app-event":
    // 应用事件
    break;
  case "worker-request":
    // 工作请求
    break;
  case "worker-request-cancel":
    // 取消请求
    break;
}
```

## Git 处理器

### h8 类
```javascript
// Git 相关请求处理
class h8 extends f8 {
  async handleRequest(t) {
    switch (t.method) {
      case "stable-metadata":
      case "current-branch":
      case "create-worktree":
      case "delete-worktree":
      // ...
    }
  }
}
```

## 总结

Worker 线程任务处理的核心设计：
1. **parentPort 通信** - 主进程消息传递
2. **请求/响应模式** - worker-request/worker-response
3. **Git 操作** - 独立 Worker 处理
4. **Sentry 集成** - Worker 内错误上报
