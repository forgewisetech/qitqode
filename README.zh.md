<h1 align="center">QitQode</h1>

<p align="center"><strong>拥有记忆的终端 AI 编程智能体。</strong></p>

<p align="center">
  <a href="https://qitqode.com">官网</a>
</p>

<p align="center">
  <a href="./README.md">English</a> | <strong>简体中文</strong> | <a href="./README.zht.md">繁體中文</a> | <a href="./README.ja.md">日本語</a> | <a href="./README.fr.md">Français</a> | <a href="./README.ru.md">Русский</a> | <a href="./README.es.md">Español</a> | <a href="./README.pt.md">Português</a>
</p>

---

大多数编程智能体在会话结束的那一刻就把一切都忘光了。QitQode 不会。它能读写代码、运行命令、管理 Git——并跨会话保留一份持久、可检索的项目记忆；当会话运行过长时，它会重建自己的上下文，从而继续工作，而不是从头再来。

一个账户，八项能力——**Free (no cost)**、**Adaptive**、**Fast**、**Economy**、**Planner**、**Repair**、**Max intelligence**。**Orchestrated** 流水线是 Qortex 单独提供的一项能力，用于 gate → plan → build → repair 的多阶段流程，并非普通的交互式模型选择。无需在各家服务商的控制台之间切换，无需管理一堆 API 密钥，也无需为逐个模型的计费做表格。

---

## 快速开始

```bash
npm install -g @qitqode/cli
# or: bun add --global @qitqode/cli

# Run
qitqode
```

首次启动会引导你完成登录：

- **使用 QitQode 登录**——一套设备码流程，在任何环境下都能用，包括 SSH 会话和远程沙箱：CLI 会显示一个验证 URL 和验证码（在有浏览器可用时会自动打开），你在任意设备上批准即可完成
- **API 密钥**——也可以直接粘贴一个 QitQode API 密钥

然后在模型选择器中挑选一个档位，就可以开始工作了。整个配置过程就这么简单。

### 使用简体中文运行 QitQode

TUI 会自动检测你的系统语言环境。若要手动切换，请在 QitQode 中运行 `/language`（或 `/lang`），然后从列表中选择简体中文。

<details>
<summary><strong>WSL：剪贴板问题</strong></summary>

如果你在 WSL 上复制时遇到乱码，请安装 `xsel`：

```bash
sudo apt install xsel
```

</details>

---

## 为什么选择 QitQode

你不需要又一个聊天套壳工具。你需要的是一个能扛住长时间任务、不会中途跑偏的智能体。QitQode 围绕四个机制构建：

### 1. 能挺过会话结束的记忆

每个项目都拥有一个由 SQLite 全文检索支撑的持久记忆层：`MEMORY.md` 中的项目知识、自动的会话检查点、临时便签，以及按任务记录的进度日志。当你恢复工作时，相关记忆会被自动注入——经过排序和 token 预算控制，而不是一股脑倒出来。智能体会从上次停下的地方继续，而不是重新学习你的代码库。

### 2. 能自我重建的上下文

长任务会轻易超出上下文窗口。QitQode 会盯住这个窗口，在它被填满之前保存检查点，并从最新的检查点、项目记忆和任务进度中重建工作上下文——这样一次长达数小时的重构就不会在 token 上限处崩掉。

### 3. 可以问责的自主性

用 `/goal` 设定一个停止条件。当智能体认为自己完成时，一个独立的裁判模型会审阅整段对话，判断目标是否真正达成——不再有任务刚做到一半就乐观地宣布"全部完成！"的情况。搭配树状任务追踪器（`T1`、`T1.1`……）和并行子智能体，就能实现真正的无人值守工作。

### 4. 一份订阅，零服务商管道搭建

七项交互式能力，一次登录。用 `/free`、`/fast`、`/economical`、`/adaptive`、`/planner`、`/repair` 或 `/max-int` 在会话中途切换能力。Orchestrated 是独立的 Qortex 多阶段流水线，不是普通的交互式档位。随时通过内置的 qredits 显示查看你的余额——用量一目了然，不会到月底才让你大吃一惊。

### 还有别人省略掉的那些部分

- **凭据静态加密**——你的认证令牌用一个保存在操作系统钥匙串中的密钥封存，并且默认会从智能体启动的每个子进程中剥离凭据相关的环境变量。
- **人人可用的 TUI**——屏幕阅读器无障碍模式、`NO_COLOR` 支持、减少动效、播报详细程度控制，以及符合 WCAG AA 的高对比度主题。都是一等公民，而不是事后拼上去的。（详见下文。）
- **纯粹的 MIT 许可证**——没有单独的使用限制文件，也没有藏在 README 末尾的服务条款。
- **为真实机器打造的设备码登录**——可在 SSH、容器以及远程沙箱中使用，这些场景下回环浏览器重定向根本无法工作。

---

## 核心功能

### 多种智能体

| 智能体      | 说明                                                                 |
| ----------- | -------------------------------------------------------------------- |
| **build**   | 默认。拥有完整工具权限，用于开发                                     |
| **plan**    | 只读分析模式，用于代码探索与方案设计                                 |
| **compose** | 编排模式，用于规范驱动开发和技能驱动的工作流                         |

按 `Alt+M` 在主智能体之间循环切换。子智能体由系统按需创建。

### 持久记忆

由 SQLite FTS5 全文检索驱动的跨会话记忆：

- **项目记忆**（`MEMORY.md`）——持久的项目知识、规则和架构决策
- **会话检查点**（`checkpoint.md`）——由 checkpoint-writer 子智能体自动维护的结构化状态快照
- **临时便签**（`notes.md`）——供智能体使用的临时记录区
- **任务进度**（`tasks/<id>/progress.md`）——按任务记录的日志

会话恢复时记忆会被自动注入，因此智能体无需重新学习项目上下文。

### 智能上下文管理

- **自动检查点**——根据模型上下文窗口决定何时保存会话状态
- **上下文重建**——当上下文接近上限时，从最新的检查点、项目记忆、任务进度和保留的近期消息中重建上下文，让智能体能继续当前任务
- **预算化注入**——用 token 预算控制有多少检查点、记忆和便签内容进入上下文，并结合重要性排序

### 任务追踪

一套树状任务系统（`T1`、`T1.1`、`T1.2`……），会自动与检查点系统集成，因此会话恢复时任务进度得以保留。

### 子智能体系统

主智能体可以按需创建子智能体。子智能体共享当前会话上下文，可以并行工作，并具备生命周期追踪、取消和后台执行能力。

### 目标 / 停止条件

`/goal` 命令为一个会话设定停止条件。当智能体试图停止时，一个独立的裁判模型会评估对话，判断该条件是否真正满足——避免在自主工作中过早地"乐观停止"。

### Compose 模式

Compose 模式为规范驱动开发提供一套结构化工作流。它内置了用于规划、执行、代码审查、TDD、调试、验证和合并的技能——从规范到交付代码，编排完整的生命周期。

### 提示词预测

工作时会以内联的幽灵文本形式预测你的下一条提示词——按 `Tab` 采纳。

### 深度研究

内置的 `/deep-research` 工作流会针对那些单次搜索无法解决的问题，运行一次结构化的多步骤调查。

### 无头运行与 IDE 使用

运行 `qitqode serve` 启动一个无头 HTTP 服务器，或运行 `qitqode acp` 以支持 Agent Client Protocol，从兼容的编辑器和远程环境中驱动 QitQode。

**无人值守运行。** 三个标志决定 TUI 会在多少地方停下来询问你：

| 标志 | 提问 | 工具权限 |
| --- | --- | --- |
| `--never-ask` | 自动决策 | 仍会向你请求 |
| `--fullauto` | 自动决策 | 自动批准，配置中明确拒绝的除外 |
| `--headless` | 自动决策 | 自动批准 —— 且没有 TUI；需要 `--prompt` 或管道输入 |

`--fullauto` 保留正常的交互式 TUI：你可以看着会话运行，只要权限还在代你批准，输入框上就会一直显示 `FULL-AUTO` 标记。配置中设为 `deny` 的内容仍会被拒绝。`--fullauto` 隐含 `--never-ask`，与 `--headless` 同时使用也无妨（无头模式本来就是这个行为）。

### 语音输入

由 TenVAD 驱动的实时流式语音输入。用 `/voice` 激活后开口说话——音频会按停顿分段并增量转写进输入框。需要 `sox`（在 macOS 上用 `brew install sox`，其他平台类似），以及通过 `voice` 配置字段显式配置的语音识别模型。

> **注意：** 编程模型只能通过 QitQode 后端按档位使用。`voice` 字段是一个受限的例外，仅用于语音识别和语音控制——它不会向编程模型列表中添加任何模型。

<details>
<summary><strong>WSLg 音频设置</strong></summary>

```bash
sudo apt install -y sox pulseaudio libasound2-plugins
export PULSE_SERVER=unix:/mnt/wslg/PulseServer
```

</details>

<details>
<summary><strong>SSH 远程音频（Mac → 远程主机）</strong></summary>

```bash
# Mac (local)
brew install pulseaudio
pulseaudio --load="module-native-protocol-tcp auth-ip-acl=127.0.0.1" --exit-idle-time=-1 --daemonize
# Add to ~/.ssh/config: RemoteForward 4713 127.0.0.1:4713

# Remote host
apt install -y pulseaudio pulseaudio-utils sox
export PULSE_SERVER=tcp:127.0.0.1:4713
# Verify: pactl info
```

</details>

### Dream 与 Distill

- **`/dream`**——扫描近期的会话轨迹，将持久知识提炼进项目记忆，并移除过时的条目
- **`/distill`**——从近期工作中发现反复出现的手动流程，把高置信度的候选项打包成可复用的技能、子智能体或命令

---

## 配置

QitQode 通过项目目录中的 `.qitqode/qitqode.json`（或全局的 `~/.config/qitqode/qitqode.json`）进行配置。主要选项包括：

- 模型能力选择（Free (no cost)、Adaptive、Fast、Economy、Planner、Repair、Max intelligence、以及 Orchestrated 流水线）
- 智能体权限与自定义智能体
- 检查点与记忆行为
- MCP 服务器连接
- 键位绑定与主题

Max 模式（并行 best-of-N 推理，配合裁判选择）可通过配置中的 `experimental.maxMode` 启用。

---

## 无障碍

QitQode 的 TUI 附带一等的无障碍支持：

- **无障碍模式**——设置 `QITQODE_TUI_ACCESSIBLE=1`（或在配置中设 `"tui": { "accessible": true }`）以获得对屏幕阅读器友好的体验：线性主屏渲染（不使用备用屏幕）、不捕获鼠标、低帧率、减少动效、不使用声音提示。
- **NO_COLOR**——任何非空的 [`NO_COLOR`](https://no-color.org) 值都会切换到单色渲染并使用透明背景。严重程度绝不会仅靠颜色传达（提示信息带有 `ℹ ✓ ▲ ✗` 符号，差异对比保留 `+`/`-` 标记）。
- **减少动效**——设置 `QITQODE_REDUCE_MOTION=1`（或 `"tui": { "reduce_motion": true }`）将加载动画和动效替换为静态文本。也可在运行时从命令列表中切换。
- **声音**——通过 `QITQODE_TUI_SOUND=0`、`"tui": { "sound": false }` 或命令列表中的运行时开关禁用声音提示。无障碍模式始终禁用声音。
- **播报详细程度**——在无障碍模式下，用 `QITQODE_TUI_ANNOUNCEMENTS=quiet|normal|verbose`、`"tui": { "announcements": "quiet" }` 或命令列表中的运行时选择器来控制屏幕阅读器播报的详细程度。`quiet` 只播报回合边界；`normal`（默认）会加上工具开始行；`verbose` 会加上工具完成行。错误和中止在任何级别下都始终播报。
- **高对比度主题**——选择内置的 `high-contrast` 主题，可获得纯黑/白表面以及符合 WCAG AA 的配色。
- **小尺寸终端**——TUI 在狭窄终端中会优雅降级，当窗口低于 40x8 的最小尺寸时会显示清晰的提示信息。
- **纯键盘操作的对话框**——每个对话框都能在没有鼠标的情况下完全操作：`Esc` 始终关闭，`Tab`（以及在有按钮行或列表的对话框中的方向键）移动焦点，`Enter` 或 `Space` 激活获得焦点的控件。在纯文本/NO_COLOR 终端中，高亮的列表行还会带一个 `›` 标记，因此无需颜色也能辨别当前选择。

无障碍环境变量是单向开关：`QITQODE_TUI_ACCESSIBLE=1`、`QITQODE_REDUCE_MOTION=1`、`NO_COLOR` 和 `QITQODE_TUI_SOUND=0` 始终优先于配置值和运行时开关——无障碍保障不可被反向关闭。`QITQODE_TUI_ANNOUNCEMENTS` 在设置后同样优先于配置值和运行时选择器。

---

## 开发

```bash
bun install              # Install dependencies
bun run dev              # Run in development mode
bun turbo typecheck      # Type check
```

---

## 许可证

源代码基于 [MIT 许可证](./LICENSE)授权。
