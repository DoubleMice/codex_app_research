# Codex App 安全机制深度分析

## 概述

Codex App 实现了多层安全机制，包括沙箱隔离、权限控制和审批策略。

## 1. 沙箱模式 (Sandbox Modes)

### 三种沙箱级别

| 模式 | 说明 | 风险等级 |
|------|------|----------|
| read-only | 只读模式，禁止写入 | 低 |
| workspace-write | 允许写入工作目录 | 中 |
| danger-full-access | 完全访问，无限制 | 高 |

### 配置映射

```javascript
// 行号: 20350-20352
KG = {
  "read-only": { sandboxMode: "read-only", approvalPolicy: "on-request" },
  auto: { sandboxMode: "workspace-write", approvalPolicy: "on-request" },
  "full-access": { sandboxMode: "danger-full-access", approvalPolicy: "never" },
}
```

## 2. 审批策略 (Approval Policy)

### 策略类型

| 策略 | 说明 |
|------|------|
| on-request | 每次执行前请求用户确认 |
| never | 从不请求确认（危险） |
| auto-edit | 自动编辑模式 |

### CLI 参数

```bash
codex [OPTIONS]
  --sandbox <MODE>           # 沙箱模式
  --approval-policy <POLICY> # 审批策略
  --full-auto                # 便捷别名 (workspace-write + on-request)
  --dangerously-bypass-approvals-and-sandbox  # 跳过所有确认（极度危险）
```

## 3. macOS Seatbelt 沙箱

### 实现机制

```
执行路径: /usr/bin/sandbox-exec
源文件: codex-rs/core/src/seatbelt.rs
调试工具: codex-rs/cli/src/debug_sandbox/seatbelt.rs
```

### Seatbelt 策略规则

**基础策略 (seatbelt_base_policy.sbpl)**
```scheme
; 允许只读文件操作
(allow file-read*)

; PTY 设备访问
(allow file-read* file-write* file-ioctl (literal "/dev/ptmx"))
(allow file-ioctl (regex #"^/dev/ttys[0-9]+"))

; 进程控制
(allow process-exec)
(allow process-fork)
(allow process-info* (target same-sandbox))
```

**网络访问策略**
```scheme
; 当 network_access 启用时添加
(allow network-outbound)
(allow network-inbound)
```

**写入策略**
```scheme
; workspace-write 模式
(allow file-write* (regex #"^/"))
(allow file-write-data ...)
```

### 调试功能

```bash
codex seatbelt --log-denials <command>
# 捕获 macOS sandbox denials 并在退出后打印
```

### 日志监控

```javascript
// 监控 sandbox 拒绝事件
--predicate '(((processID == 0) AND (senderImagePath CONTAINS "/Sandbox"))
              OR (subsystem == "com.apple.sandbox.reporting"))'
```

## 4. Linux Landlock + seccomp 沙箱

### 实现机制

```
源文件: codex-rs/core/src/landlock.rs
执行器: codex-linux-sandbox
```

### Landlock 文件系统隔离

```rust
// 核心功能
LandlockRestrict  // 限制文件系统访问

// 错误处理
"Landlock was not able to fully enforce all sandbox rules"
```

### seccomp 系统调用过滤

```
// 限制危险系统调用
"operation not permitted"
"read-only file system"
```

### CLI 命令

```bash
codex landlock <command>
# 在 Landlock+seccomp 下运行命令 (仅 Linux)
```

## 5. Windows 受限令牌沙箱

### 实现机制

```
类型: WindowsRestrictedToken
执行器: Windows Restricted Token Sandbox
```

### CLI 命令

```bash
codex windows <command>
# 在 Windows 受限令牌下运行命令 (仅 Windows)
```

## 6. 沙箱配置选项

### 工作区写入配置

```toml
[sandbox_workspace_write]
writable_roots = ["/path/to/dir"]  # 可写目录列表
network_access = false              # 网络访问开关
exclude_tmpdir_env_var = true       # 排除 TMPDIR
exclude_slash_tmp = true            # 排除 /tmp
```

### 环境变量

| 变量 | 说明 |
|------|------|
| CODEX_SANDBOX | 沙箱模式 |
| CODEX_SANDBOX_NETWORK_DISABLED | 禁用网络 |
| DARWIN_USER_CACHE_DIR | macOS 缓存目录 |

## 7. Electron 进程隔离

### 安全配置

```javascript
// 渲染进程隔离
contextIsolation: true      // 上下文隔离
nodeIntegration: false      // 禁用 Node.js 集成
```

### Context Bridge

```javascript
// 安全暴露 API
n.contextBridge.exposeInMainWorld("electronBridge", R);
n.contextBridge.exposeInMainWorld("codexWindowType", "electron");
```

## 8. 执行策略 (Exec Policy)

### 策略文件

```
源文件: codex-rs/core/src/exec_policy.rs
默认规则: default.rules
```

### 策略检查

```rust
// 审批检查
"approval required by policy, but AskForApproval is set to Never"
```

### CLI 验证

```bash
# 检查 execpolicy 文件
codex check-execpolicy <command>
```

## 9. Git Worktree 隔离

### 任务隔离机制

```
每个 Thread 独立 Worktree
├── 避免工作目录冲突
├── 支持并行任务执行
└── 支持快照和回滚
```

### 路径配置

```
$CODEX_HOME/worktrees/<thread-id>/
```

## 10. 安全架构总结

### 多层防护体系

```
┌─────────────────────────────────────────┐
│           用户审批层                      │
│    (on-request / never / auto-edit)     │
├─────────────────────────────────────────┤
│           执行策略层                      │
│         (exec_policy.rs)                │
├─────────────────────────────────────────┤
│           沙箱隔离层                      │
│  ┌─────────┬─────────┬─────────┐       │
│  │ Seatbelt│ Landlock│ Windows │       │
│  │ (macOS) │ (Linux) │ Token   │       │
│  └─────────┴─────────┴─────────┘       │
├─────────────────────────────────────────┤
│           进程隔离层                      │
│    (Electron contextIsolation)          │
├─────────────────────────────────────────┤
│           任务隔离层                      │
│       (Git Worktree per Thread)         │
└─────────────────────────────────────────┘
```

### 安全设计原则

| 原则 | 实现 |
|------|------|
| 最小权限 | 默认 read-only 模式 |
| 纵深防御 | 多层沙箱机制 |
| 用户控制 | 审批策略可配置 |
| 隔离执行 | Worktree 任务隔离 |

### 可借鉴点

1. **跨平台沙箱** - 针对不同 OS 实现原生沙箱
2. **策略分层** - 审批策略与沙箱策略分离
3. **调试支持** - 提供 sandbox denial 日志
4. **灵活配置** - 支持细粒度权限控制

