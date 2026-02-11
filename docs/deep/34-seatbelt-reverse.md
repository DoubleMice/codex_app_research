# Seatbelt 沙箱策略逆向分析

## 概述

Codex CLI 使用 macOS Seatbelt 沙箱机制限制命令执行的权限。

**沙箱执行器**: `/usr/bin/sandbox-exec`
**策略格式**: SBPL (Sandbox Profile Language)

## 1. 基础策略

```scheme
(version 1)
(deny default)
```

默认拒绝所有操作，然后显式允许需要的权限。

## 2. 进程权限

```scheme
(allow process-exec)
(allow process-fork)
(allow signal (target same-sandbox))
(allow process-info* (target same-sandbox))
```

## 3. 文件系统权限

### 读取权限

```scheme
(allow file-read*)
```

### 写入权限 (受限)

```scheme
(allow file-write*
(allow file-write-data
```

### PTY 设备

```scheme
(allow pseudo-tty)
(allow file-read* file-write* file-ioctl (literal "/dev/ptmx"))
(allow file-read* file-write*
(allow file-ioctl (regex #"^/dev/ttys[0-9]+"))
```

## 4. 网络权限

### 网络启用时添加的策略

```scheme
; when network access is enabled, these policies are added
(allow network-outbound)
(allow network-inbound)
(allow system-socket)
```

## 5. IPC 权限

```scheme
(allow mach-lookup
(allow ipc-posix-sem)
```

## 6. 系统权限

```scheme
(allow user-preference-read)
(allow sysctl-read
(allow sysctl-write
(allow iokit-open
```

## 7. 沙箱模式

| 模式 | 说明 |
|------|------|
| read-only | 只读模式，禁止写入 |
| workspace-write | 允许工作区写入 |
| danger-full-access | 完全访问（危险） |

## 8. 相关环境变量

```
CODEX_SANDBOX              # 沙箱模式
CODEX_SANDBOX_NETWORK_DISABLED  # 禁用网络
```
