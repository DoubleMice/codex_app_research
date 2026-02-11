# pty.node 终端原生模块逆向分析

## 概述

pty.node 是 node-pty 库的原生模块，提供伪终端 (PTY) 功能。

**文件**: `extracted/app/node_modules/node-pty/build/Release/pty.node`
**架构**: arm64 (Apple Silicon)
**语言**: C++ (N-API 绑定)

## 1. 链接库

```
/usr/lib/libc++.1.dylib
/usr/lib/libSystem.B.dylib
```

## 2. 导出符号

### N-API 入口

```
_napi_register_module_v1        # 模块注册入口
_node_api_module_get_api_version_v1  # API 版本
```

### 核心函数

| 符号 | 说明 |
|------|------|
| `PtyFork` | 创建 PTY 并 fork 子进程 |
| `PtyOpen` | 打开 PTY 对 |
| `PtyResize` | 调整 PTY 尺寸 |
| `PtyGetProc` | 获取进程信息 |
| `SetupExitCallback` | 设置退出回调 |
| `init` | 模块初始化 |

## 3. API 函数签名

### pty.fork

```
Usage: pty.fork(file, args, env, cwd, cols, rows, uid, gid, utf8, helperPath, onexit)
```

参数说明：
- `file`: 要执行的程序路径
- `args`: 命令行参数数组
- `env`: 环境变量对象
- `cwd`: 工作目录
- `cols`: 终端列数
- `rows`: 终端行数
- `uid`: 用户 ID
- `gid`: 组 ID
- `utf8`: UTF-8 模式标志
- `helperPath`: 辅助程序路径
- `onexit`: 退出回调函数

### pty.open

```
Usage: pty.open(cols, rows)
```

参数说明：
- `cols`: 终端列数
- `rows`: 终端行数

### pty.resize

```
Usage: pty.resize(fd, cols, rows)
```

参数说明：
- `fd`: 文件描述符
- `cols`: 新列数
- `rows`: 新行数

### pty.process

```
Usage: pty.process(pid)
```

参数说明：
- `pid`: 进程 ID

## 4. 错误消息

### 进程创建错误

```
"posix_spawnp failed."
```

### PTY 打开错误

```
"openpty(3) failed."
```

### ioctl 错误

```
"ioctl(2) failed, EBADF"    # 无效文件描述符
"ioctl(2) failed, EFAULT"   # 无效地址
"ioctl(2) failed, EINVAL"   # 无效参数
"ioctl(2) failed, ENOTTY"   # 不是终端设备
"ioctl(2) failed"           # 通用 ioctl 错误
```

### 线程错误

```
"thread constructor failed"
"ThreadSafeFunction.Release() failed"
"ThreadSafeFunction.BlockingCall() failed"
```

## 5. 系统调用

### PTY 创建

- `openpty(3)` - 打开伪终端对
- `posix_spawnp` - 创建子进程

### 终端控制

- `ioctl(2)` - 终端 I/O 控制
  - `TIOCSWINSZ` - 设置窗口大小

## 6. N-API 绑定

### 使用的 N-API 函数

```
napi_get_last_error_info
napi_is_exception_pending
napi_get_and_clear_last_exception
napi_create_string_utf8
napi_create_error
napi_create_object
napi_define_properties
napi_create_reference
napi_close_escapable_handle_scope
napi_close_handle_scope
```

### ThreadSafeFunction

模块使用 `Napi::ThreadSafeFunction` 实现：
- 子进程退出通知
- 跨线程回调

## 7. PTY 工作流程

### 创建 PTY

1. 调用 `pty.fork()` 或 `pty.open()`
2. 使用 `openpty(3)` 创建主/从终端对
3. 使用 `posix_spawnp` fork 子进程
4. 设置退出回调

### 调整大小

1. 调用 `pty.resize(fd, cols, rows)`
2. 使用 `ioctl(TIOCSWINSZ)` 设置窗口大小
3. 子进程收到 `SIGWINCH` 信号

### 进程退出

1. 子进程退出
2. 触发 `onexit` 回调
3. 清理资源
