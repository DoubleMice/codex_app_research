# Cloud Tasks 云端任务实现分析

## 概述

Codex Cloud Tasks 是实验性功能，允许在云端执行任务。

## 环境变量

```
CODEX_CLOUD_TASKS_MODE
CODEX_CLOUD_TASKS_BASE_URL
CODEX_CLOUD_TASKS_FORCE_INTERNAL
```

## CLI 命令

### 子命令
```
codex cloud          # 浏览云端任务 TUI
codex cloud exec     # 提交新任务
codex cloud list     # 列出任务
codex cloud status   # 查看任务状态
codex cloud diff     # 查看任务 diff
codex cloud apply    # 应用任务 diff
```

## Rust 模块

### 源文件
```
codex-rs/cloud-tasks/src/
├── env_detect.rs    # 环境检测
├── lib.rs           # 库入口
├── app.rs           # 应用逻辑
├── util.rs          # 工具函数
└── ui.rs            # TUI 界面

codex-rs/cloud-tasks-client/src/
└── http.rs          # HTTP 客户端
```

## 总结

Cloud Tasks 云端任务的核心设计：
1. **实验性功能** - [EXPERIMENTAL] 标记
2. **TUI 界面** - 终端交互式界面
3. **环境检测** - 自动检测可用环境
4. **Diff 应用** - 本地应用云端结果
