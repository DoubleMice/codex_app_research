# codex CLI 二进制逆向分析

## 概述

Codex CLI 是用 Rust 编写的命令行工具，负责与 OpenAI API 通信和执行代理任务。

**文件**: `extracted/codex`
**架构**: arm64 (Apple Silicon)
**语言**: Rust

## 1. 链接库

### macOS 系统框架

| 框架 | 说明 |
|------|------|
| AppKit | GUI 框架 |
| CoreGraphics | 图形框架 |
| CoreFoundation | 核心基础框架 |
| CoreServices | 核心服务 |
| Foundation | 基础框架 |
| Security | 安全框架 |
| SystemConfiguration | 系统配置 |

### 系统库

| 库 | 说明 |
|---|------|
| liblzma.5.dylib | LZMA 压缩 |
| libbz2.1.0.dylib | BZ2 压缩 |
| libSystem.B.dylib | 系统库 |
| libobjc.A.dylib | Objective-C 运行时 |
| libiconv.2.dylib | 字符编码转换 |

## 2. 外部符号

### CoreFoundation 符号

```
_CFArrayCreate
_CFArrayAppendValue
_CFDictionaryCreate
_CFDataCreate
_CFRelease
_CFRetain
```

### Security 符号

```
_SecTrustEvaluate
_SecTrustGetCertificateAtIndex
_SecTrustSetAnchorCertificates
_SSLCopyPeerTrust
```

### 压缩库符号

```
_BZ2_bzDecompress
_BZ2_bzDecompressEnd
_BZ2_bzDecompressInit
```

## 3. Rust 模块结构

### codex_core 核心模块

```
core/src/codex.rs              # 主入口
core/src/codex_thread.rs       # 线程管理
core/src/codex_delegate.rs     # 委托处理
core/src/client.rs             # API 客户端
core/src/default_client.rs     # 默认客户端
core/src/auth.rs               # 认证模块
core/src/config/mod.rs         # 配置模块
core/src/config/edit.rs        # 配置编辑
core/src/exec.rs               # 执行模块
core/src/exec_policy.rs        # 执行策略
core/src/bash.rs               # Bash 执行
core/src/powershell.rs         # PowerShell 执行
core/src/spawn.rs              # 进程生成
core/src/apply_patch.rs        # 补丁应用
core/src/compact.rs            # 上下文压缩
core/src/compact_remote.rs     # 远程压缩
core/src/truncate.rs           # 截断处理
core/src/git_info.rs           # Git 信息
core/src/file_watcher.rs       # 文件监视
core/src/project_doc.rs        # 项目文档 (AGENTS.md)
core/src/parse_command.rs      # 命令解析
core/src/message_history.rs    # 消息历史
core/src/turn_metadata.rs      # Turn 元数据
core/src/landlock.rs           # Linux 沙箱
```

### 工具系统模块

```
core/src/tools/mod.rs                    # 工具模块入口
core/src/tools/registry.rs               # 工具注册表
core/src/tools/router.rs                 # 工具路由
core/src/tools/orchestrator.rs           # 工具编排
core/src/tools/parallel.rs               # 并行执行
core/src/tools/sandboxing.rs             # 沙箱化
core/src/tools/handlers/shell.rs         # Shell 工具
core/src/tools/handlers/read_file.rs     # 读文件工具
core/src/tools/handlers/list_dir.rs      # 列目录工具
core/src/tools/handlers/grep_files.rs    # Grep 工具
core/src/tools/handlers/view_image.rs    # 查看图片
core/src/tools/handlers/plan.rs          # 计划工具
core/src/tools/handlers/collab.rs        # 协作工具
core/src/tools/handlers/mcp.rs           # MCP 工具
core/src/tools/handlers/dynamic.rs       # 动态工具
core/src/tools/handlers/unified_exec.rs  # 统一执行
core/src/tools/runtimes/apply_patch.rs   # 补丁运行时
core/src/tools/runtimes/shell.rs         # Shell 运行时
```

### App Server 模块

```
app-server/src/message_processor.rs          # 消息处理器
app-server/src/codex_message_processor.rs    # Codex 消息处理
app-server/src/outgoing_message.rs           # 出站消息
app-server/src/dynamic_tools.rs              # 动态工具
app-server/src/fuzzy_file_search.rs          # 模糊文件搜索
app-server/src/bespoke_event_handling.rs     # 定制事件处理
```

### MCP Server 模块

```
mcp-server/src/lib.rs                  # MCP 服务器入口
mcp-server/src/message_processor.rs    # 消息处理
mcp-server/src/codex_tool_runner.rs    # Codex 工具运行器
mcp-server/src/codex_tool_config.rs    # 工具配置
mcp-server/src/exec_approval.rs        # 执行审批
mcp-server/src/patch_approval.rs       # 补丁审批
mcp-server/src/outgoing_message.rs     # 出站消息
```

### TUI 模块

```
tui/src/app.rs                         # TUI 应用入口
tui/src/app_backtrack.rs               # 回溯功能
tui/src/chatwidget.rs                  # 聊天组件
tui/src/chatwidget/agent.rs            # Agent 组件
tui/src/chatwidget/skills.rs           # Skills 组件
tui/src/diff_render.rs                 # Diff 渲染
tui/src/markdown_render.rs             # Markdown 渲染
tui/src/file_search.rs                 # 文件搜索
tui/src/session_log.rs                 # 会话日志
tui/src/resume_picker.rs               # 恢复选择器
tui/src/onboarding/auth.rs             # 认证引导
tui/src/update_prompt.rs               # 更新提示
```

## 4. 环境变量

### 核心配置

| 变量 | 说明 |
|------|------|
| CODEX_HOME | Codex 主目录 (~/.codex) |
| CODEX_API_KEY | API 密钥 |
| OPENAI_API_KEY | OpenAI API 密钥 |
| OPENAI_BASE_URL | API 基础 URL |
| OPENAI_ORGANIZATION | 组织 ID |
| OPENAI_PROJECT | 项目 ID |

### 沙箱配置

| 变量 | 说明 |
|------|------|
| CODEX_SANDBOX | 沙箱模式 |
| CODEX_SANDBOX_NETWORK_DISABLED | 禁用网络 |

### Cloud Tasks

| 变量 | 说明 |
|------|------|
| CODEX_CLOUD_TASKS_MODE | Cloud Tasks 模式 |
| CODEX_CLOUD_TASKS_BASE_URL | Cloud Tasks 基础 URL |
| CODEX_CLOUD_TASKS_FORCE_INTERNAL | 强制内部模式 |

### TUI 配置

| 变量 | 说明 |
|------|------|
| CODEX_TUI_ROUNDED | 圆角边框 |
| CODEX_TUI_RECORD_SESSION | 记录会话 |
| CODEX_TUI_SESSION_LOG_PATH | 会话日志路径 |

### 调试/内部

| 变量 | 说明 |
|------|------|
| CODEX_RS_SSE_FIXTURE | SSE 测试夹具 |
| CODEX_STARTING_DIFF | 起始 Diff |
| CODEX_APPLY_GIT_CFG | 应用 Git 配置 |
| CODEX_THREAD_ID | 线程 ID |
| CODEX_MANAGED_BY_NPM | NPM 管理标记 |
| CODEX_MANAGED_BY_BUN | Bun 管理标记 |
| CODEX_CONNECTORS_TOKEN | 连接器令牌 |
| CODEX_GITHUB_PERSONAL_ACCESS_TOKEN | GitHub PAT |
| CODEX_REFRESH_TOKEN_URL_OVERRIDE | 刷新令牌 URL 覆盖 |
| CODEX_INTERNAL_ORIGINATOR_OVERRIDE | 内部来源覆盖 |
| CODEX_OSS_BASE_URL | OSS 基础 URL |
| CODEX_OSS_PORT | OSS 端口 |
| CODEX_POWERSHELL_PAYLOAD | PowerShell 载荷 |

## 5. 协议类型定义

### codex_app_server_protocol 命名空间

```rust
// V2 协议类型
codex_app_server_protocol::protocol::v2::ThreadItem
codex_app_server_protocol::protocol::v2::TurnStatus
codex_app_server_protocol::protocol::v2::TurnError
codex_app_server_protocol::protocol::v2::ConfigLayer
codex_app_server_protocol::protocol::v2::ConfigLayerSource
codex_app_server_protocol::protocol::v2::SandboxMode
codex_app_server_protocol::protocol::v2::SandboxPolicy
codex_app_server_protocol::protocol::v2::NetworkAccess
codex_app_server_protocol::protocol::v2::MergeStrategy
codex_app_server_protocol::protocol::v2::SessionSource
codex_app_server_protocol::protocol::v2::SkillMetadata
codex_app_server_protocol::protocol::v2::SkillScope
codex_app_server_protocol::protocol::v2::McpAuthStatus
codex_app_server_protocol::protocol::v2::McpToolCallStatus
codex_app_server_protocol::protocol::v2::WriteStatus
codex_app_server_protocol::protocol::v2::TextElement
codex_app_server_protocol::protocol::v2::TextPosition
codex_app_server_protocol::protocol::v2::TextRange
codex_app_server_protocol::protocol::v2::ByteRange
codex_app_server_protocol::protocol::v2::CommandAction
codex_app_server_protocol::protocol::v2::TurnPlanStep
codex_app_server_protocol::protocol::v2::AskForApproval
codex_app_server_protocol::protocol::v2::ErrorNotification
codex_app_server_protocol::protocol::v2::RateLimitSnapshot
codex_app_server_protocol::protocol::v2::ModelListResponse
codex_app_server_protocol::protocol::v2::CollabAgentStatus

// V1 协议类型
codex_app_server_protocol::protocol::v1::Tools
codex_app_server_protocol::protocol::v1::Profile

// 通用类型
codex_app_server_protocol::protocol::common::GitSha
```

## 6. TUI 斜杠命令

从二进制中提取的 TUI 斜杠命令及其描述：

| 命令 | 说明 |
|------|------|
| /model | choose what model and reasoning effort to use |
| /approvals | choose what Codex can do without approval |
| /permissions | choose what Codex is allowed to do |
| /setup-elevated-sandbox | set up elevated agent sandbox |
| /experimental | toggle experimental features |
| /skills | use skills to improve how Codex performs specific tasks |
| /review | review my current changes and find issues |
| /rename | rename the current thread |
| /new | start a new chat during a conversation |
| /resume | resume a saved chat |
| /fork | fork the current chat |
| /agents | create an AGENTS.md file with instructions for Codex |
| /compact | summarize conversation to prevent hitting the context limit |
| /plan | switch to Plan mode |
| /collab | change collaboration mode (experimental) |
| /diff | show git diff (including untracked files) |
| /@ | mention a file |
| /status | show current session configuration and token usage |
| /debug-config | show config layers and requirement sources for debugging |
| /statusline | configure which items appear in the status line |
| /mcp | list configured MCP tools |
| /apps | manage apps |
| /logout | exit Codex |
| /bug | send logs to maintainers |
| /rollout | print the rollout file path |
| /terminals | list background terminals |
| /personality | choose a communication style for Codex |
| /test-approval | test approval request |

## 7. 配置选项

从二进制中提取的配置选项列表：

```
model                              # 模型名称
model_provider                     # 模型提供商
model_context_window               # 上下文窗口大小
model_auto_compact_token_limit     # 自动压缩令牌限制
model_reasoning_effort             # 推理努力程度
model_reasoning_summary            # 推理摘要
model_verbosity                    # 模型详细程度
model_supports_reasoning_summaries # 支持推理摘要
model_instructions_file            # 模型指令文件
shell_environment_policy           # Shell 环境策略
sandbox_workspace_write            # 沙箱工作区写入
notify                             # 通知设置
developer_instructions             # 开发者指令
compact_prompt                     # 压缩提示
profile                            # 配置文件
history                            # 历史记录
log_dir                            # 日志目录
file_opener                        # 文件打开器
tui                                # TUI 设置
hide_agent_reasoning               # 隐藏代理推理
show_raw_agent_reasoning           # 显示原始代理推理
personality                        # 个性设置
agents                             # 代理配置
skills                             # 技能配置
analytics                          # 分析设置
check_for_update_on_startup        # 启动时检查更新
disable_paste_burst                # 禁用粘贴突发
project_root_markers               # 项目根标记
trust_level                        # 信任级别
ignore_large_untracked_files       # 忽略大型未跟踪文件
persistence                        # 持久化设置
max_bytes                          # 最大字节数
```

## 8. API 端点和 URL

从二进制中提取的硬编码 URL：

```
https://api.openai.com/profile           # 用户配置文件
https://auth.openai.com/oauth/token      # OAuth 令牌端点
/backend-api                             # 后端 API 路径前缀
```

### API 请求头

```
OpenAI-Beta: responses_websocket
OpenAI-Organization
OpenAI-Project
x-openai-internal-codex-residency
x-openai-subagent
x-codex-beta-features
x-codex-turn-state
x-codex-turn-metadata
Authorization: Bearer <token>
```

## 9. 事件类型

从二进制中提取的 codex_event 类型：

```
new_session              # 新会话
file_search_start        # 文件搜索开始
file_search_result       # 文件搜索结果
insert_history_cell      # 插入历史单元
app_event                # 应用事件
codex_error_info         # 错误信息
codex_elicitation        # 引出请求
codex_mcp_tool_call_id   # MCP 工具调用 ID
codex_event_id           # 事件 ID
codex_call_id            # 调用 ID
codex_command            # 命令
codex_cwd                # 当前工作目录
codex_reason             # 原因
codex_changes            # 变更
```

## 10. 错误消息

从二进制中提取的关键错误消息：

```
"Not signed in. Please run 'codex login' to sign in with ChatGPT"
"API key login is disabled. Use ChatGPT login instead."
"ChatGPT login is disabled. Use API key login instead."
"OAuth login is only supported for streamable HTTP servers."
"Login timed out"
"Access blocked by Cloudflare"
"Token data is not available."
"No more recovery steps available."
"thread has no persisted rollout"
"approval required by policy, but AskForApproval is set to Never"
"command must not be empty"
"instructions must not be empty"
"branch must not be empty"
"sha must not be empty"
```
