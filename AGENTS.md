# AGENTS.md

本文件为 AI 编码 agent 提供项目导航，帮助快速理解项目约定与结构。阅读本文件是参与本项目任何任务的第一步。

## 项目概述

本项目是一个 **Claude Code Skills（技能）合集仓库**，用于发布可被 Claude Code 及其他兼容 `SKILL.md` 约定的 AI agent（如 Kimi Code、Cursor、Codex）安装和使用的技能（Skills）。每个技能是一个自包含的目录，核心是 `SKILL.md` 文件，向 agent 注入特定领域知识、工作流或操作规范。

项目本身**不包含可执行代码、构建工具或依赖管理器**——它是一个纯配置与文档型仓库。"构建"产物即为目录结构本身，由 AI agent 在运行时直接加载。

### 插件划分

本项目发布**两个独立插件**，按领域分离：

| 插件 | 类别 | 包含技能 | 说明 |
|------|------|---------|------|
| `bolt-workflow` | workflow | spec-first、doc-first、done-check | 开发工作流：从需求澄清到完成验收的全流程规范 |
| `bun-pnpm` | tooling | bun-pnpm | Bun 项目工具链：依赖管理用 pnpm，运行时用 bun |

### 工作流技能间的协作关系

`bolt-workflow` 的三个技能按阶段串联：

```
需求输入
  │
  ▼
[spec-first] 澄清需求、确认方案 → .specs/<name>/spec.md + prototype.html
  │           （临时工作区，不提交 Git）
  │
  ▼  ← 用户明确确认
  │
[doc-first] 将确认方案沉淀到正式文档 → docs/
  │           （提交到 Git）
  │
  ▼
编码实现
  │
  ▼
[done-check] 系统性验收 → .reports/<task-name>/report.html
  │
  ▼
✅ 宣告完成
```

`bun-pnpm` 与上述流程正交，适用于 Bun 项目中的依赖管理场景。

## 目录结构

```
.
├── .claude-plugin/
│   ├── plugin.json          # 根插件清单：bolt-workflow（含 doc-first / spec-first / done-check）
│   └── marketplace.json     # 技能市场发布元数据（注册 bolt-workflow 和 bun-pnpm 两个插件）
├── skills/
│   ├── <skill-name>/
│   │   ├── SKILL.md         # 必需：技能定义（YAML front matter + Markdown 指令）
│   │   └── references/      # 可选：参考文档
│   └── bun-pnpm/
│       ├── .claude-plugin/
│       │   └── plugin.json  # bun-pnpm 独立插件清单
│       └── SKILL.md         # 技能定义
├── .gitignore               # 忽略 .specs/（临时工作区）
├── README.md                # 人类向的项目说明
└── AGENTS.md                # 本文件
```

### 当前已收录技能

| 技能 | 所属插件 | 路径 | 版本 | category | 说明 |
|------|---------|------|------|----------|------|
| `spec-first` | bolt-workflow | `skills/spec-first/SKILL.md` | 1.0.0 | workflow | 需求澄清与原型确认：要求 agent 先澄清需求、确认原型方案（输出到 `.specs/`），获得用户明确认可后再编码。界面类任务必须输出自包含 HTML 原型 |
| `doc-first` | bolt-workflow | `skills/doc-first/SKILL.md` | 1.0.0 | workflow | 文档优先原则：要求 agent 先写文档、后写代码，代码变更必须同步更新文档。定义了文档优先级、变更同步映射表、执行流程和检查清单 |
| `done-check` | bolt-workflow | `skills/done-check/SKILL.md` | 1.0.0 | workflow | 开发完成验收：要求 agent 在宣告任务完成前执行系统性验收（需求回溯、测试、构建、手动验证、diff 自审、回归排查、文档同步），按 CLI / Web / Server 区分验收方式，生成 `.reports/<task-name>/report.html` 验收报告 |
| `bun-pnpm` | bun-pnpm | `skills/bun-pnpm/SKILL.md` | 1.0.0 | tooling | Bun 项目使用 pnpm 管理依赖：依赖管理（install/add/remove/update）用 pnpm，运行时（脚本执行、测试、服务启动）用 bun。基于 pnpm@11+，配置统一写入 `pnpm-workspace.yaml` |

## 关键配置文件

### `.claude-plugin/plugin.json`（根）

根插件清单，定义 `bolt-workflow` 插件。核心字段：

- `name`：插件名称（`"bolt-workflow"`）
- `version`：插件版本（`1.0.0`）
- `description`：插件描述
- `author`：作者信息
- `keywords`：关键词数组（用于技能检索）
- `category`：分类（`workflow`）
- `skills`：**数组**，列出各技能目录的相对路径（`["./skills/doc-first", "./skills/spec-first", "./skills/done-check"]`）

### `skills/bun-pnpm/.claude-plugin/plugin.json`

`bun-pnpm` 是独立插件，有自己的清单。`skills` 指向 `["./"]`（SKILL.md 就在插件根目录）。

**添加新技能时，根据其领域归入对应插件的 `skills` 数组，或创建新的插件目录。**

### `.claude-plugin/marketplace.json`

技能市场发布元数据，遵循 schema `https://json.schemastore.org/claude-code-marketplace.json`。包含：

- `name`、`description`：市场名称与描述
- `owner`：所有者信息（name + email）
- `metadata`：版本元数据
- `plugins`：**数组**，每个插件条目含 `name`、`description`、`source`、`version`、`author`、`keywords`、`category`、`skills`。当前注册了两个插件：`bolt-workflow`（`source: "./"`，`skills` 相对仓库根目录）和 `bun-pnpm`（`source: "./skills/bun-pnpm"`，`skills: ["./"]` 相对该插件目录）
- `skills`：在 `marketplace.json` 的每个插件条目中，路径相对于该插件的 `source` 目录；例如 `source: "./skills/bun-pnpm"` 时，插件根目录下的 `SKILL.md` 应写作 `skills: ["./"]`，否则 `npx skills add` 会发现技能但无法归入对应分组

此文件面向 Claude Code marketplace 分发，非运行时加载逻辑。

### `skills/<name>/SKILL.md`

技能定义文件，由两部分组成：

1. **YAML front matter**：
   - `name`：技能名（与目录名一致，kebab-case）
   - `description`：多行描述，说明触发条件与职责。**这是 agent 判断何时使用该技能的关键依据**——措辞应包含激活场景和判断准则
2. **Markdown 正文**：面向 agent 的详细指令，通常包含原则声明、适用判断表、执行流程、场景规范、检查清单等结构化内容

### `.gitignore`

当前仅忽略 `.specs/`（spec-first 技能的需求与原型临时工作区）。

> **注意**：`done-check` 技能会在 `.reports/` 目录下生成验收报告，并声明该目录"不提交到 Git"，但 `.gitignore` 中**尚未添加 `.reports/`**。添加 done-check 相关产出物时，应同步在 `.gitignore` 中补充 `.reports/`。

## 添加新技能的流程

1. 判断技能属于哪个领域（工作流 / 工具链 / 其他），决定归入已有插件还是新建插件。
2. 在 `skills/` 下创建新目录，目录名即技能名（kebab-case）。
3. 创建 `SKILL.md`，包含 YAML front matter（`name`、`description`）与 Markdown 正文指令。
   - `description` 字段应明确说明：何时激活此技能、判断准则是什么
   - 正文建议包含：原则声明、适用场景表、执行流程、常见场景示范、检查清单
4.（可选）添加 `references/` 子目录存放参考文档。
5. 在所属插件的 `plugin.json` 的 `skills` 数组中添加该技能的相对路径。
6. 在 `.claude-plugin/marketplace.json` 的 `plugins` 数组中确认对应插件条目信息正确。
7. 更新 `README.md` 的技能表格。
8. 更新本文件（`AGENTS.md`）的"当前已收录技能"表格。

## 构建与测试

- **无构建步骤**：项目不含编译、打包流程，无 `package.json` / `pyproject.toml` / `Cargo.toml` 等构建配置。
- **无自动化测试**：项目没有测试套件。验证方式为人工确认：
  - `SKILL.md` 的 YAML front matter 格式正确（`---` 包裹，`name` 与 `description` 字段存在）
  - `plugin.json` 为合法 JSON 且 `skills` 数组中的路径指向真实存在的目录
  - `marketplace.json` 为合法 JSON 且 `plugins` 数组条目完整
  - 可用 `python3 -m json.tool <file>` 或 `jq . <file>` 对 JSON 文件做语法校验

```bash
# JSON 语法校验示例
python3 -m json.tool .claude-plugin/plugin.json
python3 -m json.tool .claude-plugin/marketplace.json
```

## 编码与写作约定

- **语言**：项目文档与技能内容以**中文**为主，技术术语保留英文原文（如 `SKILL.md`、front matter、kebab-case）。
- **文档优先原则**：本项目自身的 `doc-first` 技能即要求 agent 遵循"先文档后代码"的工作流。修改技能定义或配置后，应同步更新 `README.md` 与本文件（`AGENTS.md`）。
- **技能命名**：使用 kebab-case（连字符小写），如 `doc-first`、`bun-pnpm`。SKILL.md 的 `name` 字段应与目录名一致。
- **JSON 文件**：保持 2 空格缩进，与现有文件风格一致。
- **SKILL.md 正文风格**：大量使用 Markdown 表格（适用判断表、命令对照表、检查清单）、ASCII 流程图、代码块示例。保持结构化、可扫描的排版。
- **临时工作区命名**：`.specs/<requirement-name>/`（需求澄清产出）和 `.reports/<task-name>/`（验收报告）均使用 kebab-case 命名。

## 安全注意事项

- **密钥管理**：`done-check` 技能明确规定——agent **绝不要求用户在对话中提供密钥值**，绝不将密钥写入文件或命令，绝不经手密钥。密钥只在用户手中，由用户自行配置。
- **临时产出物不入库**：`.specs/` 和 `.reports/` 是 agent 与用户之间的临时工作区，不提交到版本控制（`.specs/` 已在 `.gitignore` 中忽略；`.reports/` 需补充）。
- **SKILL.md 中不含敏感信息**：技能定义面向公开发布，不应包含任何私有配置、密钥或内部地址。

## Git 约定

- 主分支：`main`
- Commit message 使用**中文描述** + **英文类型前缀**风格：
  - `init:` — 初始化提交（如 `init: agent skills 合集插件仓库`）
  - `feat:` — 新增技能（如 `feat: 新增 done-check 技能 — 开发完成验收`）
  - `docs:` — 文档更新（如 `docs: 补充 spec-first 与 doc-first 的衔接说明`）
- Commit message 中的技能/功能描述使用中文，类型前缀使用英文

## 许可证

MIT（见 `README.md` 声明）。
