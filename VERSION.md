# Codex.dmg 版本说明

## 版本信息

| 属性 | 值 |
|------|------|
| 文件名 | Codex.dmg |
| 版本号 | 260208.1016 |
| 构建号 | 571 |
| 文件大小 | 151 MB |
| 下载日期 | 2026-02-10 |

## 技术规格

- **平台**: macOS (arm64)
- **最低系统**: macOS 12.0 (Monterey)
- **Electron**: 40.0.0
- **Node.js**: 22.x

## 来源

```
https://persistent.oaistatic.com/codex-app-prod/Codex-darwin-arm64-260208.1016.zip
```

## extracted 目录结构

```
extracted/
├── codex          # Rust CLI 二进制 (54 MB)
└── app/           # Electron 应用资源
    ├── package.json
    ├── .vite/build/
    │   ├── main-BLcwFbOH.js
    │   ├── worker-C_GFIvBQ.js
    │   └── preload.js
    ├── native/
    │   └── sparkle.node
    ├── node_modules/
    └── webview/
```
