# CLI 帮助文档和子命令分析

## 概述

Codex CLI 是一个基于 Rust 的命令行工具，提供多个子命令。

## 1. 主命令

```
codex [OPTIONS] [PROMPT]
```

如果没有指定子命令，选项将转发到交互式 CLI。

## 2. 子命令列表

| 子命令 | 说明 |
|--------|------|
| resume | 恢复之前的会话 |
| review | 运行代码审查 |
| exec | 执行命令 |
| cloud | Cloud Tasks 操作 |
| session | 会话管理 |

## 3. resume 子命令

```
codex resume [OPTIONS] [SESSION_ID] [PROMPT]
```

### 参数

| 参数 | 说明 |
|------|------|
| SESSION_ID | 会话 ID (UUID) 或线程名称 |
| PROMPT | 恢复后发送的提示 |

### 选项

| 选项 | 说明 |
|------|------|
| --last | 恢复最近的会话 |
| --all | 显示所有会话 (禁用 cwd 过滤) |

## 4. review 子命令

```
codex review [OPTIONS]
```

### 选项

| 选项 | 说明 |
|------|------|
| --uncommitted | 审查暂存、未暂存和未跟踪的更改 |
| --branch BRANCH | 审查与给定基础分支的更改 |
| --sha SHA | 审查提交引入的更改 |
| --title TITLE | 可选的提交标题 |

## 5. cloud 子命令

```
codex cloud [SUBCOMMAND]
```

### 子命令

| 子命令 | 说明 |
|--------|------|
| list | 列出云端任务 |
| diff | 查看差异 |
| exec | 执行云端任务 |

### 示例

```bash
codex cloud list --cursor='...'
```

## 6. 全局选项

| 选项 | 说明 |
|------|------|
| --help | 打印帮助信息 |
| --version | 打印版本信息 |
| --color COLOR | 指定颜色设置 |
| --skip-git-repo-check | 允许在 Git 仓库外运行 |
| --ephemeral | 不持久化会话文件 |
| --output-schema PATH | JSON Schema 文件路径 |
| --output-last-message FILE | 最后消息输出文件 |
| --experimental-json | 以 JSONL 格式打印事件 |

## 7. 环境变量选项

| 环境变量 | 说明 |
|----------|------|
| SKIP_GIT_REPO_CHECK | 允许在 Git 仓库外运行 |
| EPHEMERAL | 不持久化会话文件 |
| OUTPUT_SCHEMA | JSON Schema 文件路径 |
| COLOR | 颜色设置 |
| LAST_MESSAGE_FILE | 最后消息输出文件 |

## 8. execpolicy 检查

```
codex execpolicy-check [OPTIONS] COMMAND
```

### 选项

| 选项 | 说明 |
|------|------|
| --rules RULES | execpolicy 规则文件路径 |
| --pretty | 美化 JSON 输出 |

## 9. TUI 斜杠命令

| 命令 | 说明 |
|------|------|
| /fork | 分叉当前会话 |
| /model | 选择模型 |
| /approvals | 审批设置 |
| /skills | Skills 管理 |
| /apps | 应用管理 |
| /experimental | 实验性功能 |

## 10. 错误消息

| 错误 | 说明 |
|------|------|
| a subcommand is required but one was not provided | 需要子命令 |
| unrecognized subcommand | 无法识别的子命令 |
| the following required argument was not provided | 缺少必需参数 |
| invalid value for one of the arguments | 参数值无效 |

## 11. Shell 补全

支持的 Shell:
- Bash
- Zsh
- Fish
- PowerShell

### Fish 补全示例

```fish
__fish_use_subcommand
__fish_seen_subcommand_from
```

## 12. 帮助输出格式

```
Print help (see more with '--help')
Print help (see a summary with '-h')
Print version
Print this message or the help of the given subcommand(s)
```

## 13. 代码证据

### 主命令用法

```
If no subcommand is specified, options will be forwarded to the interactive CLI.
codex [OPTIONS] [PROMPT]
```

### Resume 命令

```
Resume a previous session by id or pick the most recent with --last
Conversation/session id (UUID) or thread name. UUIDs take precedence if it parses.
```

### Review 命令

```
Run a code review against the current repository
Review staged, unstaged, and untracked changes
Review changes against the given base branch
Review the changes introduced by a commit
```

