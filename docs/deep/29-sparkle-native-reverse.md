# sparkle.node 原生模块逆向分析

## 概述

sparkle.node 是 Codex Desktop 的自动更新原生模块，封装了 macOS Sparkle 框架。

**文件**: `extracted/app/native/sparkle.node`
**架构**: arm64 (Apple Silicon)
**语言**: C++ (N-API 绑定)

## 1. 链接库

```
@rpath/Sparkle.framework/Versions/B/Sparkle (2.8.0)
/usr/lib/libc++.1.dylib
/usr/lib/libSystem.B.dylib
```

## 2. 导出符号

### Objective-C 类

```
_OBJC_CLASS_$_CodexSparkleDelegate
_OBJC_METACLASS_$_CodexSparkleDelegate
```

### N-API 入口

```
_napi_register_module_v1        # 模块注册入口
_node_api_module_get_api_version_v1  # API 版本
```

### codex::sparkle 命名空间函数

| 符号 | 说明 |
|------|------|
| `codex::sparkle::Init` | 初始化 Sparkle |
| `codex::sparkle::InitAll` | 初始化所有导出 |
| `codex::sparkle::CheckForUpdates` | 检查更新 |
| `codex::sparkle::CheckForUpdatesInBackground` | 后台检查更新 |
| `codex::sparkle::InstallUpdatesIfAvailable` | 安装可用更新 |
| `codex::sparkle::SetUpdateReadySink` | 设置更新就绪回调 |
| `codex::sparkle::SetLogSink` | 设置日志回调 |

## 3. Sparkle 委托协议

```objc
// 实现的 Sparkle 委托协议
SPUUpdaterDelegate
SPUStandardUserDriverDelegate
```

## 4. 日志消息

### 更新检查流程

```
"CheckForUpdates called (initRequested=%d)"
"Controller ready, invoking checkForUpdates"
"Controller not ready; latching manual check"
"Background check requested (initRequested=%d)"
"Skipping background check; install-on-quit pending"
"Controller ready, invoking background check"
"Controller not ready; latching background check"
```

### 更新状态

```
"No update found."
"Valid update found: %@ (%@)"
"Update downloaded."
"Update ready to install on quit: %@ (%@)"
"Update failed to download: %@"
"User canceled update download."
"Update aborted with error: %@"
```

### 安装流程

```
"install-on-quit selected; disabling automatic checks"
"Install requested; update is staged for install-on-quit. Terminating app to allow Sparkle to install."
"Install requested but no immediate installation block is available; showing Sparkle update UI."
```

## 5. 错误消息

```
"Sparkle init requires a feed URL string"
"Sparkle init requires a non-empty feed URL"
"Sparkle init received an invalid feed URL"
"Sparkle setUpdateReadySink requires a function (or null)"
"Sparkle setLogSink requires a function (or null)"
```

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

### Napi::ThreadSafeFunction

模块使用 `Napi::ThreadSafeFunction` 实现跨线程回调，用于：
- 更新就绪通知 (`sparkleUpdateReadySink`)
- 日志输出

## 7. 更新流程分析

### 初始化

1. 调用 `Init(feedUrl)` 设置 Appcast URL
2. 创建 `CodexSparkleDelegate` 实例
3. 配置 Sparkle 更新器

### 检查更新

1. `CheckForUpdates()` - 手动检查（显示 UI）
2. `CheckForUpdatesInBackground()` - 后台静默检查

### 安装更新

1. 更新下载完成后触发 `updateReadySink` 回调
2. 调用 `InstallUpdatesIfAvailable()` 安装
3. 支持 "install-on-quit" 模式

## 8. Sparkle 框架版本

- 兼容版本: 1.6.0
- 当前版本: 2.8.0

Sparkle 2.x 特性：
- 支持 Apple Silicon
- 改进的安全性
- 支持 Delta 更新
- 支持 XPC 服务
