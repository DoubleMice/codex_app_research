# CLI 二进制分析

## 基本信息

| 项目 | 值 |
|------|-----|
| 文件名 | codex |
| 类型 | Mach-O 64-bit executable arm64 |
| 大小 | ~54MB |
| 开发语言 | Rust |

## 链接库

### 系统框架
- AppKit.framework
- CoreGraphics.framework
- CoreFoundation.framework
- CoreServices.framework
- Foundation.framework
- Security.framework
- SystemConfiguration.framework

### 系统库
- liblzma.5.dylib
- libbz2.1.0.dylib
- libSystem.B.dylib
- libobjc.A.dylib
- libiconv.2.dylib

## Rust 模块结构

### 核心模块
```
codex_core::auth    - 认证模块
codex_core::exec    - 执行模块
codex_core::util    - 工具模块
codex_mcp_server    - MCP 服务器
codex_mcp_client    - MCP 客户端
```

## 支持的模型

| 模型 | 说明 |
|------|------|
| gpt-5-codex-mini | 轻量版模型 |
| gpt-oss:20b | 开源模型 |
| openai/gpt-oss-20b | 开源模型 |

## 命令行参数

### 主要参数
- `--terminal-width` - 终端宽度
- `--path-to-ignore` - 忽略路径
- `--sandbox-policy` - 沙箱策略
- `--git-common-dir` - Git 公共目录

## 认证方式

### 支持的登录方式
1. **ChatGPT 登录** - OAuth 认证
2. **API Key 登录** - 直接使用 API 密钥
3. **Account 登录** - 账户认证

### 认证相关字符串
```
Refreshing token
Login timed out
API key login is disabled
ChatGPT login is disabled
OAuth login is only supported for streamable HTTP servers
```

## MCP 协议支持

### MCP 功能
- `codex-mcp-server` - 内置 MCP 服务器
- `codex-mcp-client` - MCP 客户端
- 支持 OAuth 登录 MCP 服务器

### MCP 相关命令
```bash
codex mcp add my-tool -- my-command
```

## Cloud Tasks 功能

### 环境变量
- `CODEX_CLOUD_TASKS_MODE`
- `CODEX_CLOUD_TASKS_BASE_URL`
- `CODEX_CLOUD_TASKS_FORCE_INTERNAL`

### Cloud Tasks 操作
- `codex_cloud_tasks_diff` - 查看差异
- `codex_cloud_tasks_exec` - 执行任务
- `codex_cloud_tasks_list` - 列出任务
- `codex_cloud_tasks_apply` - 应用更改
- `codex_cloud_tasks_status` - 查看状态
- `codex_cloud_tasks_tui` - TUI 界面

## 工具调用系统

### 内置工具
- `web_search` - 网页搜索
- `local_shell_call` - 本地 Shell 调用
- `custom_tool_call` - 自定义工具调用

### 审批机制
- `exec-approval` - 执行审批
- `patch-approval` - 补丁审批
- `Allow Codex to apply proposed code changes?`

## 事件系统

### 事件类型
- `TurnStartedEvent` - 回合开始
- `TurnAbortedEvent` - 回合中止
- `UndoStartedEvent` - 撤销开始
- `StreamErrorEvent` - 流错误
- `ShutdownComplete` - 关闭完成

## 自动更新

### 更新流程
1. 下载安装包 (DMG)
2. 挂载 DMG (`hdiutil attach`)
3. 使用 `ditto` 复制应用
4. 卸载 DMG (`hdiutil detach`)

### 安装路径
- `/Applications/Codex.app`
- `~/Applications/Codex.app`

## Ollama 集成

### 本地模型支持
- 检测本地 Ollama 服务器
- 提示: `ollama serve` 启动服务
- 支持 OSS provider 配置 base_url

## 终端检测

### 支持的终端
- iTerm2 (`ITERM_SESSION_ID`)
- Alacritty (`ALACRITTY_SOCKET`)

### TERM 检测
- 检测 `TERM=dumb` 环境
- 非 TTY 环境警告

## API 端点

### ChatGPT 后端
```
https://chatgpt.com/backend-api
```

## 协作模式

### 功能
- `CollabWaitingEnd` - 协作等待结束
- `CollabCloseBegin` - 协作关闭开始
- `Pair Programming` - 结对编程模式

## 总结

Codex CLI 是一个用 Rust 编写的命令行工具，具有以下特点：

1. **模块化设计** - 核心功能分离为 auth、exec、util 等模块
2. **MCP 支持** - 内置 MCP 服务器和客户端
3. **多认证方式** - 支持 ChatGPT OAuth 和 API Key
4. **Cloud Tasks** - 支持云端任务管理
5. **本地模型** - 支持 Ollama 本地模型
