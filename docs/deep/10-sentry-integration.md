# Sentry 监控集成实现分析

## 概述

Codex App 使用 Sentry 进行错误监控和性能追踪。

## Sentry 配置

### DSN
```javascript
// 行号: 20451
const WE = "https://17de4aef18bcd467f0a250b004f16737@o33249.ingest.us.sentry.io/4510507758977024";
```

### 版本标识
```javascript
// 行号: 20452
const nq = "codex-desktop@";
function MU(t) {
  return `${nq}${t}`;
}
```

## 数据上报

### Envelope 格式
```javascript
// 行号: 35349
const l0 = "application/x-sentry-envelope";
const c5 = "application/gzip";
```

### Envelope URL 解析
```javascript
// 行号: 35525-35529
const e = new URL(t);
const r = e.pathname.split("/").filter(Boolean).at(-1);
return `${e.protocol}//${e.host}/api/${r}/envelope/`;
```

## IPC 通道

### Sentry 相关 IPC
```javascript
// 行号: 36949-36951
g7 = "codex_desktop:get-sentry-init-options"
_7 = "codex_desktop:trigger-sentry-test"
```

## 总结

Sentry 监控集成的核心设计：
1. **DSN 配置** - 硬编码 Sentry 项目
2. **Envelope 格式** - 标准 Sentry 协议
3. **Gzip 压缩** - 减少上报数据量
4. **IPC 集成** - 渲染进程获取配置
