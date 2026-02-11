# SQLite 数据访问层实现分析

## 概述

Codex App 使用 better-sqlite3 库进行本地数据持久化。

## 数据库表结构

### inbox_items 表
```sql
-- 版本 1
CREATE TABLE IF NOT EXISTS inbox_items (
  id TEXT PRIMARY KEY,
  title TEXT
)

-- 版本 2 添加字段
ALTER TABLE inbox_items ADD COLUMN description TEXT;
ALTER TABLE inbox_items ADD COLUMN thread_id TEXT;
ALTER TABLE inbox_items ADD COLUMN read_at INTEGER;
ALTER TABLE inbox_items ADD COLUMN created_at INTEGER;
```

### automations 表
```sql
-- 版本 7
CREATE TABLE IF NOT EXISTS automations (
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

### automation_runs 表
```sql
-- 版本 9
CREATE TABLE IF NOT EXISTS automation_runs (
  thread_id TEXT PRIMARY KEY,
  automation_id TEXT NOT NULL,
  status TEXT NOT NULL,
  read_at INTEGER,
  thread_title TEXT,
  source_cwd TEXT,
  inbox_title TEXT,
  inbox_summary TEXT,
  created_at INTEGER NOT NULL,
  updated_at INTEGER NOT NULL
)

-- 版本 14 添加字段
ALTER TABLE automation_runs ADD COLUMN archived_user_message TEXT;
ALTER TABLE automation_runs ADD COLUMN archived_assistant_message TEXT;

-- 版本 19 添加字段
ALTER TABLE automation_runs ADD COLUMN archived_reason TEXT;
```

## 迁移机制

### 版本管理
```javascript
// 行号: 23941-23948
const n = KH(t);  // 获取当前版本
for (const r of e)
  if (!(r.version <= n)) {
    t.prepare("BEGIN").run();
    try {
      r.up();
      t.prepare(`PRAGMA user_version = ${r.version}`).run();
      t.prepare("COMMIT").run();
    } catch (i) {
      // 回滚
    }
  }
```

### 字段添加函数
```javascript
// Ot 函数用于安全添加列
Ot(t, "table_name", "column_name", "TYPE");
```

## 关键代码位置

| 功能 | 行号 | 说明 |
|------|------|------|
| 迁移函数 | 23820 | ZH 函数 |
| inbox_items | 23825 | 版本 1 |
| automations | 23851 | 版本 7 |
| automation_runs | 23877 | 版本 9 |
| 版本检查 | 23941 | KH 函数 |

## 总结

SQLite 数据访问层的核心设计：
1. **版本迁移** - PRAGMA user_version 管理
2. **事务保护** - BEGIN/COMMIT/ROLLBACK
3. **增量迁移** - 只执行未应用的版本
4. **安全添加列** - Ot 函数封装
