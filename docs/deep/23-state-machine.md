# 状态机分析

## 概述

Codex App 使用状态机管理 Thread 和 Turn 的生命周期。

## 1. Thread 生命周期

### Thread 状态

| 状态 | 说明 |
|------|------|
| initialized | 已初始化 |
| started | 已启动 |
| archived | 已归档 |
| compacted | 已压缩 |

### Thread 事件

| 事件 | 说明 |
|------|------|
| thread/started | 线程启动 |
| thread/name/updated | 名称更新 |
| thread/tokenUsage/updated | Token 使用量更新 |
| thread/compacted | 线程压缩 |
| thread/archive | 线程归档 |
| thread/unarchive | 取消归档 |

## 2. Turn 生命周期

### Turn 状态

| 状态 | 说明 |
|------|------|
| started | 已启动 |
| completed | 已完成 |
| aborted | 已中断 |

### Turn 事件

| 事件 | 说明 |
|------|------|
| turn/started | 回合开始 |
| turn/completed | 回合完成 |
| turn/diff/updated | Diff 更新 |
| turn/plan/updated | 计划更新 |

## 3. Item 生命周期

### Item 事件

| 事件 | 说明 |
|------|------|
| item/started | 项目开始 |
| item/completed | 项目完成 |
| item/agentMessage/delta | Agent 消息增量 |
| item/plan/delta | 计划增量 |

## 4. 状态转换图

### Thread 状态转换

```
┌──────────────┐
│ initialized  │
└──────┬───────┘
       │ start
       ▼
┌──────────────┐     archive     ┌──────────────┐
│   started    │ ───────────────►│   archived   │
└──────┬───────┘                 └──────┬───────┘
       │                                │
       │ compact                        │ unarchive
       ▼                                ▼
┌──────────────┐                 ┌──────────────┐
│  compacted   │                 │   started    │
└──────────────┘                 └──────────────┘
```

### Turn 状态转换

```
┌──────────────┐
│   started    │
└──────┬───────┘
       │
       ├─────────────┬─────────────┐
       │             │             │
       ▼             ▼             ▼
┌──────────┐  ┌──────────┐  ┌──────────┐
│ completed│  │  aborted │  │  error   │
└──────────┘  └──────────┘  └──────────┘
```

## 5. 协作 Agent 状态

### CollabAgentState

| 状态 | 说明 |
|------|------|
| SpawnBegin | 启动开始 |
| SpawnEnd | 启动结束 |
| InteractionBegin | 交互开始 |
| InteractionEnd | 交互结束 |
| WaitingBegin | 等待开始 |
| WaitingEnd | 等待结束 |
| CloseBegin | 关闭开始 |
| CloseEnd | 关闭结束 |

