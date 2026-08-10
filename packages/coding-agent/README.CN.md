<p align="center">
  <a href="https://pi.dev">
    <img alt="pi logo" src="https://pi.dev/logo-auto.svg" width="128">
  </a>
</p>
<p align="center">
  <a href="https://discord.com/invite/3cU7Bz4UPx"><img alt="Discord" src="https://img.shields.io/badge/discord-community-5865F2?style=flat-square&logo=discord&logoColor=white" /></a>
  <a href="https://www.npmjs.com/package/@earendil-works/pi-coding-agent"><img alt="npm" src="https://img.shields.io/npm/v/@earendil-works/pi-coding-agent?style=flat-square" /></a>
</p>

> 新贡献者提交的 issue 和 PR 默认会被自动关闭。维护者每天会审阅这些自动关闭的内容。详见 [CONTRIBUTING.md](../../CONTRIBUTING.md)。

> 本文为 [README.md](README.md) 的中文翻译，便于阅读；以英文原文为准。

---

Pi 是一个极简的终端编码代理框架（coding harness）。让 pi 适配你的工作流，而不是反过来——无需 fork 或改动 pi 内部实现。可通过 TypeScript [扩展（Extensions）](#扩展extensions)、[技能（Skills）](#技能skills)、[提示词模板（Prompt Templates）](#提示词模板prompt-templates) 和 [主题（Themes）](#主题themes) 进行扩展。把扩展、技能、提示词模板和主题放进 [Pi 包（Pi Packages）](#pi-包pi-packages)，再通过 npm 或 git 与他人共享。

Pi 自带强大的默认能力，但刻意不内置子代理（sub agents）、计划模式（plan mode）等功能。你可以让 pi 帮你实现想要的能力，或安装符合你工作流的第三方 pi 包。

Pi 支持四种运行模式：交互模式、打印/JSON 模式、用于进程集成的 RPC 模式，以及可嵌入自有应用的 SDK。

## 分享你的开源编码代理会话

如果你用 pi 做开源工作，欢迎分享你的编码代理会话。

公开的 OSS 会话数据有助于基于真实开发流程改进模型、提示词、工具和评测。

完整说明见 [这篇 X 帖子](https://x.com/badlogicgames/status/2037811643774652911)。

发布会话请使用 [`badlogic/pi-share-hf`](https://github.com/badlogic/pi-share-hf)，按其 README.md 完成配置。你只需要 Hugging Face 账号、Hugging Face CLI，以及 `pi-share-hf`。

也可以观看 [这个视频](https://x.com/badlogicgames/status/2041151967695634619)，演示如何发布我的 `pi-mono` 会话。

我也会定期把 `pi-mono` 工作会话发布到这里：

- [badlogicgames/pi-mono on Hugging Face](https://huggingface.co/datasets/badlogicgames/pi-mono)

## 目录

- [快速开始](#快速开始)
- [提供商与模型](#提供商与模型)
- [交互模式](#交互模式)
  - [编辑器](#编辑器)
  - [命令](#命令)
  - [快捷键](#快捷键)
  - [消息队列](#消息队列)
- [会话](#会话)
  - [分支](#分支)
  - [压缩](#压缩)
- [设置](#设置)
- [上下文文件](#上下文文件)
- [自定义](#自定义)
  - [提示词模板（Prompt Templates）](#提示词模板prompt-templates)
  - [技能（Skills）](#技能skills)
  - [扩展（Extensions）](#扩展extensions)
  - [主题（Themes）](#主题themes)
  - [Pi 包（Pi Packages）](#pi-包pi-packages)
- [编程方式使用](#编程方式使用)
- [设计理念](#设计理念)
- [CLI 参考](#cli-参考)

---

## 快速开始

```bash
npm install -g --ignore-scripts @earendil-works/pi-coding-agent
```

`--ignore-scripts` 会在安装时禁用依赖的生命周期脚本。普通 npm 安装不需要这些 install 脚本。

也可使用安装脚本：

```bash
curl -fsSL https://pi.dev/install.sh | sh
```

用 API key 认证：

```bash
export ANTHROPIC_API_KEY=sk-ant-...
pi
```

或使用已有订阅：

```bash
pi
/login  # 然后选择提供商
```

然后直接和 pi 对话即可。默认会给模型四个工具：`read`、`write`、`edit` 和 `bash`。模型用它们完成你的请求。可通过 [技能](#技能skills)、[提示词模板](#提示词模板prompt-templates)、[扩展](#扩展extensions) 或 [pi 包](#pi-包pi-packages) 增加能力。

**平台说明：** [Windows](docs/windows.md) | [Termux (Android)](docs/termux.md) | [tmux](docs/tmux.md) | [终端设置](docs/terminal-setup.md) | [Shell 别名](docs/shell-aliases.md)

---

## 提供商与模型

对每个内置提供商，pi 会维护一份支持工具调用的模型列表。已配置的提供商目录会自动刷新；运行 `pi update --models` 可立即强制刷新。通过订阅（`/login`）或 API key 认证后，用 `/model`（或 Ctrl+L）从该提供商选择任意模型。

**订阅：**
- Anthropic Claude Pro/Max
- OpenAI ChatGPT Plus/Pro (Codex)
- GitHub Copilot

**API keys：**
- Anthropic
- Ant Ling
- OpenAI
- Azure OpenAI
- DeepSeek
- NVIDIA NIM
- Google Gemini
- Google Vertex
- Amazon Bedrock
- Mistral
- Groq
- Cerebras
- Cloudflare AI Gateway
- Cloudflare Workers AI
- xAI
- OpenRouter
- Vercel AI Gateway
- ZAI Coding Plan (Global)
- ZAI Coding Plan (China)
- OpenCode Zen
- OpenCode Go
- Hugging Face
- Fireworks
- Together AI
- Baseten
- Kimi For Coding
- MiniMax
- Xiaomi MiMo
- Xiaomi MiMo Token Plan (China)
- Xiaomi MiMo Token Plan (Amsterdam)
- Xiaomi MiMo Token Plan (Singapore)

Pi 也支持 llama.cpp router 服务。用 `/login llama.cpp` 配置，用 `/llama` 管理下载与已加载模型，再用 `/model` 选择已加载模型。设置与用法见 [docs/llama-cpp.md](docs/llama-cpp.md)。

其他提供商配置见 [docs/providers.md](docs/providers.md)。

**自定义提供商与模型：** 若提供商兼容受支持的 API（OpenAI、Anthropic、Google），可通过 `~/.pi/agent/models.json` 添加。自定义 API 或 OAuth 请用扩展。见 [docs/models.md](docs/models.md) 与 [docs/custom-provider.md](docs/custom-provider.md)。

---

## 交互模式

<p align="center"><img src="docs/images/interactive-mode.png" alt="Interactive Mode" width="600"></p>

界面从上到下：

- **启动头（Startup header）** - 显示快捷键（`/hotkeys` 查看全部）、已加载的 AGENTS.md、提示词模板、技能和扩展
- **消息区（Messages）** - 你的消息、助手回复、工具调用与结果、通知、错误，以及扩展 UI
- **编辑器（Editor）** - 输入区域；边框颜色表示 thinking 级别
- **页脚（Footer）** - 工作目录、会话名、总 token/缓存用量（`↑` 输入、`↓` 输出、`R` 缓存读、`W` 缓存写、`CH` 最近缓存命中率）、费用、上下文用量、当前模型。总量包含助手回复、工具上报用量，以及摘要生成。

编辑器可被其他 UI 临时替换，例如内置 `/settings`，或扩展提供的自定义 UI（如让用户以结构化方式回答模型问题的 Q&A 工具）。[扩展](#扩展extensions) 也可替换编辑器、在其上方/下方添加组件、状态栏、自定义页脚或浮层。

### 编辑器

| 功能 | 用法 |
|---------|-----|
| 文件引用 | 输入 `@` 对项目文件做模糊搜索 |
| 路径补全 | Tab 补全路径 |
| 多行输入 | Shift+Enter（Windows Terminal 上为 Ctrl+Enter） |
| 外部编辑器 | Ctrl+G 打开 `externalEditor`、`$VISUAL`、`$EDITOR`；Windows 默认 Notepad，其他平台默认 `nano` |
| 剪贴板 | Ctrl+V 粘贴图片或文本（Windows 上为 Alt+V），或把图片拖到终端 |
| Bash 命令 | `!command` 执行并把输出发给 LLM，`!!command` 执行但不发送 |

删除单词、撤销等标准编辑快捷键见 [docs/keybindings.md](docs/keybindings.md)。

### 命令

在编辑器中输入 `/` 触发命令。[扩展](#扩展extensions) 可注册自定义命令，[技能](#技能skills) 以 `/skill:name` 提供，[提示词模板](#提示词模板prompt-templates) 通过 `/templatename` 展开。

| 命令 | 说明 |
|---------|-------------|
| `/login`、`/logout` | 管理提供商凭据 |
| [`/llama`](docs/llama-cpp.md) | 下载、加载、卸载 llama.cpp router 模型 |
| `/model` | 切换模型 |
| `/scoped-models` | 启用/禁用用于 Ctrl+P 循环切换的模型 |
| `/settings` | Thinking 级别、主题、消息投递、传输方式 |
| `/resume` | 从历史会话中选择 |
| `/new` | 开始新会话 |
| `/name <name>` | 设置会话显示名 |
| `/session` | 显示会话信息（文件、ID、消息、token、费用） |
| `/tree` | 跳到会话中任意节点并从那里继续 |
| `/trust` | 保存项目信任决策供后续会话使用（需重启） |
| `/fork` | 从之前的某条用户消息创建新会话 |
| `/clone` | 将当前活动分支复制为新会话 |
| `/compact [prompt]` | 手动压缩上下文，可附带自定义指令 |
| `/copy` | 复制最后一条助手消息到剪贴板 |
| `/export [file]` | 将会话导出为 HTML 或 JSONL |
| `/import <file>` | 从 JSONL 导入并恢复会话 |
| `/share` | 上传为私有 GitHub gist，并生成可分享的 HTML 链接 |
| `/reload` | 重新加载快捷键、扩展、技能、提示词、主题和上下文文件 |
| `/hotkeys` | 显示全部键盘快捷键 |
| `/changelog` | 显示版本历史 |
| `/quit` | 退出 pi |

### 快捷键

完整列表见 `/hotkeys`。可通过 `~/.pi/agent/keybindings.json` 自定义。见 [docs/keybindings.md](docs/keybindings.md)。

**常用：**

| 按键 | 动作 |
|-----|--------|
| Ctrl+C | 清空编辑器 |
| Ctrl+C 两次 | 退出 |
| Escape | 取消/中止 |
| Escape 两次 | 打开 `/tree` |
| Ctrl+L | 打开模型选择器 |
| Ctrl+P / Shift+Ctrl+P | 向前/向后循环 scoped 模型 |
| Shift+Tab | 循环 thinking 级别 |
| Ctrl+O | 折叠/展开工具输出 |
| Ctrl+T | 折叠/展开 thinking 块 |
| Ctrl+X | 复制最后一条助手消息 |

### 消息队列

代理工作时可提交消息：

- **Enter** 排队一条 *steering*（引导）消息，在当前助手回合执行完工具调用后投递
- **Alt+Enter** 排队一条 *follow-up*（跟进）消息，仅在代理完成全部工作后投递
- **Escape** 中止并把已排队消息恢复到编辑器
- **Alt+Up** 把排队消息取回编辑器

在 Windows Terminal 上，`Alt+Enter` 默认是全屏。请按 [docs/terminal-setup.md](docs/terminal-setup.md) 重新映射，以便 pi 能收到 follow-up 快捷键。

投递方式可在 [设置](docs/settings.md) 中配置：`steeringMode` 与 `followUpMode` 可为 `"one-at-a-time"`（默认，等待响应）或 `"all"`（一次投递全部排队消息）。`transport` 用于选择支持多传输方式的提供商偏好（`"sse"`、`"websocket"` 或 `"auto"`）。

---

## 会话

会话以带树结构的 JSONL 文件存储。每条记录有 `id` 与 `parentId`，可在同一文件内做原地分支，无需新建文件。文件格式见 [docs/session-format.md](docs/session-format.md)。

### 管理

会话自动保存到 `~/.pi/agent/sessions/`，按工作目录组织。

```bash
pi -c                  # 继续最近会话
pi -r                  # 浏览并选择历史会话
pi --no-session        # 临时模式（不保存）
pi --name "my task"    # 启动时设置会话显示名
pi --session <path|id> # 使用指定会话文件或 ID
pi --fork <path|id>    # 将指定会话文件或 ID fork 为新会话
```

在交互模式用 `/session` 查看当前会话 ID，再配合 `--session <id>` 或 `--fork <id>` 复用。

### 分支

**`/tree`** - 在会话树中原地导航。选择任意历史节点并从那里继续，可在分支间切换。全部历史保留在单个文件中。

<p align="center"><img src="docs/images/tree-view.png" alt="Tree View" width="600"></p>

- 输入即可搜索；用 Ctrl+←/Ctrl+→ 或 Alt+←/Alt+→ 折叠/展开并在分支间跳转；用 ←/→ 翻页
- 过滤模式（Ctrl+O）：default → no-tools → user-only → labeled-only → all
- 按 Ctrl+X 复制选中消息
- 按 Shift+L 给条目打书签标签，按 Shift+T 切换标签时间戳显示

**`/fork`** - 从活动分支上之前的某条用户消息创建新会话文件。打开选择器，复制到该点为止的活动路径，并把所选提示放入编辑器以便修改。

**`/clone`** - 将当前活动分支复制为新会话文件（停留在当前位置）。新会话保留完整活动路径历史，并以空编辑器打开。

**`--fork <path|id>`** - 从 CLI 直接 fork 已有会话文件或部分会话 UUID。会把完整源会话复制到当前项目的新会话文件中。

### 压缩

长会话可能耗尽上下文窗口。压缩会总结较旧消息，同时保留近期内容。

**手动：** `/compact` 或 `/compact <自定义指令>`

**自动：** 默认启用。在上下文溢出时触发（恢复并重试），或在接近上限时主动触发。可通过 `/settings` 或 `settings.json` 配置。

压缩会丢失部分细节。完整历史仍在 JSONL 文件中；用 `/tree` 可回看。可通过 [扩展](#扩展extensions) 自定义压缩行为。内部机制见 [docs/compaction.md](docs/compaction.md)。

---

## 设置

用 `/settings` 修改常用选项，或直接编辑 JSON：

| 位置 | 作用域 |
|----------|-------|
| `~/.pi/agent/settings.json` | 全局（所有项目） |
| `.pi/settings.json` | 项目（覆盖全局） |

全部选项见 [docs/settings.md](docs/settings.md)。

### 项目信任

交互模式启动时，若项目目录包含项目级设置、资源或项目 `.agents/skills`，且 `~/.pi/agent/trust.json` 中对该目录或其父目录尚无已保存决策，pi 会先询问是否信任。信任后，pi 可加载 `.pi/settings.json` 与 `.pi` 资源、安装缺失的项目包，并执行项目扩展。

在信任决策之前，pi 仅加载上下文文件、用户/全局扩展，以及 CLI `-e` 扩展，以便它们处理 `project_trust` 事件。项目本地扩展、项目包管理的扩展和项目设置仅在信任后加载。切换到另一 cwd 且当前进程尚未解决其信任时，同样适用此拆分。

非交互模式（`-p`、`--mode json`、`--mode rpc`）不会显示信任提示。若无适用的已保存信任决策，则使用全局设置中的 `defaultProjectTrust`：`ask`（默认）和 `never` 会忽略这些项目资源，而 `always` 会信任它们。可用 `--approve`/`-a` 或 `--no-approve`/`-na` 覆盖单次运行的项目信任。

若无扩展或已保存决策生效，则由 `defaultProjectTrust` 控制回退行为。可在 `~/.pi/agent/settings.json` 中设为 `"ask"`、`"always"` 或 `"never"`，或用 `/settings` 修改。

`pi config` 与包相关命令使用相同的项目信任流程，但 `pi update` 从不提示。传入 `--approve` 可在单次命令中信任项目本地设置，`--no-approve` 则忽略它们。

在交互模式用 `/trust` 可保存项目信任决策供后续会话使用，包括对直接父目录的信任。它只写入 `~/.pi/agent/trust.json`；当前会话不会重载，需重启 pi 后生效。

### 遥测与更新检查

Pi 有两个独立的启动特性：

- **更新检查：** 请求 `https://pi.dev/api/latest-version` 检查是否有更新版本。用 `PI_SKIP_VERSION_CHECK=1` 禁用。禁用更新检查只关闭此项。
- **安装/更新遥测：** 首次安装或检测到 changelog 更新后，向 `https://pi.dev/api/report-install` 发送匿名版本 ping。该设置也控制 OpenRouter、Cloudflare 以及直连 NVIDIA NIM 请求的可选提供商标识头。在 `settings.json` 中将 `enableInstallTelemetry` 设为 `false`，或设置 `PI_TELEMETRY=0` 可退出。这不会禁用更新检查；除非禁用更新检查或启用离线模式，Pi 仍可能联系 `pi.dev` 获取最新版本。

使用 `--offline` 或 `PI_OFFLINE=1` 可禁用此处描述的全部启动网络操作，包括更新检查、包更新检查，以及安装/更新遥测。

---

## 上下文文件

Pi 启动时会从以下位置加载 `AGENTS.md`（或 `CLAUDE.md`）：
- `~/.pi/agent/AGENTS.md`（全局）
- 父目录（从 cwd 向上遍历）
- 当前目录

用于项目说明（`AGENTS.md`/`CLAUDE.md`）、约定、常用命令等。所有匹配文件会拼接在一起。

用 `--no-context-files`（或 `-nc`）可禁用上下文文件加载。

### 系统提示词

用 `.pi/SYSTEM.md`（项目）或 `~/.pi/agent/SYSTEM.md`（全局）替换默认系统提示词。用 `APPEND_SYSTEM.md` 可追加而不替换。

---

## 自定义

### 提示词模板（Prompt Templates）

可复用的 Markdown 提示词。输入 `/name` 展开。

```markdown
<!-- ~/.pi/agent/prompts/review.md -->
Review this code for bugs, security issues, and performance problems.
Focus on: {{focus}}
```

放在 `~/.pi/agent/prompts/`、`.pi/prompts/`，或 [pi 包](#pi-包pi-packages) 中以便共享。见 [docs/prompt-templates.md](docs/prompt-templates.md)。

### 技能（Skills）

按需加载的能力包，遵循 [Agent Skills 标准](https://agentskills.io)。通过 `/skill:name` 调用，或让代理自动加载。

```markdown
<!-- ~/.pi/agent/skills/my-skill/SKILL.md -->
# My Skill
Use this skill when the user asks about X.

## Steps
1. Do this
2. Then that
```

放在 `~/.pi/agent/skills/`、`~/.agents/skills/`、`.pi/skills/` 或 `.agents/skills/`（从 `cwd` 向上到父目录），或 [pi 包](#pi-包pi-packages) 中以便共享。见 [docs/skills.md](docs/skills.md)。

### 扩展（Extensions）

<p align="center"><img src="docs/images/doom-extension.png" alt="Doom Extension" width="600"></p>

TypeScript 模块，可用自定义工具、命令、快捷键、事件处理器和 UI 组件扩展 pi。

```typescript
export default function (pi: ExtensionAPI) {
  pi.registerTool({ name: "deploy", ... });
  pi.registerCommand("stats", { ... });
  pi.on("tool_call", async (event, ctx) => { ... });
}
```

默认导出也可以是 `async`。pi 会在启动继续前等待异步扩展工厂完成，适合一次性初始化，例如在调用 `pi.registerProvider()` 前拉取远程模型列表。

**能做什么：**
- 自定义工具（或完全替换内置工具）
- 子代理与计划模式
- 自定义压缩与摘要
- 权限门控与路径保护
- 自定义编辑器与 UI 组件
- 状态栏、页眉、页脚
- Git 检查点与自动提交
- SSH 与沙箱执行
- MCP 服务集成
- 让 pi 看起来像 Claude Code
- 等待时玩游戏（是的，能跑 Doom）
- ……任何你能想到的事

放在 `~/.pi/agent/extensions/`、`.pi/extensions/`，或 [pi 包](#pi-包pi-packages) 中以便共享。见 [docs/extensions.md](docs/extensions.md) 与 [examples/extensions/](examples/extensions/)。

### 主题（Themes）

内置：`dark`、`light`。主题支持热重载：修改当前活动主题文件后，pi 会立即应用更改。

放在 `~/.pi/agent/themes/`、`.pi/themes/`，或 [pi 包](#pi-包pi-packages) 中以便共享。见 [docs/themes.md](docs/themes.md)。

### Pi 包（Pi Packages）

通过 npm 或 git 打包并共享扩展、技能、提示词和主题。可在 [npmjs.com](https://www.npmjs.com/search?q=keywords%3Api-package) 或 [Discord](https://discord.com/channels/1456806362351669492/1457744485428629628) 查找包。

> **安全：** Pi 包拥有完整系统访问权限。扩展可执行任意代码，技能可指示模型执行任何操作（包括运行可执行文件）。安装第三方包前请审查源码。

```bash
pi install npm:@foo/pi-tools
pi install npm:@foo/pi-tools@1.2.3      # 固定版本
pi install git:github.com/user/repo
pi install git:github.com/user/repo@v1  # tag 或 commit
pi install git:git@github.com:user/repo
pi install git:git@github.com:user/repo@v1  # tag 或 commit
pi install https://github.com/user/repo
pi install https://github.com/user/repo@v1      # tag 或 commit
pi install ssh://git@github.com/user/repo
pi install ssh://git@github.com/user/repo@v1    # tag 或 commit
pi remove npm:@foo/pi-tools
pi uninstall npm:@foo/pi-tools          # remove 的别名
pi list
pi update                               # 仅更新 pi
pi update --all                         # 更新 pi 与包
pi update --extensions                  # 仅更新包
pi update --models                      # 仅刷新模型目录
pi update --self                        # 仅更新 pi
pi update --self --force                # 即使已是当前版本也重新安装 pi
pi update npm:@foo/pi-tools             # 更新单个包
pi config                               # 启用/禁用扩展、技能、提示词、主题
```

包安装到 `~/.pi/agent/git/`（git）或 `~/.pi/agent/npm/`（npm）。用 `-l` 做项目本地安装（`.pi/git/`、`.pi/npm/`）。Git 的 `@ref` 为固定的 tag 或 commit；固定版本的包会被 `pi update --extensions` 与 `pi update --all` 跳过，可用 `pi install git:host/user/repo@new-ref` 把已有包移到新 ref。Git 包默认用 `npm install --omit=dev` 安装依赖，因此运行时依赖须写在 `dependencies`；若配置了 `npmCommand`，git 包为兼容包装器会使用普通 `install`。若使用 Node 版本管理器并希望包安装复用稳定的 npm 上下文，可在 `settings.json` 中设置 `npmCommand`，例如 `["mise", "exec", "node@20", "--", "npm"]`。

在 `package.json` 中添加 `pi` 键即可创建包：

```json
{
  "name": "my-pi-package",
  "keywords": ["pi-package"],
  "pi": {
    "extensions": ["./extensions"],
    "skills": ["./skills"],
    "prompts": ["./prompts"],
    "themes": ["./themes"]
  }
}
```

若无 `pi` 清单，pi 会从约定目录（`extensions/`、`skills/`、`prompts/`、`themes/`）自动发现。

见 [docs/packages.md](docs/packages.md)。

---

## 编程方式使用

### SDK

```typescript
import { createAgentSession, ModelRuntime, SessionManager } from "@earendil-works/pi-coding-agent";

const modelRuntime = await ModelRuntime.create();
const { session } = await createAgentSession({
  sessionManager: SessionManager.inMemory(),
  modelRuntime,
});

await session.prompt("What files are in the current directory?");
```

高级多会话运行时替换请使用 `createAgentSessionRuntime()` 与 `AgentSessionRuntime`。

见 [docs/sdk.md](docs/sdk.md) 与 [examples/sdk/](examples/sdk/)。

### RPC 模式

非 Node.js 集成可通过 stdin/stdout 使用 RPC 模式：

```bash
pi --mode rpc
```

RPC 模式使用严格的 LF 分隔 JSONL 帧。客户端必须仅按 `\n` 拆分记录。不要用通用行读取器（如 Node `readline`），它们还会按 Unicode 分隔符拆分，可能破坏 JSON 载荷。

协议见 [docs/rpc.md](docs/rpc.md)。

---

## 设计理念

Pi 刻意做成高度可扩展，这样就不需要替你规定工作流。其他工具内置的功能，可以用 [扩展](#扩展extensions)、[技能](#技能skills) 自己实现，或从第三方 [pi 包](#pi-包pi-packages) 安装。这样核心保持精简，同时你能按自己的工作方式塑造 pi。

**没有 MCP。** 用带 README 的 CLI 工具（见 [技能](#技能skills)），或写扩展加 MCP 支持。[为什么？](https://mariozechner.at/posts/2025-11-02-what-if-you-dont-need-mcp/)

**没有子代理。** 做法很多：用 tmux 拉起多个 pi 实例，用 [扩展](#扩展extensions) 自己做，或安装符合你方式的包。

**没有权限弹窗。** 在容器里跑，或用 [扩展](#扩展extensions) 按你的环境与安全要求做确认流程。

**没有计划模式。** 把计划写到文件，或用 [扩展](#扩展extensions) 实现，或安装包。

**没有内置 to-do。** 它们会干扰模型。用 TODO.md，或用 [扩展](#扩展extensions) 自己做。

**没有后台 bash。** 用 tmux。可观测性完整，可直接交互。

完整理由见 [这篇博客](https://mariozechner.at/posts/2025-11-30-pi-coding-agent/)。

---

## CLI 参考

```bash
pi [options] [@files...] [messages...]
```

### 包命令

```bash
pi install <source> [-l]     # 安装包，-l 为项目本地
pi remove <source> [-l]      # 移除包
pi uninstall <source> [-l]   # remove 的别名
pi update [source|self|pi]   # 仅更新 pi，或更新某个包源
pi update --all              # 更新 pi 与包
pi update --extensions       # 仅更新包
pi update --models           # 仅刷新模型目录
pi update --self             # 仅更新 pi
pi update --self --force     # 即使已是当前版本也重新安装 pi
pi update --extension <src>  # 更新单个包
pi list                      # 列出已安装包
pi config                    # 启用/禁用包资源
```

`pi config` 与项目包命令接受 `--approve`/`--no-approve`，用于在单次命令中信任或忽略项目本地设置。`pi update` 从不提示项目信任。

### 模式

| 标志 | 说明 |
|------|-------------|
| （默认） | 交互模式 |
| `-p`、`--print` | 打印响应后退出 |
| `--mode json` | 以 JSON 行输出全部事件（见 [docs/json.md](docs/json.md)） |
| `--mode rpc` | 用于进程集成的 RPC 模式（见 [docs/rpc.md](docs/rpc.md)） |
| `--export <in> [out]` | 将会话导出为 HTML |

在 print 模式下，pi 也会读取管道 stdin，并合并进初始提示：

```bash
cat README.md | pi -p "Summarize this text"
```

### 模型选项

| 选项 | 说明 |
|--------|-------------|
| `--provider <name>` | 提供商（anthropic、openai、google 等） |
| `--model <pattern>` | 模型模式或 ID（支持 `provider/id`，以及可选的 `:<thinking>`） |
| `--api-key <key>` | API key（覆盖环境变量） |
| `--thinking <level>` | `off`、`minimal`、`low`、`medium`、`high`、`xhigh`、`max` |
| `--models <patterns>` | 逗号分隔模式，用于 Ctrl+P 循环 |
| `--list-models [search]` | 列出可用模型 |

### 会话选项

| 选项 | 说明 |
|--------|-------------|
| `-c`、`--continue` | 继续最近会话 |
| `-r`、`--resume` | 浏览并选择会话 |
| `--session <path\|id>` | 使用指定会话文件或部分 UUID |
| `--fork <path\|id>` | 将指定会话文件或部分 UUID fork 为新会话 |
| `--session-dir <dir>` | 自定义会话存储目录 |
| `--no-session` | 临时模式（不保存） |
| `--name <name>`、`-n <name>` | 启动时设置会话显示名 |

### 工具选项

| 选项 | 说明 |
|--------|-------------|
| `--tools <list>`、`-t <list>` | 仅允许指定工具名（内置、扩展与自定义工具） |
| `--exclude-tools <list>`、`-xt <list>` | 禁用指定工具名（内置、扩展与自定义工具） |
| `--no-builtin-tools`、`-nbt` | 默认禁用内置工具，但保留扩展/自定义工具 |
| `--no-tools`、`-nt` | 默认禁用全部工具 |

可用内置工具：`read`、`bash`、`edit`、`write`、`grep`、`find`、`ls`

### 资源选项

| 选项 | 说明 |
|--------|-------------|
| `-e`、`--extension <source>` | 从路径、npm 或 git 加载扩展（可重复） |
| `--no-extensions` | 禁用扩展发现 |
| `--skill <path>` | 加载技能（可重复） |
| `--no-skills` | 禁用技能发现 |
| `--prompt-template <path>` | 加载提示词模板（可重复） |
| `--no-prompt-templates` | 禁用提示词模板发现 |
| `--theme <path>` | 加载主题（可重复） |
| `--no-themes` | 禁用主题发现 |
| `--no-context-files`、`-nc` | 禁用 AGENTS.md 与 CLAUDE.md 上下文文件发现 |

可将 `--no-*` 与显式标志组合，精确加载所需内容并忽略 settings.json（例如 `--no-extensions -e ./my-ext.ts`）。

### 其他选项

| 选项 | 说明 |
|--------|-------------|
| `--system-prompt <text>` | 替换默认提示词（上下文文件与技能仍会追加） |
| `--append-system-prompt <text>` | 追加到系统提示词 |
| `--ui-mode <mode>` | UI 模式：`regular`（默认）或实验性 `fullscreen` |
| `--verbose` | 强制详细启动输出 |
| `-a`、`--approve` | 本次运行信任项目本地文件 |
| `-na`、`--no-approve` | 本次运行忽略项目本地文件 |
| `-h`、`--help` | 显示帮助 |
| `-v`、`--version` | 显示版本 |

### 文件参数

用 `@` 前缀把文件纳入消息：

```bash
pi @prompt.md "Answer this"
pi -p @screenshot.png "What's in this image?"
pi @code.ts @test.ts "Review these files"
```

### 示例

```bash
# 交互模式并带初始提示
pi "List all .ts files in src/"

# 非交互
pi -p "Summarize this codebase"

# 非交互 + 管道 stdin
cat README.md | pi -p "Summarize this text"

# 命名的一次性会话
pi --name "release audit" -p "Audit this repository"

# 换模型
pi --provider openai --model gpt-4o "Help me refactor"

# 带提供商前缀的模型（无需 --provider）
pi --model openai/gpt-4o "Help me refactor"

# thinking 级别简写
pi --model sonnet:high "Solve this complex problem"

# 限制模型循环范围
pi --models "claude-*,gpt-4o"

# 只读模式
pi --tools read,grep,find,ls -p "Review the code"

# 禁用某个扩展或内置工具，其余仍可用
pi --exclude-tools ask_question

# 高 thinking 级别
pi --thinking high "Solve this complex problem"
```

### 环境变量

| 变量 | 说明 |
|----------|-------------|
| `AI_AGENT` | CLI 与 RPC 入口会设为 `pi`，便于通用工具把子进程归因到 Pi |
| `PI_CODING_AGENT` | CLI 与 RPC 入口会设为 `true`，便于子进程检测自己运行在 Pi 内 |
| `PI_CODING_AGENT_DIR` | 覆盖配置目录（默认：`~/.pi/agent`） |
| `PI_CODING_AGENT_SESSION_DIR` | 覆盖会话存储目录（会被 `--session-dir` 覆盖） |
| `PI_PACKAGE_DIR` | 覆盖包目录（对 Nix/Guix 等 store 路径分词不佳的场景有用） |
| `PI_OFFLINE` | 禁用启动网络操作，包括更新检查、包更新检查，以及安装/更新遥测 |
| `PI_SKIP_VERSION_CHECK` | 跳过启动时的 Pi 版本更新检查。会阻止对 `pi.dev` 的最新版本请求 |
| `PI_TELEMETRY` | 覆盖安装/更新遥测与提供商标识头。用 `1`/`true`/`yes` 启用，或 `0`/`false`/`no` 禁用。不会禁用更新检查 |
| `PI_CACHE_RETENTION` | 设为 `long` 启用延长提示缓存（Anthropic：1h，OpenAI：24h） |
| `VISUAL`、`EDITOR` | 未设置 `externalEditor` 时，Ctrl+G 的后备外部编辑器；Windows 默认 Notepad，其他平台默认 `nano` |

LLM 可调用的 bash 工具所运行的命令还会收到当前会话元数据：

| 变量 | 说明 |
|----------|-------------|
| `PI_SESSION_ID` | 当前会话 ID |
| `PI_SESSION_FILE` | 会话 JSONL 绝对路径；临时会话未设置 |
| `PI_PROVIDER` | 当前所选模型提供商 |
| `PI_MODEL` | 当前所选模型 ID |
| `PI_REASONING_LEVEL` | 当前有效 reasoning 级别 |

这些值在每条命令启动时解析。语义、示例与自定义工具退出方式见 [Environment Variables](docs/environment-variables.md#bash-tool-session-environment)。

---

## 贡献与开发

指南见 [CONTRIBUTING.md](../../CONTRIBUTING.md)；环境搭建、fork 与调试见 [docs/development.md](docs/development.md)。

## 许可证

MIT

## 另见

- [@earendil-works/pi-ai](https://www.npmjs.com/package/@earendil-works/pi-ai)：核心 LLM 工具包
- [@earendil-works/pi-agent-core](https://www.npmjs.com/package/@earendil-works/pi-agent-core)：Agent 框架
- [@earendil-works/pi-tui](https://www.npmjs.com/package/@earendil-works/pi-tui)：终端 UI 组件

<p align="center">
  <a href="https://pi.dev">pi.dev</a> domain graciously donated by
  <br /><br />
  <a href="https://exe.dev"><img src="docs/images/exy.png" alt="Exy mascot" width="48" /><br />exe.dev</a>
</p>
