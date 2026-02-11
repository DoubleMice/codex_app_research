# node-pty 终端模拟实现分析

## 概述

Codex App 使用 node-pty 实现伪终端模拟。

## PTY 创建

### 动态导入
```javascript
// 行号: 64483
const { spawn: p } = await import("node-pty");
```

### 创建选项
```javascript
// 行号: 64484
const d = p(c, u, {
  cols: n.cols ?? 80,
  rows: n.rows ?? 24,
  cwd: a,
  env: l
});
```

## 会话管理

### 会话数据结构
```javascript
// 行号: 64486-64497
const m = {
  id: r,
  pty: d,
  owner: e,
  ownerId: e.id,
  buffer: "",
  cwd: i,
  shell: f,
  attached: false,
  conversationId: n.conversationId,
  preserveOnOwnerDestroy: n.preserveOnOwnerDestroy ?? false,
};
```

### 会话存储
```javascript
// 行号: 64498-64506
this.sessions.set(r, m);
n.conversationId && this.sessionsByConversation.set(String(n.conversationId), r);
this.bindSessionEvents(m);
this.flushInit(m);
this.sendAttached(m);
```

## 总结

node-pty 终端模拟的核心设计：
1. **动态导入** - 按需加载 node-pty
2. **会话管理** - Map 存储会话
3. **事件绑定** - bindSessionEvents
4. **Conversation 关联** - 支持会话恢复
