# 数据存储分析

## 概述

Codex App 使用 SQLite 数据库进行本地数据存储。

## 数据库位置

```
$CODEX_HOME/sqlite/
```

## 数据库迁移

使用版本化迁移系统，当前版本: 13+

## 数据表结构

### inbox_items 表
| 字段 | 类型 | 说明 |
|------|------|------|
| id | TEXT | 主键 |
| title | TEXT | 标题 |
| description | TEXT | 描述 |
| thread_id | TEXT | 线程 ID |
| read_at | INTEGER | 阅读时间 |
| created_at | INTEGER | 创建时间 |

### automations 表
| 字段 | 类型 | 说明 |
|------|------|------|
| id | TEXT | 主键 |
| name | TEXT | 名称 |
| prompt | TEXT | 提示词 |
| status | TEXT | 状态 (默认 ACTIVE) |
| next_run_at | INTEGER | 下次运行时间 |
| last_run_at | INTEGER | 上次运行时间 |
| cwds | TEXT | 工作目录列表 (JSON) |
| rrule | TEXT | 调度规则 |
| created_at | INTEGER | 创建时间 |
| updated_at | INTEGER | 更新时间 |

### automation_runs 表
| 字段 | 类型 | 说明 |
|------|------|------|
| thread_id | TEXT | 主键 |
| automation_id | TEXT | 自动化 ID |
| status | TEXT | 状态 |
| read_at | INTEGER | 阅读时间 |
| thread_title | TEXT | 线程标题 |
| source_cwd | TEXT | 源工作目录 |
| inbox_title | TEXT | 收件箱标题 |
| inbox_summary | TEXT | 收件箱摘要 |
| created_at | INTEGER | 创建时间 |
| updated_at | INTEGER | 更新时间 |

## 文件存储

### Automation 配置
- 路径: `$CODEX_HOME/automations/<id>/`
- 格式: TOML

### Skills 配置
- 路径: `~/.codex/skills/`
- 格式: YAML (openai.yaml)

### Thread 数据
- 路径: `$CODEX_HOME/threads/`
- 存储对话历史

## RRule 调度格式

### 默认规则
- 使用 iCalendar RRule 格式
- 支持周期性任务调度

### 示例
```
FREQ=DAILY;INTERVAL=1
FREQ=WEEKLY;BYDAY=MO,WE,FR
```

## 状态管理

### Automation 状态
- `ACTIVE` - 活跃
- `PAUSED` - 暂停
- `DISABLED` - 禁用

## 总结

Codex App 的数据存储特点：

1. **SQLite 数据库** - 本地持久化
2. **版本化迁移** - 数据库升级
3. **TOML 配置** - Automation 配置
4. **RRule 调度** - 定时任务
