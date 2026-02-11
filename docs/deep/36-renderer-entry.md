# 渲染进程入口代码逆向分析

## 概述

Codex Desktop 渲染进程使用 React 构建，通过 Vite 打包。

**入口文件**: `webview/assets/index-BnRAGF7J.js`
**文件大小**: ~2896 行 (压缩后)
**框架**: React 18 + TanStack Query

## 1. HTML 入口

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Codex</title>
    <script type="module" crossorigin src="./assets/index-BnRAGF7J.js"></script>
    <link rel="stylesheet" crossorigin href="./assets/index-BXVXZOU0.css">
    <meta http-equiv="Content-Security-Policy" content="...">
  </head>
  <body tabindex="0" style="outline: none">
    <div id="root"></div>
  </body>
</html>
```

## 2. Content Security Policy

```
default-src 'none';
img-src 'self' https: data: blob:;
child-src 'self' blob:;
frame-src 'self' blob:;
worker-src 'self' blob:;
script-src 'self' 'wasm-unsafe-eval' 'sha256-Z2/iFzh9VMlVkEOar1f/oSHWwQk3ve1qk/C2WdsC4Xk=';
style-src 'self' 'unsafe-inline';
font-src 'self' data:;
media-src 'self' blob:;
connect-src 'self' https://ab.chatgpt.com https://cdn.openai.com;
```

### CSP 分析

| 指令 | 值 | 说明 |
|------|------|------|
| default-src | 'none' | 默认禁止所有 |
| img-src | 'self' https: data: blob: | 允许本地和远程图片 |
| script-src | 'self' 'wasm-unsafe-eval' | 允许本地脚本和 WASM |
| connect-src | 'self' https://ab.chatgpt.com https://cdn.openai.com | 允许连接的域名 |

## 3. React 应用初始化

```javascript
// 根容器获取
const OVe = document.getElementById("root");
if (!OVe) throw new Error("Root container not found");

// 单例 Root 创建
const aX = window;
aX.__codexRoot || (aX.__codexRoot = yGe.createRoot(OVe));
const TEn = aX.__codexRoot;

// 渲染应用
TEn.render(p.jsx(T.StrictMode, {
  children: p.jsx(Zkn, {  // 顶层 Provider
    children: p.jsx(cCn, {  // 配置 Provider
      children: p.jsx(nEn, {  // 主题 Provider
        children: p.jsx(dGe, { store: kM,  // Redux Store
          children: p.jsx(aKe, {  // 国际化 Provider
            children: p.jsxs(aCn, {  // 路由 Provider
              children: [...]
            })
          })
        })
      })
    })
  })
}));
```

## 4. Provider 层级结构

```
StrictMode
└── Zkn (顶层 Provider)
    └── cCn (配置 Provider)
        └── nEn (主题 Provider)
            └── dGe (Redux Store Provider)
                └── aKe (国际化 Provider)
                    └── aCn (路由 Provider)
                        └── AWe (AppRoutes)
```

## 5. 核心组件列表

### 路由组件

| 组件 | 说明 |
|------|------|
| ISn | 主路由 |
| Kkn | 扩展路由 |
| mkn | 菜单路由 |

### 功能组件

| 组件 | 说明 |
|------|------|
| SEn | 会话管理 (ek) |
| DSn | 对话列表 |
| VSn | 设置面板 |
| gSn | 工具面板 |
| NSn | 通知面板 |
| iSn | Electron 专用 |
| oSn | Electron 专用 |
| LCn | 配置面板 |
| nCn | 网络面板 |
| lEn | Worktree 初始化 |
| KSn | 快捷键 |
| bEn | 分析追踪 |
| MSn | MCP 面板 |
| cEn | 自动化清理 |
| pEn | Worktree 清理 |

## 6. 状态管理

### Redux Store

```javascript
dGe({ store: kM, children: ... })
```

### TanStack Query

```javascript
// 查询客户端
const Jr = () => queryClient;

// 查询 Hook
const xn = (key) => useQuery(key);

// 查询失效
t.invalidateQueries({ queryKey: tO(o) });
```

## 7. 事件监听

### 全局错误处理

```javascript
// 全局错误捕获
window.addEventListener("error", t => {
  const e = t?.error?.stack ?? t?.error?.message ?? t?.message ?? "Unknown error";
  Xt.dispatchMessage("log-message", {
    level: "error",
    message: `[desktop-notifications][global-error] ${String(e)}`
  });
});

// Promise 拒绝捕获
window.addEventListener("unhandledrejection", t => {
  const e = t.reason;
  const n = typeof e == "object" && e != null
    ? e.stack ?? e.message ?? JSON.stringify(e)
    : String(e);
  Xt.dispatchMessage("log-message", {
    level: "error",
    message: `[desktop-notifications][unhandled-rejection] ${n}`
  });
});
```

### 窗口焦点事件

```javascript
function wEn() {
  Xt.dispatchMessage("ready", {});
  const t = _En;
  window.addEventListener("focus", t);
  document.hasFocus() && t();
  return () => {
    window.removeEventListener("focus", t);
  };
}

function _En() {
  Xt.dispatchMessage("view-focused", {});
}
```

## 8. 窗口类型标识

```javascript
// 设置窗口类型
document.documentElement.dataset.codexWindowType = "electron";
document.documentElement.dataset.windowType = "electron";
```

## 9. 懒加载模块

### 代码高亮 Provider

```javascript
const yEn = T.lazy(async () => ({
  default: (await Ee(() => import("./shiki-highlight-provider-BwvW0IpZ.js"), [], import.meta.url))
    .ShikiHighlightProvider
}));
```

### 动态导入映射

```javascript
const __vite__mapDeps = (i, m = __vite__mapDeps, d = (m.f || (m.f = [
  "./angular-html-CU67Zn6k.js",
  "./html-GMplVEZG.js",
  "./javascript-wDzz0qaB.js",
  "./css-DPfMkruS.js",
  // ... 更多语言高亮模块
]))) => d[i];
```

## 10. Worktree 管理

### Worktree 初始化组件

```javascript
function lEn() {
  const t = Jr();  // QueryClient
  const e = Mr();  // 会话管理
  const n = zP({ includeCompleted: true });  // 待处理 Worktree
  const { updatePendingWorktree: r } = Ly();

  // 创建 Worktree
  const y = T.useEffectEvent(async x => {
    r(x.id, _ => { _.phase = "creating" });
    try {
      const _ = await Jl("git").request({
        method: "create-worktree",
        params: {
          hostConfig: s,
          cwd: x.sourceWorkspaceRoot,
          startingState: x.startingState,
          localEnvironmentConfigPath: x.localEnvironmentConfigPath,
          streamId: x.streamId
        },
        signal: x.abortController.signal
      });
      // ...
    } catch (_) {
      // 错误处理
    }
  });
}
```

### Worktree 清理策略

```javascript
const hEn = 10;  // LRU 保留数量
const fEn = 5760 * 60 * 1000;  // TTL: 4 天

function vEn({ worktrees, conversations, workspaceRoots, pinnedThreadIds, pendingRoots, policy }) {
  // 清理逻辑
  // 1. 保留 pinned 的 worktree
  // 2. 保留有 workspace root 的 worktree
  // 3. 保留 pending 的 worktree
  // 4. LRU 保留最近使用的
  // 5. 删除超过 TTL 的
}
```

## 11. 自动化运行清理

```javascript
function cEn() {
  // 清理待处理的自动化运行线程
  const s = async c => {
    const u = e?.threadIds ?? [];
    if (u.length === 0) return;

    jt.info("[automation-run-cleanup] archiving pending automation runs");

    // 刷新会话列表
    await r.refreshRecentConversations();

    // 归档会话
    for (const g of u) {
      await r.archiveConversation(v);
    }
  };
}
```

## 12. 分析追踪

```javascript
function bEn() {
  const e = Dr();  // 环境检测
  const n = v1();  // 主机配置
  const r = I1();  // 追踪函数

  // 窗口打开事件
  T.useEffect(() => {
    if (e === "electron") {
      i({ eventName: "codex_app_window_opened" });
      return () => {
        i({ eventName: "codex_app_window_closed" });
      };
    }
  }, [e]);

  // 远程主机窗口事件
  T.useEffect(() => {
    const d = n?.kind;
    if (d != null && d !== "local") {
      i({
        eventName: "codex_app_remote_host_window_opened",
        metadata: { kind: d }
      });
    }
  }, [n?.kind, e]);
}
```

## 13. 导出的模块

```javascript
export {
  Df as $,
  Jr as A,           // QueryClient
  Xe as B,
  GTe as C,
  MIt as D,
  cK as E,
  ase as F,
  qs as G,           // 通知设置
  Xo as H,
  t$e as I,
  as as J,
  Tt as K,
  fr as L,
  Z as M,
  KEn as N,
  Ur as O,
  hi as P,
  qr as Q,           // 查询时间
  dEn as R,
  zN as S,
  h9 as T,
  wP as U,
  // ... 更多导出
  Xt as bU,          // IPC 消息分发
  jt as bV,          // 日志
  Bn as bW,          // 错误格式化
  // ...
};
```

## 14. 资源文件结构

```
webview/
├── index.html              # HTML 入口
├── assets/
│   ├── index-BnRAGF7J.js   # 主入口 JS
│   ├── index-BXVXZOU0.css  # 主样式
│   ├── shiki-highlight-provider-BwvW0IpZ.js  # 代码高亮
│   ├── angular-html-CU67Zn6k.js  # 语言高亮模块
│   ├── javascript-wDzz0qaB.js
│   ├── typescript-BPQ3VLAy.js
│   └── ...                 # 更多语言模块
├── apps/                   # 子应用
└── colorcons/              # 颜色图标
```

