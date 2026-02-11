# worker-C_GFIvBQ.js 逆向分析

## 概述

Worker 线程 JS 文件负责后台 Git 操作和文件系统任务。

**文件**: `extracted/app/.vite/build/worker-C_GFIvBQ.js`
**类型**: Node.js Worker Thread

## 1. Worker 初始化

```javascript
// 行 301: Worker 数据获取
const { workerId, sentryInitOptions, maxLogLevel } = workerData;

// 行 301: parentPort 消息监听
const Lh = parentPort;
Lh.on("message", e => { ... });
```

## 2. 消息处理方法

### Git 操作方法

| 方法 | 说明 |
|------|------|
| stable-metadata | 获取稳定元数据 |
| current-branch | 获取当前分支 |
| upstream-branch | 获取上游分支 |
| branch-ahead-count | 获取分支领先数 |
| default-branch | 获取默认分支 |
| base-branch | 获取基础分支 |
| recent-branches | 获取最近分支 |
| branch-changes | 获取分支变更 |
| status-summary | 获取状态摘要 |

### Worktree 操作方法

| 方法 | 说明 |
|------|------|
| create-worktree | 创建 Worktree |
| list-worktrees | 列出 Worktrees |
| delete-worktree | 删除 Worktree |
| restore-worktree | 恢复 Worktree |
| worktree-snapshot-ref | 获取快照引用 |

### Diff 操作方法

| 方法 | 说明 |
|------|------|
| staged-and-unstaged-changes | 获取暂存和未暂存变更 |
| untracked-changes | 获取未跟踪变更 |
| tracked-uncommitted-changes | 获取已跟踪未提交变更 |
| apply-changes | 应用变更 |
| commit | 提交变更 |

## 3. 核心类

### GitManager (gJ)

```javascript
// 行 295: Git 仓库管理器
class gJ {
  reposByKey = new Map;

  async getRepoRepository(t, n) { ... }
  async getWorktreeRepository(t, n) { ... }
  invalidateStableMetadata() { ... }
  invalidateUntrackedPathsCache() { ... }
}
```

### GitRepoWatcher (p8)

```javascript
// 行 296: Git 仓库监视器
class p8 {
  watchers = [];

  async start() { ... }
  dispose() { ... }
  async tryWatch(t, n) { ... }
}
```

### WorkerHandler (h8)

```javascript
// 行 297: Worker 请求处理器
class h8 {
  watchesByKey = new Map;
  inFlightRequests = new Map;
  canceledRequests = new Set;

  async handleRequest(t) { ... }
  handleCancel(t) { ... }
}
```

## 4. Git 命令执行

### 常用 Git 命令

```javascript
// 分支操作
["rev-parse", "HEAD"]
["rev-parse", "--abbrev-ref", "HEAD"]
["merge-base", "HEAD", ref]

// Worktree 操作
["worktree", "list", "--porcelain"]
["worktree", "add", "--detach", path, ref]

// Diff 操作
["diff", "--cached"]
["diff", "--numstat", ref1, ref2]

// 配置操作
["config", "--get", key]
["config", "--local", "--get", key]
```

## 5. 文件日志系统

```javascript
// 行 299-301: 文件日志配置
const vb = 10 * 1024 * 1024;  // 10MB 最大段大小
const Lb = 5;                  // 最大段数
const Db = 1e4;                // 待处理行限制
```

## 6. Sentry 集成

```javascript
// 行 301: Sentry 初始化
v8(Po, w8);  // 初始化 Sentry
D8(e, t, n); // 更新用户信息
```

## 7. 消息类型

### 入站消息

| 类型 | 说明 |
|------|------|
| worker-request | 工作请求 |
| worker-request-cancel | 取消请求 |
| worker-sentry-user-update | Sentry 用户更新 |
| worker-app-event | 应用事件 |

### 出站消息

| 类型 | 说明 |
|------|------|
| worker-response | 工作响应 |
| worker-event | 工作事件 |
