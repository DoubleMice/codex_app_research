# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a reverse engineering research project analyzing OpenAI Codex Desktop App architecture. The project contains extracted app files and comprehensive analysis documentation.

## Directory Structure

```
.
├── Codex.dmg                    # Original macOS app installer
├── extracted/
│   ├── app/                     # Extracted Electron app (JS, native modules)
│   │   ├── main-*.js            # Main process (60K+ lines, minified)
│   │   ├── worker-*.js          # Worker thread
│   │   ├── webview/             # Renderer process (React 18)
│   │   └── node_modules/        # Native modules (pty, sqlite, sparkle)
│   └── codex                    # Rust CLI binary (150MB)
├── docs/
│   ├── basic/                   # 15 basic analysis documents (01-15)
│   ├── deep/                    # 43 deep analysis documents (01-43)
│   └── codex-app-analysis-report.md  # Main comprehensive report (25 chapters)
└── README.md
```

## Analysis Workflow

### Extracting Evidence from JS Files

```bash
# Search for patterns in minified JS
grep -n "pattern" extracted/app/main-*.js

# Search across all JS files
grep -rn "pattern" extracted/app/
```

### Extracting Evidence from Binary

```bash
# Extract strings from Rust CLI
strings extracted/codex | grep "pattern"

# Analyze symbols
nm extracted/codex | grep "pattern"

# Check linked libraries
otool -L extracted/codex
```

### Analyzing Native Modules

```bash
# List native module exports
nm extracted/app/node_modules/*/build/Release/*.node

# Check dependencies
otool -L extracted/app/node_modules/*/build/Release/*.node
```

## Documentation Standards

All analysis must include code evidence. Format:

```markdown
### 【代码证据】Feature Name

**来源**: `file-path:line-number` 或 `binary strings 提取`

\`\`\`javascript
// actual code snippet
\`\`\`
```

## Key Files for Analysis

| File | Content |
|------|---------|
| `extracted/app/main-*.js` | Main process logic, IPC handlers |
| `extracted/app/worker-*.js` | Git operations, file I/O |
| `extracted/app/webview/assets/index-*.js` | React renderer entry |
| `extracted/app/preload.js` | electronBridge API |
| `extracted/codex` | Rust CLI with App Server Protocol |

## Language

Documentation is written in Chinese (简体中文). Analysis reports and code comments should maintain this convention.
