# Skills 自定义技能系统分析

## 概述

Codex App 的 Skills 系统允许用户创建和使用自定义技能，扩展 AI 助手的能力。

## Skills 存储位置

| 位置 | 说明 |
|------|------|
| `~/.codex/skills/` | 用户自定义 Skills |
| `skills/.curated` | 官方精选 Skills |
| `skills/.experimental` | 实验性 Skills |
| `vendor_imports/skills` | 第三方导入 Skills |

## 官方 Skills 仓库

- **仓库地址**: https://github.com/openai/skills.git
- **缓存文件**: `skills-curated-cache.json`

## Skill 定义格式

### 目录结构
```
skill-name/
├── SKILL.md          # Skill 说明文档
├── agents/
│   └── openai.yaml   # OpenAI 代理配置
└── assets/
    ├── icon-small.*  # 小图标
    └── icon-large.*  # 大图标
```

### openai.yaml 配置
```yaml
interface:
  short_description: "技能简短描述"
  icon_small: "icon-small.png"
  icon_large: "icon-large.png"
```

### 字段名映射
```javascript
{
  short_description: "shortDescription",
  shortDescription: "shortDescription",
  "short-description": "shortDescription",
  icon_small: "iconSmall",
  iconSmall: "iconSmall",
  "icon-small": "iconSmall",
  icon_large: "iconLarge",
  iconLarge: "iconLarge",
  "icon-large": "iconLarge"
}
```

## Skill 引用方式

在 Automation 提示词中引用 Skill：
```markdown
[$skill-name](/Users/username/.codex/skills/skill-name/SKILL.md)
```

- 使用 `$` 前缀标识 Skill
- 使用 Markdown 链接格式
- 链接指向 SKILL.md 文件

## Skill 加载机制

1. **本地 Skills**: 从 `~/.codex/skills/` 加载
2. **推荐 Skills**: 从 GitHub 仓库获取并缓存
3. **缓存策略**: 使用 `fetchedAt` 时间戳管理缓存

## 总结

Codex App 的 Skills 系统特点：
1. 基于文件系统的 Skill 管理
2. YAML 格式的代理配置
3. 支持本地和远程 Skills
4. 与 Automation 系统深度集成
