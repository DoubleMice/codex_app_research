# Sparkle 自动更新实现分析

## 概述

Codex App 使用 Sparkle 框架实现 macOS 自动更新。

## Sparkle 管理器

### 日志模块
```javascript
// 行号: 35730
const p0 = ht("sparkle");
```

### 配置选项
```javascript
// 行号: 35733-35741
{
  allowDevtools: e,
  allowDebugMenu: n,
  allowWindowReload: r,
  enableSparkle: i,
  sparkleManager: a,
}
```

## 更新检查

### 后台检查
```javascript
// 行号: 59684-59697
Tt().debug("Sparkle background check.");
if (typeof a.checkForUpdatesInBackground == "function") {
  a.checkForUpdatesInBackground();
}
```

### 定时检查
```javascript
// 行号: 59700-59704
const u = this.resolveIntervalMs();
if (u > 0) {
  Tt().info("Sparkle scheduling interval (ms)", u);
  setInterval(c, u).unref();
}
```

## IPC 集成

### 手动检查更新
```javascript
// 行号: 59706-59707
L.ipcMain.handle("codex_desktop:check-for-updates", async (l) => {
  this.options.isTrustedIpcEvent(l) && s();
});
```

## 总结

Sparkle 自动更新的核心设计：
1. **原生模块** - sparkle.node 原生绑定
2. **后台检查** - checkForUpdatesInBackground
3. **定时轮询** - setInterval 定期检查
4. **IPC 触发** - 支持手动检查更新
