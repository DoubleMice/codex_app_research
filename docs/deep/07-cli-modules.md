# CLI Rust 模块结构分析

## 概述

Codex CLI 使用 Rust 编写，位于 codex-rs/ 目录下。

## 核心模块 (codex_core)

### 源文件列表
```
codex-rs/core/src/
├── auth.rs           # 认证模块
├── codex.rs          # 主入口
├── codex_thread.rs   # 线程管理
├── connectors.rs     # 连接器
├── custom_prompts.rs # 自定义提示
├── exec.rs           # 命令执行
├── exec_policy.rs    # 执行策略
├── git_info.rs       # Git 信息
├── landlock.rs       # Linux 沙箱
├── mcp_connection_manager.rs  # MCP 连接
├── message_history.rs # 消息历史
├── personality_migration.rs   # 配置迁移
├── project_doc.rs    # 项目文档
├── seatbelt.rs       # macOS 沙箱
├── spawn.rs          # 进程启动
└── lib.rs            # 库入口
```

## App Server 模块 (codex_app_server)

```
codex-rs/app-server/src/
├── bespoke_event_handling.rs  # 事件处理
├── codex_message_processor.rs # 消息处理
├── config_api.rs              # 配置 API
├── dynamic_tools.rs           # 动态工具
├── fuzzy_file_search.rs       # 模糊搜索
├── message_processor.rs       # 消息处理器
├── models.rs                  # 数据模型
├── outgoing_message.rs        # 出站消息
├── transport.rs               # 传输层
└── lib.rs                     # 库入口
```

## ChatGPT 模块

```
codex-rs/chatgpt/src/
├── apply_command.rs    # 应用命令
├── chatgpt_client.rs   # ChatGPT 客户端
└── chatgpt_token.rs    # Token 管理
```

## 其他模块

```
codex-rs/
├── async-utils/        # 异步工具
├── backend-client/     # 后端客户端
├── mcp-server/         # MCP 服务器
└── app-server-protocol/ # 协议定义
```

## 协议类型

### App Server Protocol
```rust
codex_app_server_protocol::protocol::v2::
├── ProfileV2
├── ThreadItem
├── TurnStatus
├── ConfigLayer
├── SandboxMode
├── WriteStatus
├── CommandAction
└── ...
```

## 总结

CLI Rust 模块的核心设计：
1. **模块化** - 功能按职责分离
2. **沙箱支持** - landlock (Linux) / seatbelt (macOS)
3. **协议分层** - v1/v2 版本兼容
4. **MCP 集成** - 独立 MCP 服务器模块
