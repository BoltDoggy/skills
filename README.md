# Claude Code Skills

Claude Code 技能合集。

## 插件与技能

本项目包含两个独立插件：

### bolt-workflow — 开发工作流

从需求到交付的全流程规范。

| 技能 | 说明 |
|------|------|
| [spec-first](skills/spec-first/) | 需求澄清与原型确认：要求 agent 先澄清需求、确认原型方案，获得用户认可后再编码 |
| [doc-first](skills/doc-first/) | 文档优先原则：要求 agent 先写文档、后写代码，代码变更必须同步更新文档 |
| [done-check](skills/done-check/) | 开发完成验收：要求 agent 在宣告完成前执行系统性验收（需求回溯、测试、手动验证、diff 自审、回归排查、文档同步），按 CLI / Web / Server 区分验收方式 |

### bun-pnpm — 工具链规范

| 技能 | 说明 |
|------|------|
| [bun-pnpm](skills/bun-pnpm/) | Bun 项目使用 pnpm 管理依赖：依赖管理用 pnpm，运行时用 bun |

## 安装

### Claude Code

```bash
# 1. 添加商店（只需一次）
/plugin marketplace add BoltDoggy/skills

# 2. 安装插件
/plugin install bolt-workflow@bolt-skills   # 开发工作流
/plugin install bun-pnpm@bolt-skills        # Bun 工具链
```

也可直接从 Git 安装（不经过商店）：

```bash
/plugin install https://github.com/BoltDoggy/skills.git
```

### 其他 Agent（Kimi Code / Cursor / Codex 等）

```bash
# 安装全部 skills
npx skills add BoltDoggy/skills --all

# 安装指定 skill
npx skills add BoltDoggy/skills --skill spec-first --skill doc-first --skill done-check
npx skills add BoltDoggy/skills --skill bun-pnpm

# 查看可用的 skills
npx skills add BoltDoggy/skills --list
```

`npx skills add` 在交互式选择时按 `marketplace.json` 中的插件分组展示，安装时按 skill 粒度选择。
`marketplace.json` 中每个插件的 `skills` 路径相对于该插件的 `source` 目录；例如 `bun-pnpm` 的 `source` 是 `./skills/bun-pnpm`，所以对应 `skills` 写 `["./"]`。

## 添加新技能

在 `skills/` 下创建目录，包含 `SKILL.md` 即可：

```
skills/
└── your-skill/
    ├── SKILL.md           # 必需：Skill 定义
    └── references/        # 可选：参考文档
```

然后在所属插件的 `plugin.json` 和根目录 `.claude-plugin/marketplace.json` 中添加对应路径。注意 marketplace 中的 `skills` 路径相对于该插件的 `source` 目录。

## 许可证

MIT
