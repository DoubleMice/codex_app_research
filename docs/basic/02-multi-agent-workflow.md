# 多代理工作流分析

## 概述

Codex App 采用基于 Thread（线程）的多任务管理架构，而非传统意义上的多代理系统。每个 Thread 代表一个独立的对话/任务上下文。

## 核心概念

### Thread（线程）
- 每个任务运行在独立的 Thread 中
- Thread 有唯一的 `thread_id`
- 支持 Thread 标题和状态管理
- 可以固定（pin）重要的 Thread

### Automation（自动化）
- 定时执行的后台任务
- 每次运行创建一个 `automation_run` 记录
- 支持 RRule 格式的重复规则

## 数据库 Schema

### automations 表
```sql
CREATE TABLE automations (
  id TEXT PRIMARY KEY,
  prompt TEXT NOT NULL,
  status TEXT NOT NULL DEFAULT 'ACTIVE',
  last_run_at INTEGER,
  next_run_at INTEGER,
  cwds TEXT NOT NULL DEFAULT '[]',
  rrule TEXT,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL
)
```

### automation_runs 表
```sql
CREATE TABLE automation_runs (
  thread_id TEXT PRIMARY KEY,
  automation_id TEXT NOT NULL,
  status TEXT NOT NULL,
  read_at INTEGER,
  thread_title TEXT,
  source_cwd TEXT,
  inbox_title TEXT,
  inbox_summary TEXT,
  archived_user_message TEXT,
  archived_assistant_message TEXT,
  archived_reason TEXT,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL
)
```

## 任务状态流转

| 状态 | 说明 |
|------|------|
| IN_PROGRESS | 任务执行中 |
| PENDING_REVIEW | 等待用户审核 |
| READ | 用户已查看 |
| ARCHIVED | 已归档 |

## 工作流机制

### Git Worktree 隔离
- 每个并行任务使用独立的 Git worktree
- 避免多任务间的代码冲突
- 支持同一仓库的多分支并行开发

### Thread 管理
- 支持固定重要 Thread (`PINNED_THREAD_IDS`)
- Thread 标题自动生成和手动修改
- Thread 状态变更事件通知

### 自动化调度
- 使用 RRule 格式定义重复规则
- 记录上次运行时间 (`last_run_at`)
- 计算下次运行时间 (`next_run_at`)
- 支持多工作目录 (`cwds`)

## 代码实现分析

### 事件系统
```javascript
// Thread 状态变更事件
tq = {
  "thread-stream-state-changed": 4,
  "thread-archived": 1
}
```

### 存储键
```javascript
PINNED_THREAD_IDS: "pinned-thread-ids"
THREAD_TITLES: "thread-titles"
```

## 总结

Codex App 的"多代理"实际上是基于 Thread 的多任务并行执行：
1. 每个 Thread 是独立的对话上下文
2. Automation 提供定时任务能力
3. Git Worktree 实现代码隔离
4. 状态机管理任务生命周期
