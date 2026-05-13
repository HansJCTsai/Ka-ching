# 🤑 Ka-ching!

> *Your money is talking. Every morning, in your Line.*
>
> **錢錢來了哦** — 每天美股盤後，AI 分析直送你的 Line。
>
> 採用 **Harness Engineering** 哲學設計，模型可換、Harness 不變。

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![GitHub Actions](https://img.shields.io/badge/CI-GitHub%20Actions-2088FF?logo=github-actions&logoColor=white)](.github/workflows/daily_kaching.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Built with Harness](https://img.shields.io/badge/Built_with-Harness_Engineering-FF6B6B)](https://github.com/NousResearch/hermes-agent)

[中文版](#) · [English README](README.en.md)

---

## ✨ 它能做什麼？

每天美股盤後，**Ka-ching!** 自動執行：

```
📊 抓股價 + 新聞   →   🤖 AI 雙引擎分析   →   💰 Line "Ka-ching!" 一聲
```

打開手機就看到專屬於你的市場報告。

- **個人化** — 每位訂閱者有自己的自選股清單與分析風格
- **雙 AI 引擎** — Gemini 負責資料前處理，Claude 負責深度分析
- **零維運** — 跑在 GitHub Actions 上，免伺服器、免費
- **可演進** — 從定時推播 → CLI → Web Chat → Line Bot 對話

---

## 🎬 預覽

每天早上 5:30（台灣時間），你的 Line 會 *Ka-ching!* 一聲：

```
┌─────────────────────────────┐
│  🤑 Ka-ching!  錢錢來了哦      │
│  2026-04-27                 │
├─────────────────────────────┤
│  🟢 AAPL   $189.50  +2.3%   │
│  🔴 NVDA   $875.20  -1.1%   │
│  🟢 TSLA   $245.80  +0.8%   │
├─────────────────────────────┤
│  🤖 AI 觀點                  │
│  科技股今日呈現分化⋯          │
└─────────────────────────────┘
```

---

## 🚀 快速開始

### 前置需求

- Python 3.11+
- Poetry（依賴管理）
- 以下 API 金鑰：
  - Anthropic API（Claude）
  - Google Gemini API
  - Line Channel Access Token
  - News API（可選）

### 安裝

```bash
# 1. Clone 專案
git clone https://github.com/yourname/kaching.git
cd kaching

# 2. 安裝依賴
poetry install

# 3. 設定環境變數
cp .env.example .env
# 編輯 .env 填入你的 API keys

# 4. 設定訂閱者
cp subscribers.example.json subscribers.json
# 編輯 subscribers.json 加入你的 Line user_id 與自選股
```

### 本地測試執行

```bash
poetry run python -m interfaces.scheduler
```

如果一切正常，你的 Line 會 *Ka-ching!* 一聲收到測試報告 🎉

### 部署到 GitHub Actions

```bash
# 1. 推到 GitHub
git push origin main

# 2. 在 repo Settings → Secrets 設定以下變數
#    - ANTHROPIC_API_KEY
#    - GEMINI_API_KEY
#    - LINE_CHANNEL_ACCESS_TOKEN
#    - NEWS_API_KEY

# 3. 手動觸發測試
gh workflow run daily_kaching.yml
```

之後每天美股盤後會自動 *Ka-ching!* 🤑

---

## 🧠 為什麼這樣設計？

### Harness Engineering — 投資外殼，不投資模型

Ka-ching! 的核心設計哲學借鑑自 Nous Research 的 [Hermes Agent](https://github.com/NousResearch/hermes-agent)：

> **2026 年真正的 AI 突破，不是更聰明的模型，而是更聰明的「模型外層包裝」。**

「Harness」原意是馬具 — 馬本身的力量是固定的，但**怎麼套韁繩、怎麼導引方向、怎麼回饋**，決定了這匹馬能拉多少、跑多快、去哪裡。LLM 也一樣：模型本身的能力上限變化沒那麼快，但你**怎麼包裝它**，決定了它能不能在真實世界做事。

### 五層 Harness 架構

```
┌────────────────────────────────────────────┐
│  編排層  Orchestration  → GitHub Actions    │
│  解決「何時、由誰、用什麼順序呼叫 LLM」          │
├────────────────────────────────────────────┤
│  指令層  Instructions   → skills/*.md       │
│  解決「LLM 要做什麼、不能做什麼、怎麼輸出」      │
├────────────────────────────────────────────┤
│  約束層  Constraints    → config.py + .env  │
│  解決「在什麼環境、用什麼模型、什麼參數」        │
├────────────────────────────────────────────┤
│  記憶層  Memory         → subscribers.json  │
│  解決「跨次執行如何記住狀態、個人化」           │
├────────────────────────────────────────────┤
│  回饋層  Feedback       → notifier + logs   │
│  解決「結果如何記錄、如何用來改進系統」          │
└────────────────────────────────────────────┘
              ↑↓
        ┌──────────┐
        │   LLM    │ ← 可隨時替換的核心
        └──────────┘
```

### 為什麼這樣分層？

舉三個實際場景：

🔄 **Claude 出了新版本想升級**
- 沒用 Harness：找出所有呼叫處、改 prompt、redeploy
- Ka-ching! 的做法：改 `config.py` 一個字串，**程式碼一行不動**

🎨 **想調整 AI 分析風格**
- 沒用 Harness：在程式碼裡找 prompt、改字串、redeploy
- Ka-ching! 的做法：編輯 `skills/conservative.md`，commit 推上去就好

🔔 **想加「跌破成本價警報」**
- 沒用 Harness：要改抓資料、分析、推送三個地方
- Ka-ching! 的做法：在 `optional-skills/alert.md` 描述條件，主流程一行不動

### 為什麼不用 LangChain / LangGraph？

簡單說：**框架抽象太多，違反「換模型不動程式」的原則**。

| | LangChain / LangGraph | Ka-ching! |
|---|---|---|
| Prompt 存放 | Python 字串裡 | `skills/*.md` |
| 換模型 | 要改 Chain 設定 | 只改 skill |
| 依賴複雜度 | 重 | 輕（純 SDK） |
| 適合場景 | 複雜動態決策 | 線性 pipeline |

完整論述見 [`docs/ADR/001-no-langchain.md`](docs/ADR/001-no-langchain.md)。

---

## 📁 專案結構

```
kaching/
│
├── .github/workflows/daily_kaching.yml # GitHub Actions 排程
│
├── agent/                              # 核心邏輯（嚴禁加入 prompt 字串）
│   ├── fetcher.py                      # 只負責拿資料
│   ├── analyzer.py                     # 只負責呼叫 LLM
│   ├── notifier.py                     # 只負責推送
│   └── alert.py                        # 價格警報（Phase 2）
│
├── interfaces/                         # 入口層（每檔不超過 100 行）
│   ├── scheduler.py                    # GitHub Actions 入口
│   ├── cli.py                          # CLI 入口（Phase 3）
│   └── web.py                          # Web/Bot Webhook（Phase 3）
│
├── skills/                             # AI 行為定義
│   ├── harness.md                      # Gemini 前處理指令
│   ├── conservative.md                 # Claude 保守風格
│   └── aggressive.md                   # Claude 積極風格
│
├── optional-skills/                    # 未來功能停車場
├── tools/flex_formatter.py             # Line Flex Message 格式化
├── tests/                              # 測試
├── docs/                               # 設計文件 / ADR
├── plans/roadmap.md                    # 開發路線圖
│
├── subscribers.json                    # ⚠️ 個人資料（git ignored）
├── subscribers.example.json            # 訂閱者範本
├── config.py                           # 全域設定
├── pyproject.toml                      # 依賴管理
├── .env.example                        # 環境變數範本
│
├── AGENTS.md                           # 🤖 給 AI 編碼代理讀
└── README.md                           # 📖 給人類讀（你正在看的）
```

---

## ⚙️ 個人化設定

### 訂閱者設定（`subscribers.json`）

每位訂閱者可以自訂：

```json
{
  "subscribers": [
    {
      "name": "我自己",
      "user_id": "Uxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "stocks": ["AAPL", "NVDA", "TSLA"],
      "skill": "aggressive",
      "timezone": "Asia/Taipei",
      "active": true
    },
    {
      "name": "老爸",
      "user_id": "Uyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy",
      "stocks": ["VOO", "QQQ"],
      "skill": "conservative",
      "timezone": "Asia/Taipei",
      "active": true
    }
  ]
}
```

### 內建 Skills

| Skill | 風格 | 適合誰 |
|---|---|---|
| `conservative` | 強調風險、保守用詞 | 退休族、長期投資者 |
| `aggressive` | 強調機會、積極用詞 | 短線交易者 |
| `summary_only` | 純摘要不分析 | 只想知道數字 |

### 自訂你自己的 Skill

```bash
# 1. 建立新 skill
cp skills/conservative.md skills/my_style.md

# 2. 編輯內容（純 Markdown）
vim skills/my_style.md

# 3. 在 subscribers.json 指定使用
"skill": "my_style"
```

完整 skill 規格見 [`AGENTS.md`](AGENTS.md#-skill-規格)。

---

## 🛤 開發路線圖

當前進度由 [`plans/roadmap.md`](plans/roadmap.md) 維護。

### Phase 1：MVP ✅（進行中）
每天定時推播個人化報告 *Ka-ching!*

### Phase 2：擴充
- 跌破價格主動警報
- Line Bot 指令訂閱

### Phase 3：互動介面
- CLI 即時查詢：`kaching "NVDA 最近表現如何？"`
- Line Bot 對話模式
- Web Chat

### Phase 4：自我改善
- Agent 根據歷史推送結果自動調整 skill
- 跨次執行的記憶累積

### Phase 5：SDK 化（長期目標）
把整個 Harness 抽象成 `harness-agent` 套件，讓其他人能用同樣的設計做自己的領域 Agent（天氣 *Ding-dong!*、新聞 *News flash!*、賽事 *Goal!*⋯）。

---

## 🎭 品牌識別

### 名字由來

**Ka-ching!** 是收銀機進帳時的「叮咚」聲，象徵：
- 💰 錢錢來了的喜悅
- 🔔 Line 通知聲響起的瞬間
- 🎯 每次推送都是一個「成交」

### Slogan

> ***Your money is talking.***
>
> ***錢錢來了哦 💰***

### Bot 自我介紹

```
嗨～我是 Ka-ching! 🤑

每天美股盤後，我會帶著市場消息來找你
打開 Line，叮咚一聲 — 錢錢來了哦 💰

設定一下你的自選股，我就開始服務你！
```

### 視覺主題色

```
主色：💰 金色   #FFD700
副色：🐂 牛市綠 #4CAF50 / 🐻 熊市紅 #F44336
強調：✨ 鈴鐺白 #FFFFFF
```

---

## 🤝 貢獻

歡迎 PR！但請先閱讀 [`AGENTS.md`](AGENTS.md)，因為 Ka-ching! 有嚴格的設計守則：

- ✅ Prompt 永遠存在 `skills/*.md`，**不能**寫進 Python 字串
- ✅ 新功能優先做成 `optional-skills/`，**不要**膨脹 `agent/` 核心
- ✅ Commit message 使用 conventional commits（前綴規範見 AGENTS.md）

### 提交前檢查

```bash
poetry run ruff check . && \
poetry run black --check . && \
poetry run pytest
```

---

## 🧪 開發指令備忘錄

```bash
# 安裝
poetry install

# 本地執行（測試 Ka-ching!）
poetry run python -m interfaces.scheduler

# 測試
poetry run pytest
poetry run pytest tests/test_fetcher.py -v

# Lint & Format
poetry run ruff check .
poetry run black .

# 觸發 CI
gh workflow run daily_kaching.yml
```

---

## 📚 延伸閱讀

### 設計文件

- [`AGENTS.md`](AGENTS.md) — AI 編碼代理操作契約
- [`docs/PHILOSOPHY.md`](docs/PHILOSOPHY.md) — Harness Engineering 完整論述
- [`docs/ADR/`](docs/ADR/) — 架構決策記錄
  - `001-no-langchain.md` — 為什麼不用 LangChain
  - `002-dual-ai-engine.md` — 為什麼 Gemini + Claude 雙引擎
  - `003-skill-driven-prompts.md` — 為什麼 Prompt 用 Markdown

### 外部參考

- [Hermes Agent](https://github.com/NousResearch/hermes-agent) — Harness Engineering 概念來源
- [Anthropic Skills 標準](https://agentskills.io)
- [AGENTS.md 規範](https://agents.md/)
- [Line Messaging API](https://developers.line.biz/en/docs/messaging-api/)

---

## ❓ 常見問題

<details>
<summary><b>Q: 為什麼叫 Ka-ching?</b></summary>

Ka-ching! 是收銀機「叮咚」進帳的聲音 💰  
每天美股盤後，你的 Line 會「叮咚」一聲 — 錢錢來了哦！  
這個名字捕捉了「每天有好消息進帳」的美好瞬間。

</details>

<details>
<summary><b>Q: 這跟用 ChatGPT 看股票有什麼不同？</b></summary>

ChatGPT 沒有你的個人化資料、不會主動推播、無法整合到 Line。Ka-ching! 是「為你量身打造、主動服務」的個人 Agent。

</details>

<details>
<summary><b>Q: 為什麼用兩個 AI 模型？不是更貴嗎？</b></summary>

實際更便宜。Gemini 處理大量原始資料快又便宜，把雜訊過濾掉後，只把精華餵給 Claude 做深度分析，整體 token 消耗反而比單用 Claude 少。

</details>

<details>
<summary><b>Q: GitHub Actions 真的免費跑得起來嗎？</b></summary>

是的。Public repo 完全免費，Private repo 每月有 2000 分鐘額度。Ka-ching! 每天跑一次約 1-2 分鐘，完全用不完。

</details>

<details>
<summary><b>Q: 訂閱者 user_id 怎麼拿？</b></summary>

請參考 Line Developer 文件設定 webhook，或先用 [Line Notify](https://notify-bot.line.me/) 過渡。完整流程見 [`docs/setup-line.md`](docs/setup-line.md)。

</details>

<details>
<summary><b>Q: 這不是投資建議嗎？要承擔法律責任？</b></summary>

Ka-ching! 僅為**個人資訊整理工具**，所有 AI 產生的「分析」都是統計與摘要，**不構成投資建議**。投資決策請自行負責。

</details>

---

## 📜 授權

MIT License — 詳見 [LICENSE](LICENSE)

---

## 🙏 致謝

- [Nous Research](https://nousresearch.com/) — Harness Engineering 哲學啟發
- [Anthropic](https://anthropic.com/) — Claude API & Skills 標準
- [Google](https://ai.google.dev/) — Gemini API
- 所有 vibe coding 社群的開源貢獻者

---

<p align="center">
  <i>🤑 Ka-ching! · Built with Harness Engineering · 模型可換，Harness 不變</i>
</p>

<p align="center">
  <b>Your money is talking. Are you listening?</b>
</p>
