# Prompt 工程分析

## 概述

Codex App 使用精心设计的提示词系统来指导 AI Agent 的行为。

## 1. AGENTS.md 规范

### 作用域规则

```
- Repos often contain AGENTS.md files. These files can appear anywhere within the repository.
- Instructions in AGENTS.md files:
    - The scope of an AGENTS.md file is the entire directory tree rooted at the folder that contains it.
    - For every file you touch in the final patch, you must obey instructions in any AGENTS.md file whose scope includes that file.
    - More-deeply-nested AGENTS.md files take precedence in the case of conflicting instructions.
    - Direct system/developer/user instructions (as part of a prompt) take precedence over AGENTS.md instructions.
```

### 优先级层次

| 优先级 | 来源 |
|--------|------|
| 1 (最高) | 系统/开发者/用户直接指令 |
| 2 | 深层嵌套的 AGENTS.md |
| 3 | 浅层嵌套的 AGENTS.md |
| 4 (最低) | 根目录 AGENTS.md |

## 2. 指令类型

### 指令层级

| 类型 | 说明 |
|------|------|
| baseInstructions | 基础系统指令 |
| developerInstructions | 开发者自定义指令 |
| personality | 个性化配置 |
| customPrompt | 自定义提示词 |

### Turn 级别覆盖

```rust
// TurnStartParams 支持的覆盖选项
- Override the working directory for this turn and subsequent turns.
- Override the approval policy for this turn and subsequent turns.
- Override the sandbox policy for this turn and subsequent turns.
- Override the model for this turn and subsequent turns.
- Override the reasoning effort for this turn and subsequent turns.
- Override the reasoning summary for this turn and subsequent turns.
- Override the personality for this turn and subsequent turns.
```

## 3. apply_patch 工具指令

### 格式规范

```
*** Begin Patch
*** Update File: path/to/file.py
@@ def example():
- pass
+ return 123
*** End Patch
```

### 文件操作类型

| 操作 | 语法 | 说明 |
|------|------|------|
| Add File | `*** Add File: <path>` | 创建新文件，后续行以 + 开头 |
| Delete File | `*** Delete File: <path>` | 删除文件，无后续内容 |
| Update File | `*** Update File: <path>` | 更新现有文件 |

### BNF 语法定义

```bnf
Begin := "*** Begin Patch" NEWLINE
End := "*** End Patch" NEWLINE
AddFile := "*** Add File: " path NEWLINE { "+" line NEWLINE }
DeleteFile := "*** Delete File: " path NEWLINE
UpdateFile := "*** Update File: " path NEWLINE [ MoveTo ] { Hunk }
```

### 使用指南

```
- Use the `apply_patch` tool to edit files (NEVER try `applypatch` or `apply-patch`, only `apply_patch`)
- Do not waste tokens by re-reading files after calling `apply_patch` on them.
- Try to use apply_patch for single file edits, but it is fine to explore other options if it does not work well.
- Do not use apply_patch for changes that are auto-generated (i.e. generating package.json or running a lint or format command)
```

## 4. 个性化配置

### 配置迁移

```rust
// personality_migration.rs
"Failed to run personality migration"
"Failed to deserialize config for personality migration"
```

### 配置文件

```toml
[features]
experimental_instructions_file = ""
experimental_compact_prompt_file = ""
```

## 5. 推理配置

### 推理努力级别

| 级别 | 说明 |
|------|------|
| low | 低推理努力 |
| medium | 中等推理努力 |
| high | 高推理努力 |

### 推理摘要

```toml
[model]
model_reasoning_effort = "medium"
model_reasoning_summary = true
```

### 推理事件

| 事件 | 说明 |
|------|------|
| ReasoningContentDeltaEvent | 推理内容增量 |
| ReasoningSummaryTextDeltaNotification | 推理摘要增量 |
| ReasoningRawContentDeltaEvent | 原始推理内容增量 |

## 6. 上下文压缩

### 压缩机制

```
ContextCompactedNotification - 上下文压缩通知
thread/compacted - 线程压缩事件
```

### 配置项

```toml
[model]
model_auto_compact_token_limit = 100000
```

### 压缩提示文件

```toml
[features]
experimental_compact_prompt_file = ""  # 自定义压缩提示
```

## 7. 协作模式

### 模式预设

```rust
// EXPERIMENTAL - set a pre-set collaboration mode.
// Takes precedence over model, reasoning_effort, and developer instructions if set.
```

### 协作事件

| 事件 | 说明 |
|------|------|
| CollabAgentSpawnBegin | 协作 Agent 启动开始 |
| CollabAgentSpawnEnd | 协作 Agent 启动结束 |
| CollabAgentInteractionBegin | 协作交互开始 |
| CollabAgentInteractionEnd | 协作交互结束 |
| CollabWaitingBegin | 协作等待开始 |
| CollabWaitingEnd | 协作等待结束 |
| CollabCloseBegin | 协作关闭开始 |
| CollabCloseEnd | 协作关闭结束 |

## 8. 子 Agent 系统

### 工具定义

| 工具 | 说明 |
|------|------|
| spawn_agent | 启动子 Agent |
| close_agent | 关闭 Agent |

### 参数结构

```rust
SpawnAgentArgs  // 2 elements
CloseAgentArgs  // 1 element
```

## 9. 提示词架构总结

### 提示词层次结构

```
┌─────────────────────────────────────────┐
│           系统基础指令                    │
│         (baseInstructions)              │
├─────────────────────────────────────────┤
│           开发者指令                      │
│       (developerInstructions)           │
├─────────────────────────────────────────┤
│           AGENTS.md 指令                 │
│      (按目录深度优先级排序)               │
├─────────────────────────────────────────┤
│           用户消息                        │
│         (User Messages)                 │
├─────────────────────────────────────────┤
│           Turn 级别覆盖                   │
│    (personality, model, reasoning)      │
└─────────────────────────────────────────┘
```

### 设计原则

| 原则 | 实现 |
|------|------|
| 层次化 | 多级指令优先级 |
| 可覆盖 | Turn 级别动态调整 |
| 可扩展 | AGENTS.md 仓库级配置 |
| 可压缩 | 自动上下文压缩 |

