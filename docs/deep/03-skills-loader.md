# Skills 加载器实现分析

## 概述

Skills 系统允许用户扩展 Codex App 的功能。

## Skills 目录结构

```
~/.codex/skills/
├── skill-name/
│   ├── SKILL.md      # 技能描述
│   └── agents/
│       └── openai.yaml
```

## 官方 Skills 仓库

```javascript
j9 = "https://github.com/openai/skills.git"
// 行号: 32910
```

## Skill 元数据解析

### 字段映射
```javascript
TR = {
  name: "name",
  description: "description",
  "short-description": "shortDescription",
  "icon-small": "iconSmall",
  "icon-large": "iconLarge"
}
```

## Skill 加载流程

### 目录扫描
```javascript
// 行号: 33023-33061
async function q9({ recommendedRoot, repoRoot, path, hostConfig })
```

### 加载步骤
1. 读取目录列表
2. 过滤隐藏文件 (`.` 开头)
3. 检查 SKILL.md 存在
4. 解析元数据

## Skill 数据结构

```javascript
{
  id: "skill-name",
  name: "Skill Name",
  description: "描述",
  shortDescription: "简短描述",
  iconSmall: "图标路径",
  iconLarge: "大图标路径",
  repoPath: "仓库路径"
}
```

## Agent 配置解析

### openai.yaml 解析
```javascript
// 行号: 33125-33131
async function Y9({ path: t, hostConfig: e, skillRoot: n }) {
  const r = t.join(n, "agents", "openai.yaml");
  if (!(await er(r, e)))
    return { shortDescription: null, iconSmall: null, iconLarge: null };
  const i = await Ke.readFile(r, e);
  return Z9(i);
}
```

### Interface 段解析
```javascript
// 行号: 33132-33154
function Z9(t) {
  // 解析 interface: 段落
  // 提取 shortDescription, iconSmall, iconLarge
}
```

## 图标资源解析

### 图标搜索优先级
```javascript
// 行号: 33155-33199
// Small 图标搜索顺序:
[
  "${skillId}-small.svg",
  "${skillId}-small.png",
  "small.svg",
  "small.png",
  "${skillId}.svg",
  "${skillId}.png",
  "icon-small.svg",
  "icon-small.png"
]

// Large 图标搜索顺序:
[
  "${skillId}.png",
  "${skillId}.svg",
  "icon.png",
  "icon.svg",
  "${skillId}-large.png",
  "${skillId}-large.svg"
]
```

## 官方仓库同步

### Git Clone (Sparse)
```javascript
// 行号: 33209-33224
git clone --depth 1 --filter=blob:none --sparse <repo> <path>
```

### Git Fetch/Reset
```javascript
// 行号: 33225-33234
git fetch --depth 1 origin <branch>
git reset --hard FETCH_HEAD
```

### Sparse Checkout
```javascript
// 行号: 33235-33239
git sparse-checkout set <skill-paths>
```

## 关键代码位置

| 功能 | 行号 | 说明 |
|------|------|------|
| 元数据映射 | 33110-33124 | TR/W9 字段映射 |
| YAML 解析 | 33125-33154 | Y9/Z9 函数 |
| 图标解析 | 33155-33199 | K9/xR 函数 |
| 仓库克隆 | 33209-33224 | X9 函数 |
| 仓库更新 | 33225-33234 | J9 函数 |

## 总结

Skills 加载器的核心设计：
1. **YAML 配置** - 使用 openai.yaml 定义 Agent
2. **Sparse Checkout** - 按需下载官方 Skills
3. **图标自动发现** - 多种命名约定支持
4. **增量更新** - 浅克隆 + fetch/reset
