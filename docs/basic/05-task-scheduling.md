# 任务调度和自动化机制分析

## 概述

Codex App 使用 RRule（重复规则）标准来实现任务调度，支持灵活的定时任务配置。

## RRule 调度系统

### 数据结构
```javascript
{
  rrule: "FREQ=DAILY;INTERVAL=1",
  next_run_at: 1707580800000,
  last_run_at: 1707494400000
}
```

### 调度字段说明

| 字段 | 类型 | 说明 |
|------|------|------|
| rrule | TEXT | RRule 格式的重复规则 |
| next_run_at | INTEGER | 下次运行时间戳 |
| last_run_at | INTEGER | 上次运行时间戳 |

## RRule 格式示例

| 规则 | 说明 |
|------|------|
| `FREQ=DAILY` | 每天执行 |
| `FREQ=WEEKLY;BYDAY=MO,WE,FR` | 每周一三五 |
| `FREQ=HOURLY;INTERVAL=2` | 每2小时 |

## 自动化执行流程

1. **创建自动化**: 设置 prompt、rrule、cwds
2. **计算下次运行**: 根据 rrule 计算 next_run_at
3. **触发执行**: 到达时间后创建 automation_run
4. **状态更新**: 更新 last_run_at，重新计算 next_run_at

## 数据库操作

### 查询自动化
```sql
SELECT id, name, prompt, status, cwds, rrule,
       next_run_at as nextRunAt,
       last_run_at as lastRunAt
FROM automations WHERE id = ?
```

## 总结

Codex App 任务调度特点：
1. 使用 RRule 标准格式
2. 支持灵活的重复规则
3. 自动计算下次运行时间
4. 与 Automation 系统集成
