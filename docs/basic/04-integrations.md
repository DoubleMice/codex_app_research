# 开发环境集成分析

## 概述

Codex App 提供多种方式与开发环境集成，包括 CLI 工具、终端集成和远程主机支持。

## CLI 工具

### codex 二进制
- **位置**: `Codex.app/Contents/Resources/codex`
- **架构**: ARM64 Mach-O
- **大小**: ~54MB

## 终端集成

### 内置终端
- 快捷键: `Cmd+J` 切换终端
- 使用 `node-pty` 实现终端模拟
- 支持终端窗口大小调整

### 远程主机支持
```javascript
terminal_command: ["ssh", hostname]
```
- 通过 SSH 连接远程主机
- 支持自定义终端命令

## 支持的应用

从 `webview/apps/` 目录发现支持的应用：

| 类别 | 应用 |
|------|------|
| IDE | VS Code, VS Code Insiders, Cursor, Windsurf |
| JetBrains | IntelliJ, PyCharm, GoLand, WebStorm, RustRover, Android Studio |
| 编辑器 | Xcode, Zed, TextMate, BBEdit |
| 终端 | Terminal, iTerm2, Warp, Ghostty |
| 其他 | Finder |

## Git 集成

### Worktree 支持
```javascript
config extensions.worktreeConfig true
```
- 支持 Git worktree 隔离
- 多任务并行开发

## URL Scheme

- **协议**: `codex://`
- 支持从外部应用打开 Codex

## 总结

Codex App 的开发环境集成特点：
1. 独立 CLI 二进制工具
2. 内置终端模拟器
3. 广泛的 IDE/编辑器支持
4. Git worktree 隔离机制
