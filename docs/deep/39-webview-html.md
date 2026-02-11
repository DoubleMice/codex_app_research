# Webview/index.html 和资源加载分析

## 概述

Codex Desktop 渲染进程的 HTML 入口和资源加载结构。

## 1. index.html 结构

```html
<!doctype html>
<html lang="en">
  <head>
    <!-- PROD_BASE_TAG_HERE -->
    <!-- PROD_CSP_TAG_HERE -->
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

## 2. Meta 标签分析

| 标签 | 值 | 说明 |
|------|-----|------|
| charset | UTF-8 | 字符编码 |
| viewport | width=device-width, initial-scale=1.0 | 响应式视口 |
| Content-Security-Policy | 严格策略 | 安全策略 |

## 3. Content Security Policy 详解

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

### CSP 指令说明

| 指令 | 值 | 说明 |
|------|-----|------|
| default-src | 'none' | 默认禁止所有资源 |
| img-src | 'self' https: data: blob: | 允许本地、HTTPS、data URI、blob 图片 |
| child-src | 'self' blob: | 允许本地和 blob iframe |
| frame-src | 'self' blob: | 允许本地和 blob frame |
| worker-src | 'self' blob: | 允许本地和 blob Worker |
| script-src | 'self' 'wasm-unsafe-eval' sha256-... | 允许本地脚本、WASM、特定哈希 |
| style-src | 'self' 'unsafe-inline' | 允许本地和内联样式 |
| font-src | 'self' data: | 允许本地和 data URI 字体 |
| media-src | 'self' blob: | 允许本地和 blob 媒体 |
| connect-src | 'self' ab.chatgpt.com cdn.openai.com | 允许连接的域名 |

## 4. 资源加载

### 主入口文件

| 文件 | 类型 | 说明 |
|------|------|------|
| index-BnRAGF7J.js | JavaScript | 主应用入口 |
| index-BXVXZOU0.css | CSS | 主样式表 |

### 加载属性

```html
<script type="module" crossorigin src="./assets/index-BnRAGF7J.js"></script>
<link rel="stylesheet" crossorigin href="./assets/index-BXVXZOU0.css">
```

- **type="module"** - ES 模块加载
- **crossorigin** - 跨域资源共享

## 5. 目录结构

```
webview/
├── index.html              # HTML 入口
├── assets/                 # 静态资源
│   ├── index-BnRAGF7J.js   # 主 JS (2896 行)
│   ├── index-BXVXZOU0.css  # 主 CSS
│   └── *.js                # 433 个 JS 模块
├── apps/                   # 应用图标 (20 个)
│   ├── vscode.png
│   ├── cursor.png
│   ├── terminal.png
│   └── ...
└── colorcons/              # 彩色图标 (90 个)
    ├── agent.png
    ├── brain.png
    ├── chat.png
    └── ...
```

## 6. 资源统计

| 类型 | 数量 | 说明 |
|------|------|------|
| JS 模块 | 433 | 代码分割模块 |
| CSS 文件 | 1 | 主样式表 |
| 应用图标 | 20 | IDE/终端图标 |
| 彩色图标 | 90 | UI 图标 |

## 7. 应用图标列表

| 图标 | 应用 |
|------|------|
| vscode.png | Visual Studio Code |
| vscode-insiders.png | VS Code Insiders |
| cursor.png | Cursor |
| terminal.png | Terminal |
| iterm2.png | iTerm2 |
| ghostty.png | Ghostty |
| warp.png | Warp |
| intellij.png | IntelliJ IDEA |
| pycharm.png | PyCharm |
| goland.png | GoLand |
| webstorm.svg | WebStorm |
| rustrover.png | RustRover |
| android-studio.png | Android Studio |
| bbedit.png | BBEdit |
| textmate.png | TextMate |
| finder.png | Finder |
| antigravity.png | Antigravity |

## 8. Body 配置

```html
<body tabindex="0" style="outline: none">
  <div id="root"></div>
</body>
```

- **tabindex="0"** - 可聚焦
- **outline: none** - 无焦点轮廓
- **#root** - React 挂载点

## 9. 构建占位符

```html
<!-- PROD_BASE_TAG_HERE -->
<!-- PROD_CSP_TAG_HERE -->
```

这些注释在开发时作为占位符，生产构建时被替换。

## 10. 代码分割模块

### 语言高亮模块

```
javascript-wDzz0qaB.js
typescript-BPQ3VLAy.js
python-B6aJPvgy.js
rust-*.js
go-*.js
java-CylS5w8V.js
...
```

### 功能模块

```
agent-settings-CiwAy4XS.js
automation-B4itk_tW.js
channel-DF6SLYYj.js
shiki-highlight-provider-BwvW0IpZ.js
...
```

### 图表模块 (Mermaid)

```
architectureDiagram-VXUJARFQ-NeWwMAjx.js
blockDiagram-VD42YOAC-8Irvq_Hj.js
c4Diagram-YG6GDRKO-DHtzyeU4.js
...
```

## 11. 国际化资源

```
am-DDggQMsB.js      # 阿姆哈拉语
ar-BlBdr6v3.js      # 阿拉伯语
bg-BG-CTYvD2j3.js   # 保加利亚语
bn-BD-BLAYQ-0e.js   # 孟加拉语
bs-BA-BfYrUDGh.js   # 波斯尼亚语
ca-ES-BZdHeUwr.js   # 加泰罗尼亚语
...
```

## 12. 安全特性总结

| 特性 | 状态 | 说明 |
|------|------|------|
| CSP | ✅ 严格 | 限制资源来源 |
| ES Modules | ✅ 使用 | 现代模块系统 |
| CORS | ✅ 配置 | crossorigin 属性 |
| 脚本哈希 | ✅ 使用 | sha256 验证 |

