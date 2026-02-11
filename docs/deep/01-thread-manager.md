# Thread 管理器实现分析

## 概述

Thread 是 Codex App 中管理并行任务的核心概念。

## Thread 数据结构

### 核心字段
| 字段 | 类型 | 说明 |
|------|------|------|
| threadId | string | 线程唯一标识 |
| ephemeral | boolean | 是否为临时线程 |
| turnId | string | 当前回合 ID |

## Thread 类型

### 普通 Thread
- 持久化存储
- 关联 Git Worktree
- 支持历史回溯

### Ephemeral Thread
- 临时线程，不持久化
- 用于快速任务
- 计数器: `pendingEphemeralThreadStarts`

## Worktree 集成

### 创建流程
```javascript
// 创建 worktree 路径
git worktree add --detach <path> <commit>
```

### Worktree 路径
```javascript
// 默认路径
$CODEX_HOME/worktrees/
```

### 清理机制
```javascript
// 删除 worktree
git worktree remove <path>
```

## Thread 与 Automation 关联

### 数据库关系
```sql
SELECT thread_id as threadId,
       automation_id as automationId,
       status
FROM automation_runs
```

## Turn 管理

### 中断机制
```javascript
interruptTurn({ threadId: l, turnId: p })
```

### 状态检查
- 检查 threadId 匹配
- 检查 turnId 匹配
- 防止重复处理

## 关键代码位置

| 功能 | 行号 | 说明 |
|------|------|------|
| Thread 查询 | 23975 | automation_runs 查询 |
| Turn 中断 | 29232 | interruptTurn 调用 |
| Worktree 创建 | 30533 | git worktree add |
| Worktree 删除 | 30081 | git worktree remove |

## 总结

Thread 管理器的核心设计：
1. **Worktree 隔离** - 每个 Thread 独立工作目录
2. **Turn 机制** - 支持中断和恢复
3. **Ephemeral 模式** - 临时任务优化
