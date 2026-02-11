# Automation 调度器实现分析

## 概述

Automation 是 Codex App 的定时任务系统，使用 RRule 格式定义调度规则。

## 数据库 Schema

```sql
CREATE TABLE automations (
  id TEXT PRIMARY KEY,
  name TEXT NOT NULL,
  prompt TEXT NOT NULL,
  status TEXT NOT NULL DEFAULT 'ACTIVE',
  next_run_at INTEGER,
  last_run_at INTEGER,
  cwds TEXT NOT NULL DEFAULT '[]',
  rrule TEXT NOT NULL,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL
)
```

## RRule 解析

### RRule 库集成
- 使用标准 RRule 库
- 支持 `between`、`before`、`after` 方法
- 行号: 27192-27217

### RRule 验证
```javascript
t.isFullyConvertible(this.rrule)
// 检查 RRule 是否可完全转换
```

## 调度计算

### 核心函数
```javascript
op({ automation: t, nextRunAt: e, lastRunAt: n })
// 行号: 28458
```

### SQL 操作
```sql
INSERT OR REPLACE INTO automations
  (id, name, prompt, status, next_run_at, last_run_at,
   cwds, rrule, created_at, updated_at)
VALUES (?, ?, ?, ?, ?, ?, ?, ?, ?, ?)
```

## 状态管理

### Automation 状态
- `ACTIVE` - 活跃
- `DELETED` - 已删除

### 查询活跃任务
```sql
SELECT id, name, prompt, status, cwds, rrule,
       next_run_at as nextRunAt,
       last_run_at as lastRunAt
FROM automations
WHERE status != 'DELETED'
```

## Thread 交互

### 启动 Automation Thread
```javascript
d7({ automation: t, threadId: m, cwd: f.cwd, appServerConnection: e })
// 行号: 36874
```

## 关键代码位置

| 功能 | 行号 | 说明 |
|------|------|------|
| Schema 定义 | 23856 | automations 表 |
| RRule 验证 | 25320 | isFullyConvertible |
| 调度计算 | 28458 | op 函数 |
| Thread 启动 | 36874 | d7 函数 |

## 总结

Automation 调度器的核心设计：
1. **RRule 标准** - 使用 iCalendar 规范
2. **SQLite 持久化** - 状态存储
3. **Thread 集成** - 自动创建执行线程
