# 配置系统分析

## 概述

Codex App 使用 TOML 格式的配置文件。

## 1. 配置文件位置

```
~/.codex/config.toml
```

## 2. 配置结构

### ConfigToml 主结构

```rust
struct ConfigToml {
  // 主配置结构
}
```

### 子配置结构

| 结构 | 说明 |
|------|------|
| ToolsToml | 工具配置 |
| FeaturesToml | 功能开关 |
| AnalyticsConfigToml | 分析配置 |
| FeedbackConfigToml | 反馈配置 |
| OtelConfigToml | OpenTelemetry 配置 |
| ShellEnvironmentPolicyToml | Shell 环境策略 |

## 3. 核心配置项

### 模型配置

```toml
[model]
model_provider = "openai"      # 模型提供商
model_context_window = 128000  # 上下文窗口
model_auto_compact_token_limit = 100000
model_reasoning_effort = "medium"
model_reasoning_summary = true
model_verbosity = "normal"
```

### 沙箱配置

```toml
[sandbox_workspace_write]
writable_roots = []
network_access = false
exclude_tmpdir_env_var = true
exclude_slash_tmp = true
inherit = false
ignore_default_excludes = false
```

### 工具配置

```toml
[tools]
web_search = "live"
view_image = true
tool_timeout_secs = 300
tool_output_token_limit = 10000
```

### TUI 配置

```toml
[tui]
hide_agent_reasoning = false
show_raw_agent_reasoning = false
notifications = true
notification_method = "native"
animations = true
show_tooltips = true
```

### 分析配置

```toml
[analytics]
enabled = false  # 默认禁用
```

### 持久化配置

```toml
[persistence]
max_bytes = 10485760  # 10MB
```

## 4. 配置层级

### 层级优先级

```
1. 命令行参数 (最高)
2. 环境变量
3. 项目配置 (.codex/)
4. 用户配置 (~/.codex/config.toml)
5. 默认值 (最低)
```

## 5. 配置 Profile

### 使用方式

```bash
codex --config-profile <PROFILE>
```

### Profile 结构

```rust
struct ConfigProfile {
  // 配置 profile
}
```

## 6. 实验性功能

### 功能开关

```toml
[features]
experimental_use_unified_exec_tool = false
experimental_use_freeform_apply_patch = false
experimental_instructions_file = ""
experimental_compact_prompt_file = ""
```

## 7. 配置迁移

### 迁移机制

```rust
// personality_migration.rs
"Failed to run personality migration"
"Failed to deserialize config for personality migration"
```

### 错误处理

- 配置解析失败时使用默认值
- 支持配置版本迁移

