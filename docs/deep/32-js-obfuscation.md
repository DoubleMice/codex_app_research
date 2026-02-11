# JS 代码混淆分析

## 概述

Codex Desktop 的 JS 代码经过 Vite/Rollup 打包和 Terser 压缩，呈现典型的生产构建混淆特征。

**文件**:
- `extracted/app/.vite/build/main-BLcwFbOH.js` (~2.1MB)
- `extracted/app/.vite/build/worker-C_GFIvBQ.js`
- `extracted/app/.vite/build/preload.js`

## 1. 混淆工具识别

### 构建工具链

```
Vite + Rollup + Terser
```

特征：
- 文件名包含哈希: `main-BLcwFbOH.js`
- `.vite/build/` 目录结构
- 模块打包格式

## 2. 变量名混淆模式

### 单字母变量

```javascript
// 函数参数
function wr(t) { ... }
function ja(t) { ... }

// 循环变量
for (let i of QB(e))
```

### 双字母变量

```javascript
var XB = Object.create;
var ZT = Object.defineProperty;
var JB = Object.getOwnPropertyDescriptor;
var QB = Object.getOwnPropertyNames;
```

### 组合变量名

```javascript
// 类名
class Ii extends Error { }
class Bl extends Error { }
class HU { }
class YU { }
class HH { }

// 函数名
function dz() { }
function fz() { }
function Ek() { }
```

## 3. 模块导入模式

### CommonJS 导入

```javascript
const L = require("electron");
const vt = require("fs");
const ot = require("path");
const rz = require("https");
const Fa = require("stream");
const Ll = require("events");
const $a = require("child_process");
```

### Node.js 模块

```javascript
const ve = require("node:fs");
const te = require("node:path");
const sc = require("node:url");
const Dt = require("node:os");
const Ft = require("node:crypto");
const Sn = require("node:child_process");
const Ls = require("node:buffer");
```

## 4. 字符串保留

### 保留的字符串类型

1. **IPC 通道名**: `codex_desktop:*`
2. **错误消息**: 完整保留
3. **API 端点**: URL 字符串
4. **配置键名**: 配置项名称

## 5. 代码结构特征

### 模块包装

```javascript
var nz = (t, e, n, r) => {
  if ((e && typeof e == "object") || typeof e == "function")
    for (let i of QB(e))
      !tz.call(t, i) &&
        i !== n &&
        ZT(t, i, {
          get: () => e[i],
          enumerable: !(r = JB(e, i)) || r.enumerable,
        });
  return t;
};
```

### 默认导出包装

```javascript
var Cr = (t, e, n) => (
  (n = t != null ? XB(ez(t)) : {}),
  nz(
    e || !t || !t.__esModule
      ? ZT(n, "default", { value: t, enumerable: !0 })
      : n,
    t,
  )
);
```

## 6. 混淆程度评估

| 特征 | 程度 | 说明 |
|------|------|------|
| 变量名混淆 | 中等 | 短变量名，但保留语义 |
| 字符串混淆 | 低 | 字符串基本保留 |
| 控制流平坦化 | 无 | 未使用 |
| 死代码注入 | 无 | 未使用 |
| 代码压缩 | 高 | 空白和注释移除 |

## 7. 逆向分析建议

1. **搜索字符串**: IPC 通道名、错误消息可直接搜索
2. **类定义**: `class` 关键字保留，可定位类
3. **函数调用**: `require()` 调用保留模块名
4. **事件监听**: `addEventListener`、`on` 方法保留
