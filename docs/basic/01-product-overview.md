# Codex App 产品概述

## 基本信息

| 项目 | 信息 |
|------|------|
| 产品名称 | OpenAI Codex App |
| 开发商 | OpenAI |
| Bundle ID | com.openai.codex |
| 当前版本 | 260208.1016 (Build 571) |
| 技术栈 | Electron 40.0.0 |

## 发布时间线

- **2021年**: OpenAI Codex API 首次发布，为 GitHub Copilot 提供支持
- **2023年3月23日**: 原始 Codex API 停止服务，开发者转向 GPT-3.5-Turbo
- **2025年4月**: Codex App for macOS 首次发布
- **2025年12月**: GPT-5.2-Codex 模型发布
- **2026年2月2日**: Codex App 桌面版正式发布

## 支持平台

| 平台 | 状态 |
|------|------|
| macOS (Apple Silicon: M1/M2/M3+) | ✅ 已支持 |
| macOS 最低版本 | 12.0 |
| Windows | 🔄 开发中 |
| Linux | 🔄 计划中 |

## 订阅计划

| 计划 | 访问权限 |
|------|----------|
| ChatGPT Plus | ✅ 包含 |
| ChatGPT Pro | ✅ 包含 |
| ChatGPT Business | ✅ 包含 |
| ChatGPT Enterprise | ✅ 包含 |
| ChatGPT Edu | ✅ 包含 |
| ChatGPT Free/Go | ⏳ 限时开放 |

- 付费用户享有双倍速率限制
- 使用量计入现有订阅配额

## AI 模型

### GPT-5.2-Codex

| 特性 | 规格 |
|------|------|
| 上下文窗口 | 400,000 tokens |
| 多模态能力 | 支持 UI 设计、图表、截图理解 |
| 发布时间 | 2025年12月 |

**核心能力**:
- 处理大型代码库
- 跨项目保持上下文
- 从截图生成匹配代码
- 理解 UI 设计和架构图

## 核心功能概览

### 1. 多代理编排
- 同时运行多个 AI 代理
- 每个代理在独立的 Git worktree 中工作
- 支持任务切换，避免上下文丢失

### 2. 端到端任务处理
- 编写功能代码
- 修复 Bug
- 回答代码库问题
- 提出 Pull Request

### 3. 自动化任务
- 后台定时执行任务
- 结合 Skills 执行复杂工作流
- 完成的工作进入审核队列

### 4. Git 集成
- 内置 diff 查看
- 行内评论
- 暂存/撤销更改
- 直接提交

## 目标用户

| 用户类型 | 使用场景 |
|----------|----------|
| 非开发者 | 用自然语言描述需求创建工具 |
| 初学者 | 学习代码，观察提示如何转化为代码 |
| 专业开发者 | 加速开发，自动化重复任务 |

## 技术架构（从逆向分析获得）

### 应用框架
- **Electron**: 40.0.0
- **构建工具**: Vite
- **语言**: TypeScript

### 关键依赖
| 模块 | 用途 |
|------|------|
| better-sqlite3 | 本地数据库 |
| node-pty | 终端模拟 |
| electron-liquid-glass | macOS 毛玻璃效果 |
| Sparkle | 自动更新 |
| Sentry | 错误监控 |

### 原生组件
- `codex` CLI 二进制 (ARM64 Mach-O, ~54MB)

## 参考来源

- [OpenAI 官网](https://openai.com)
- [9to5Mac](https://9to5mac.com)
- [Forbes](https://forbes.com)
- [The New Stack](https://thenewstack.io)
