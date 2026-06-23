# Claude Code Skills

Claude Code 技能合集插件。

## 已收录技能

| 技能 | 说明 |
|------|------|
| [doc-first](skills/doc-first/) | 文档优先原则：要求 agent 先写文档、后写代码，代码变更必须同步更新文档 |
| [spec-first](skills/spec-first/) | 需求澄清与原型确认：要求 agent 先澄清需求、确认原型方案，获得用户认可后再编码 |
| [bun-pnpm](skills/bun-pnpm/) | Bun 项目使用 pnpm 管理依赖：依赖管理用 pnpm，运行时用 bun |

## 安装

### Claude Code

```bash
claude plugin install git@github.com:BoltDoggy/skills.git
```

### 其他 Agent（Kimi Code / Cursor / Codex 等）

```bash
npx skills add BoltDoggy/skills
```

支持所有兼容 `SKILL.md` 约定的 AI agent。

## 添加新技能

在 `skills/` 下创建目录，包含 `SKILL.md` 即可：

```
skills/
└── your-skill/
    ├── SKILL.md           # 必需：Skill 定义
    └── references/        # 可选：参考文档
```

然后在 `.claude-plugin/plugin.json` 的 `skills` 数组中添加路径。

## 许可证

MIT
