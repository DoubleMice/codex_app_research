# Electron 窗口和安全配置分析

## 概述

Codex Desktop 使用 Electron 40.0.0 构建，采用严格的安全配置。

## 1. BrowserWindow 创建

```javascript
y = new L.BrowserWindow({
  width: m,
  height: h,
  ...(v === void 0 || g === void 0 ? {} : { x: v, y: g }),
  title: n ?? "Codex",
  backgroundColor: uk,
  show: o,
  ...p,
  ...(a === "primary"
    ? { minWidth: S.width, minHeight: S.height }
    : { minWidth: WN, minHeight: YN }),
  webPreferences: {
    preload: this.options.preloadPath,
    contextIsolation: !0,
    nodeIntegration: !1,
    spellcheck: !1,
    devTools: this.options.allowDevtools,
  },
});
```

## 2. 安全配置 (webPreferences)

| 配置项 | 值 | 说明 |
|--------|-----|------|
| contextIsolation | true | 上下文隔离启用 |
| nodeIntegration | false | Node.js 集成禁用 |
| spellcheck | false | 拼写检查禁用 |
| devTools | 动态 | 根据配置决定 |
| preload | 路径 | 预加载脚本 |

### 安全最佳实践

- **contextIsolation: true** - 防止渲染进程访问 Node.js API
- **nodeIntegration: false** - 禁止直接使用 require()
- **preload 脚本** - 通过 contextBridge 安全暴露 API

## 3. 窗口尺寸配置

### 默认尺寸

| 窗口类型 | 宽度 | 高度 |
|----------|------|------|
| 主窗口 | 1280 | 820 |
| HUD 窗口 | 500 | 800 |
| 辅助窗口 | 1024 | 720 |

### 最小尺寸

```javascript
// 主窗口最小尺寸
{ minWidth: S.width, minHeight: S.height }

// 其他窗口最小尺寸
{ minWidth: WN, minHeight: YN }
```

## 4. 标题栏样式

### macOS 配置

```javascript
// Liquid Glass 效果
{
  titleBarStyle: "hiddenInset",
  transparent: !0,
}

// 标准 Vibrancy
{
  vibrancy: "menu",
  titleBarStyle: "hiddenInset",
}

// 默认样式
{
  titleBarStyle: "default",
  backgroundColor: uk,
}
```

### 平台差异

| 平台 | titleBarStyle | 特性 |
|------|---------------|------|
| macOS | hiddenInset | 隐藏标题栏，保留交通灯 |
| Windows | default | 标准标题栏 |
| Linux | default | 标准标题栏 |

## 5. Liquid Glass 效果

```javascript
// 检测 Liquid Glass 支持
import("electron-liquid-glass")
  .then(module => {
    module.default.isGlassSupported?.() ?? false
  });

// 回退到 Vibrancy
if (!liquidGlassSupported) {
  e.setVibrancy("menu");
}
```

## 6. DevTools 配置

```javascript
// DevTools 允许检查
allowDevtools: t,

// 配置来源
Lp = Je.allowDevtools(sn),

// 窗口配置
devTools: this.options.allowDevtools,
```

## 7. 窗口事件处理

### 标题更新

```javascript
y.on("page-title-updated", (q) => {
  q.preventDefault();
  y.isDestroyed() || y.setTitle(y.getTitle());
});
```

### 关闭处理 (macOS)

```javascript
y.on("close", (q) => {
  this.persistPrimaryWindowBounds(y, c);

  if (process.platform === "darwin" &&
      !this.isAppQuitting &&
      L.BrowserWindow.getAllWindows().length <= 1) {

    if (y.isFullScreen()) {
      q.preventDefault();
      y.once("leave-full-screen", () => {
        y.isDestroyed() || y.hide();
      });
      y.setFullScreen(!1);
      return;
    }

    q.preventDefault();
    y.hide();
  }
});
```

## 8. 窗口管理 API

```javascript
// 获取焦点窗口
L.BrowserWindow.getFocusedWindow()

// 获取所有窗口
L.BrowserWindow.getAllWindows()

// 从 WebContents 获取窗口
L.BrowserWindow.fromWebContents(e)
```

## 9. 沙箱模式配置

### 模式定义

```javascript
{
  "read-only": { sandboxMode: "read-only", approvalPolicy: "on-request" },
  auto: { sandboxMode: "workspace-write", approvalPolicy: "on-request" },
  "full-access": { sandboxMode: "danger-full-access", approvalPolicy: "never" },
}
```

### 沙箱策略

| 模式 | sandboxMode | approvalPolicy |
|------|-------------|----------------|
| read-only | read-only | on-request |
| auto | workspace-write | on-request |
| full-access | danger-full-access | never |

## 10. Content Security Policy

```html
<meta http-equiv="Content-Security-Policy" content="
  default-src 'none';
  img-src 'self' https: data: blob:;
  child-src 'self' blob:;
  frame-src 'self' blob:;
  worker-src 'self' blob:;
  script-src 'self' 'wasm-unsafe-eval' 'sha256-...';
  style-src 'self' 'unsafe-inline';
  font-src 'self' data:;
  media-src 'self' blob:;
  connect-src 'self' https://ab.chatgpt.com https://cdn.openai.com;
">
```

## 11. 窗口类型

| 类型 | 说明 |
|------|------|
| primary | 主窗口 |
| hud | HUD 窗口 |
| auxiliary | 辅助窗口 |

## 12. 背景色配置

```javascript
backgroundColor: uk  // 预定义背景色常量
```

## 13. 窗口状态持久化

```javascript
// 保存窗口边界
this.persistPrimaryWindowBounds(y, c);

// 恢复窗口状态
f?.width ?? r,
f?.height ?? i,
f?.x,
f?.y,
f?.isMaximized === !0
```

## 14. 安全配置总结

| 特性 | 状态 | 说明 |
|------|------|------|
| contextIsolation | ✅ 启用 | 隔离渲染进程 |
| nodeIntegration | ❌ 禁用 | 禁止 Node.js |
| sandbox | ✅ 启用 | 进程沙箱 |
| CSP | ✅ 严格 | 内容安全策略 |
| preload | ✅ 使用 | 安全桥接 |

