<h1 align="center">QitQode</h1>

<p align="center"><strong>具備記憶能力的終端機 AI 程式設計代理。</strong></p>

<p align="center">
  <a href="https://qitqode.com">官方網站</a>
</p>

<p align="center">
  <a href="./README.md">English</a> | <a href="./README.zh.md">简体中文</a> | <strong>繁體中文</strong> | <a href="./README.ja.md">日本語</a> | <a href="./README.fr.md">Français</a> | <a href="./README.ru.md">Русский</a> | <a href="./README.es.md">Español</a> | <a href="./README.pt.md">Português</a>
</p>

---

大多數程式設計代理在工作階段結束的那一刻就會忘掉一切。QitQode 不會。它會讀寫程式碼、執行指令、管理 Git，並在多個工作階段之間保有一份持久且可搜尋的專案記憶；當任務執行過長時，它會重建自己的脈絡，得以繼續工作，而不是從頭開始。

一個帳號，八項能力——**Free (no cost)**、**Adaptive**、**Fast**、**Economy**、**Planner**、**Repair**、**Max intelligence**。**Orchestrated** 流水線是 Qortex 單獨提供的一項能力，專供 gate → plan → build → repair 的多階段流程，並非一般互動式的模型選擇。不必管理各家供應商的儀表板、不必周旋於各種 API 金鑰、也不必為每個模型維護計費表格。

---

## 快速開始

```bash
npm install -g @qitqode/cli
# or: bun add --global @qitqode/cli

# Run
qitqode
```

首次啟動會引導你完成登入：

- **使用 QitQode 登入**——採用裝置代碼流程，適用於任何環境，包括 SSH 工作階段與遠端沙箱：CLI 會顯示一組驗證網址與代碼（在有瀏覽器可用時也會自動開啟），在任何裝置上完成授權即可
- **API 金鑰**——改為貼上一組 QitQode API 金鑰

接著從模型選擇器挑一個層級就能開始工作。整個設定就這麼簡單。

### 以繁體中文使用 QitQode

TUI 會自動偵測你的系統語系。若要手動切換，可在 QitQode 內執行 `/language`（或 `/lang`），然後從清單中選擇「繁體中文」。

<details>
<summary><strong>WSL：剪貼簿問題</strong></summary>

若你在 WSL 上複製時遇到亂碼，請安裝 `xsel`：

```bash
sudo apt install xsel
```

</details>

---

## 為什麼選擇 QitQode

你不需要再多一個聊天套殼。你需要的是一個能扛下長時間任務、又不會迷失方向的代理。QitQode 圍繞四個機制打造：

### 1. 撐過工作階段的記憶

每個專案都有一層由 SQLite 全文搜尋支援的持久記憶：`MEMORY.md` 中的專案知識、自動的工作階段檢查點、暫存筆記，以及各任務的進度紀錄。當你重新開始時，相關記憶會自動注入——經過排序與 token 預算控制，而不是一股腦倒進來。代理會從中斷處接續，而不是重新認識你的程式碼庫。

### 2. 會自我重建的脈絡

長任務很容易超出脈絡視窗。QitQode 會監看視窗，在填滿前先存下狀態，並依據最新的檢查點、專案記憶與任務進度重建工作脈絡——因此長達數小時的重構不會在 token 上限處戛然而止。

### 3. 可被究責的自主性

用 `/goal` 設定停止條件。當代理認為任務已完成時，會有一個獨立的裁判模型審閱整段對話，判定目標是否真正達成——不再出現任務只做到一半就樂觀宣告「全部完成！」的情況。搭配樹狀任務追蹤器（`T1`、`T1.1`、……）與平行子代理，即可完成真正無人值守的工作。

### 4. 一份訂閱，零供應商接線

七項互動式能力，一次登入。工作階段中可用 `/free`、`/fast`、`/economical`、`/adaptive`、`/planner`、`/repair` 或 `/max-int` 隨時切換能力。Orchestrated 是獨立的 Qortex 多階段流水線，不是一般的互動式層級。內建的 qredits 顯示讓你隨時查看餘額——用量一目了然，不會到了月底才嚇一跳。

### 以及別人省略掉的部分

- **憑證靜態加密**——你的驗證權杖以一把存放在作業系統金鑰圈的金鑰封存，且代理所衍生的每一個子行程預設都會被剝除憑證相關的環境變數。
- **人人都能用的 TUI**——螢幕閱讀器無障礙模式、`NO_COLOR` 支援、減少動態效果、播報詳細度控制，以及符合 WCAG AA 的高對比主題。原生內建，而非事後補上。（詳見下文。）
- **純粹的 MIT 授權**——沒有另立的使用限制檔案，也沒有藏在 README 尾端的服務條款。
- **為真實機器打造的裝置代碼登入**——可在 SSH、容器與遠端沙箱中運作，這些是本機瀏覽器回導方式永遠做不到的。

---

## 核心功能

### 多重代理

| 代理        | 說明                                                                 |
| ----------- | -------------------------------------------------------------------- |
| **build**   | 預設。具備開發所需的完整工具權限                                      |
| **plan**    | 唯讀分析模式，用於程式碼探索與方案設計                                |
| **compose** | 編排模式，用於規格驅動開發與技能驅動的工作流程                        |

按 `Alt+M` 可在主要代理之間循環切換。子代理則由系統依需求建立。

### 持久記憶

由 SQLite FTS5 全文搜尋驅動的跨工作階段記憶：

- **專案記憶**（`MEMORY.md`）——持久的專案知識、規則與架構決策
- **工作階段檢查點**（`checkpoint.md`）——由 checkpoint-writer 子代理自動維護的結構化狀態快照
- **暫存筆記**（`notes.md`）——供代理使用的臨時筆記區
- **任務進度**（`tasks/<id>/progress.md`）——各任務的紀錄

工作階段重新開始時記憶會自動注入，因此代理無需重新認識專案脈絡。

### 智慧脈絡管理

- **自動檢查點**——依模型脈絡視窗決定何時儲存工作階段狀態
- **脈絡重建**——當脈絡逼近上限時，依最新檢查點、專案記憶、任務進度與保留下來的近期訊息重建脈絡，讓代理得以繼續當前任務
- **預算式注入**——以 token 預算控制有多少檢查點、記憶與筆記內容進入脈絡，並搭配重要性排序

### 任務追蹤

樹狀任務系統（`T1`、`T1.1`、`T1.2`、……）會自動與檢查點系統整合，因此工作階段重新開始時任務進度得以保留。

### 子代理系統

主要代理可依需求建立子代理。子代理共用當前的工作階段脈絡，可平行運作，並具備生命週期追蹤、取消與背景執行能力。

### 目標／停止條件

`/goal` 指令為工作階段設定停止條件。當代理試圖停止時，會有一個獨立的裁判模型評估對話，判定條件是否真正滿足——避免自主工作過程中過早出現「樂觀停止」。

### Compose 模式

Compose 模式為規格驅動開發提供結構化的工作流程。它內建了用於規劃、執行、程式碼審查、TDD、除錯、驗證與合併的技能——編排從規格到交付程式碼的完整生命週期。

### 提示預測

在你工作時，行內的灰字建議會預測你的下一則提示——按 `Tab` 即可採用。

### 深度研究

內建的 `/deep-research` 工作流程會針對單次搜尋無法解決的問題，執行結構化的多步驟調查。

### 無介面與 IDE 使用

執行 `qitqode serve` 可啟動無介面的 HTTP 伺服器，或執行 `qitqode acp` 以支援 Agent Client Protocol，從相容的編輯器與遠端環境驅動 QitQode。

**無人值守執行。** 三個旗標決定 TUI 會在多少地方停下來詢問你：

| 旗標 | 提問 | 工具權限 |
| --- | --- | --- |
| `--never-ask` | 自動決策 | 仍會向你請求 |
| `--fullauto` | 自動決策 | 自動核准，設定中明確拒絕的除外 |
| `--headless` | 自動決策 | 自動核准 —— 且沒有 TUI；需要 `--prompt` 或管線輸入 |

`--fullauto` 保留正常的互動式 TUI：你可以看著工作階段執行，只要權限還在代你核准，輸入框上就會一直顯示 `FULL-AUTO` 標記。設定中設為 `deny` 的內容仍會被拒絕。`--fullauto` 隱含 `--never-ask`，與 `--headless` 同時使用也無妨（無介面模式本來就是這個行為）。

### 語音輸入

由 TenVAD 驅動的即時串流語音輸入。以 `/voice` 啟用後即可開口說話——音訊會依停頓切分，並逐步轉寫進輸入框。需要 `sox`（macOS 上用 `brew install sox`，其他平台類似），並透過 `voice` 設定欄位明確設定一個語音辨識模型。

> **注意：** 程式設計模型僅透過 QitQode 後端以層級方式提供。`voice` 欄位是一個範圍受限的例外，僅用於語音辨識與語音控制——它不會把模型加入程式設計模型清單。

<details>
<summary><strong>WSLg 音訊設定</strong></summary>

```bash
sudo apt install -y sox pulseaudio libasound2-plugins
export PULSE_SERVER=unix:/mnt/wslg/PulseServer
```

</details>

<details>
<summary><strong>SSH 遠端音訊（Mac → 遠端主機）</strong></summary>

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

### Dream 與 Distill

- **`/dream`**——掃描近期的工作階段軌跡，將持久知識萃取進專案記憶，並移除過時的條目
- **`/distill`**——在近期工作中找出重複的手動流程，並把高信心的候選項打包成可重用的技能、子代理或指令

---

## 設定

QitQode 透過專案目錄中的 `.qitqode/qitqode.json`（或全域的 `~/.config/qitqode/qitqode.json`）進行設定。主要選項包括：

- 模型能力選擇（Free (no cost)、Adaptive、Fast、Economy、Planner、Repair、Max intelligence、以及 Orchestrated 流水線）
- 代理權限與自訂代理
- 檢查點與記憶行為
- MCP 伺服器連線
- 鍵位綁定與主題

Max Mode（平行 best-of-N 推理搭配裁判選取）可透過設定中的 `experimental.maxMode` 啟用。

---

## 無障礙

QitQode 的 TUI 內建原生無障礙支援：

- **無障礙模式**——設定 `QITQODE_TUI_ACCESSIBLE=1`（或在設定中設 `"tui": { "accessible": true }`）以取得對螢幕閱讀器友善的體驗：線性的主畫面繪製（不使用替代畫面）、不擷取滑鼠、低影格率、減少動態效果，以及沒有音效提示。
- **NO_COLOR**——任何非空的 [`NO_COLOR`](https://no-color.org) 值都會切換為單色繪製並使用透明背景。嚴重程度絕不僅以顏色傳達（快顯訊息會帶有 `ℹ ✓ ▲ ✗` 符號，差異則保留 `+`/`-` 標記）。
- **減少動態效果**——設定 `QITQODE_REDUCE_MOTION=1`（或 `"tui": { "reduce_motion": true }`）可將旋轉圖示與動畫替換為靜態文字。也可在執行期間從指令清單切換。
- **音效**——以 `QITQODE_TUI_SOUND=0`、`"tui": { "sound": false }` 或指令清單中的執行期切換來停用音效提示。無障礙模式一律停用音效。
- **播報詳細度**——在無障礙模式下，可用 `QITQODE_TUI_ANNOUNCEMENTS=quiet|normal|verbose`、`"tui": { "announcements": "quiet" }` 或指令清單中的執行期選擇器，控制螢幕閱讀器播報的詳細程度。`quiet` 只播報回合邊界；`normal`（預設）會加上工具開始的訊息；`verbose` 會再加上工具完成的訊息。錯誤與中止在任何層級都一律播報。
- **高對比主題**——選擇內建的 `high-contrast` 主題，可取得純黑／白介面與符合 WCAG AA 的顏色。
- **小型終端機**——TUI 在狹窄終端機中會優雅降級，並在視窗低於 40x8 最小尺寸時顯示清楚的訊息。
- **僅鍵盤操作的對話框**——每個對話框都能完全以鍵盤操作：`Esc` 一律關閉，`Tab`（在對話框有按鈕列或清單時，方向鍵亦可）移動焦點，`Enter` 或 `Space` 啟動聚焦的控制項。在純文字／NO_COLOR 終端機中，被反白的清單列還會帶有 `›` 標記，讓選取狀態不靠顏色也能辨識。

無障礙環境變數是單向開關：`QITQODE_TUI_ACCESSIBLE=1`、`QITQODE_REDUCE_MOTION=1`、`NO_COLOR` 與 `QITQODE_TUI_SOUND=0` 一律優先於設定值與執行期切換——無障礙保證無法被反向關閉。`QITQODE_TUI_ANNOUNCEMENTS` 在設定時同樣優先於設定值與執行期選擇器。

---

## 開發

```bash
bun install              # Install dependencies
bun run dev              # Run in development mode
bun turbo typecheck      # Type check
```

---

## 授權

原始碼採用 [MIT License](./LICENSE) 授權。
