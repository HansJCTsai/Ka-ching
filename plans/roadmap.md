# 🛤 Ka-ching! 完整 Roadmap — v1 + v2 + v3

> 給人類與 AI 編碼代理共讀的執行路線圖。
> 配速：**每天 1 小時，週末 1-2 小時**，7 個月完成三版作品集。

---

## 📍 Current Status

```yaml
phase: 1 — MVP (v1)
week: 1 / 28
day: 2 / 140
last_updated: 2026-06-09

versions:
  v1: Line Bot 推播        → Day 1-60   （12 週）✅ 進行中
  v2: 手機 Chat Portal     → Day 61-100 （8 週）  ⏳ 待開始
  v3: 桌面分析工作站        → Day 101-140（8 週）  ⏳ 待開始

tech_stack:
  package_manager_py: uv
  package_manager_js: npm
  llm_primary: Claude (Anthropic)
  llm_harness: Gemini (Google)
  agent_framework: deepagents (LangChain)
  agent_advanced: LangGraph（v3）
  frontend_mobile: React Native + TypeScript（v2）
  frontend_desktop: Tauri + React + TypeScript（v3）
  backend_api: FastAPI + SSE（v2/v3）
  observability: LangSmith
  ci: GitHub Actions
```

---

## 🎯 整體目標與三版架構

```
Day 1-60   → v1 Line Bot MVP（推播 + deepagents + LangSmith）
Day 61-100 → v2 手機 Chat（FastAPI + React Native + SSE）
Day 101-140 → v3 桌面工作站（Tauri + React + LangGraph）
```

**三版共用後端架構（Harness Engineering）：**
```
資料層    →  deepagents Agent（AI 層）→  推播 / API / Sidecar
Fetcher        ↑              ↑
           Skills.md      LangSmith
         （可插拔 prompt）  （可觀測）

v1：GitHub Actions → Agent → Line 推播
v2：REST API       → Agent → SSE streaming → React Native
v3：Python sidecar → Agent + LangGraph → Tauri + React
```

**7 個月後產出：**
- ✅ v1：Line Bot 自動推播（deepagents + Harness）
- ✅ v2：手機 Chat App（React Native + SSE）
- ✅ v3：桌面分析工作站（Tauri + LangGraph）
- ✅ 2 篇技術文章 + LangSmith 可量化數據
- ✅ 完整作品集 → 台積電面試材料

---

## ⏱ 時間預算

```
每天 1 小時（平日）  × 5 天 = 5h/週
週末加碼 1-2 小時             = 1-2h/週
─────────────────────────────────────
總計：每週 ~6 小時，28 週 ~168 小時
```

---

## 🚦 卡關處理規則

| 狀況 | 處理 |
|---|---|
| 今天累了 | 至少做 15 分鐘 |
| 卡關 1 天 | 自己 Google / 試試 |
| 卡關 2 天 | 找 Claude 討論 |
| 卡關 1 週 | 重新評估這個任務是否必要 |

---

## ✅ Phase 0：專案啟動（已完成）

- [x] 設計專案架構與 Harness 哲學
- [x] 撰寫 AGENTS.zh.md / AGENTS.en.md
- [x] 撰寫 README.md / README.en.md
- [x] 建立 GitHub repo
- [x] 初始 commit
- [x] LICENSE / .gitignore / .env.example
- [X] 加 GitHub Topics 標籤
- [X] About 區塊填寫
- [X] 取得 Anthropic API Key
- [X] 取得 Gemini API Key
- [X] 申請 Line Developer 帳號

---

# 🚧 Phase 1：v1 — Line Bot 每日推播（Day 1-60）

**目標：** 每天美股盤後自動推送個人化報告到 Line
**Definition of Done：** 隔天早上手機自動收到 Ka-ching! 推送

---

## 📅 Week 1-2：環境與資料層（Day 1-10）

> **本週目標：** fetcher.py 完成，能抓股價與新聞

### Day 1 — uv 與專案骨架（⏱ 1h）
- [X] 在本地 clone repo
- [X] 安裝 uv：`curl -LsSf https://astral.sh/uv/install.sh | sh`
- [X] `uv init` 初始化專案
- [X] `uv add langchain-anthropic langchain-google-genai deepagents langsmith`
- [X] `uv add --dev pytest pytest-mock ruff`
- [X] 建立資料夾骨架（`agent/`、`interfaces/`、`skills/`、`tools/`、`evals/`）
- [X] 寫各模組的 `__init__.py`
- [X] commit：`chore: setup uv and project scaffold`

### Day 2 — yfinance REPL 探索（⏱ 1h）✅
- [X] `uv add yfinance` 並進入 REPL：`uv run python`
- [X] `yf.Ticker("AAPL").history(period="2d")` 看回傳結構
- [X] 記錄你看到什麼欄位（成交量、開盤、收盤⋯）
- [X] 不寫程式，純探索

### Day 3 — fetcher.py 第一版（⏱ 1h）
- [ ] 寫 `get_stock_data(symbols)` 函式
- [ ] 加上 type hints
- [ ] 處理「資料拿不到」的情況
- [ ] commit：`feat(agent): add fetcher.get_stock_data`

### Day 4 — Error handling + 設計優化（⏱ 1h）
- [ ] 加 try/except 包外部呼叫
- [ ] 定義回傳格式（dict structure）
- [ ] 確認符合 AGENTS.md 的模組契約
- [ ] commit：`fix(agent): robust error handling in fetcher`

### Day 5 — test_fetcher.py（⏱ 1h）
- [ ] 學 pytest-mock 怎麼 mock 外部 API
- [ ] 寫 3-5 個測試 case
- [ ] `uv run pytest` 通過
- [ ] commit：`test: add fetcher tests with mocks`

### 🎯 週末（⏱ 1-2h）
- [ ] 寫 `docs/learning-notes/unit-01-uv.md`
- [ ] 寫 `docs/learning-notes/unit-02-fetcher.md`
- [ ] 寫 ADR-001：為什麼選 yfinance

### Day 6 — News API 申請與探索（⏱ 1h）
- [ ] 申請 NewsAPI 免費 key（newsapi.org）
- [ ] 在 REPL 玩 requests 抓新聞
- [ ] 觀察回傳格式

### Day 7 — fetcher 加新聞功能（⏱ 1h）
- [ ] 寫 `get_news(symbols, hours)`
- [ ] 處理 API rate limit
- [ ] commit：`feat(agent): add fetcher.get_news`

### Day 8 — 完整測試與重構（⏱ 1h）
- [ ] 跑全套 lint + format + test
- [ ] 補測試覆蓋率
- [ ] commit：`test: complete fetcher test coverage`

### Day 9 — 緩衝日（⏱ 1h）
- [ ] 補上前面落後的進度
- [ ] 或 debug 沒解決的問題
- [ ] 或讀 Anthropic blog 文章

### Day 10 — 寫 ADR + 學習筆記（⏱ 1h）
- [ ] 完成 `docs/ADR/001-data-source-choice.md`
- [ ] 整理 Week 1-2 學到的概念
- [ ] push 到 GitHub

**🏁 Week 1-2 完成標誌：**
```bash
uv run python -c "from agent.fetcher import get_stock_data; print(get_stock_data(['AAPL']))"
# 應該印出股價資料
```

---

## 📅 Week 3-4：deepagents + Skills 雙引擎（Day 11-20）

> **本週目標：** 用 deepagents 跑出第一次 AI 分析，理解 Harness + Agent 設計
>
> **架構決策：** Skills.md 作為 Agent 的 `system_prompt`，Fetcher 函式作為 Agent 的 `tools`

### Day 11 — Anthropic API + LangSmith 起手式（⏱ 1h）
- [ ] 設定 `.env`：`ANTHROPIC_API_KEY`、`LANGSMITH_API_KEY`、`LANGSMITH_PROJECT`
- [ ] 第一次呼叫 `langchain_anthropic.ChatAnthropic`
- [ ] 在 LangSmith dashboard 確認 trace 有出現
- [ ] 觀察 token 使用量與成本
- [ ] commit：`chore: setup anthropic + langsmith env`

### Day 12 — deepagents 探索（⏱ 1h）
- [ ] 讀 deepagents 文件：`create_deep_agent` 的 `model`、`tools`、`system_prompt`
- [ ] 寫最簡單的 Hello World Agent（不接 tools，先跑通）
- [ ] 確認 LangSmith 有記錄 Agent 的思考過程
- [ ] 理解：Skills.md 就是 `system_prompt`，Fetcher 函式就是 `tools`

### Day 13 — 寫 conservative.md（⏱ 1h）
- [ ] 設計保守風格的 system prompt
- [ ] 加入 When / Instructions / Output / Examples 四段
- [ ] 重點：「這個 md 是 Agent 的人格設定，不是 prompt template」
- [ ] commit：`skill(conservative): initial conservative analysis style`

### Day 14 — 寫 aggressive.md（⏱ 1h）
- [ ] 設計積極風格的 system prompt
- [ ] 兩個 skill 比較差異
- [ ] 確認 `load_skill(skill_name)` 函式能正確讀取
- [ ] commit：`skill(aggressive): initial aggressive analysis style`

### Day 15 — stock_agent.py 第一版（⏱ 1h）
- [ ] 寫 `agent/stock_agent.py`
- [ ] `create_stock_analyzer(skill_name)` → 回傳 deepagents Agent
- [ ] `tools=[get_stock_price]`（先只接一個 tool）
- [ ] 手動測試：`agent.invoke({"messages": [{"role": "user", "content": "分析 AAPL"}]})`
- [ ] commit：`feat(agent): add stock_agent with deepagents`

### 🎯 週末（⏱ 1-2h）
- [ ] A/B 測試：同一份資料、conservative vs aggressive、看 LangSmith trace 差異
- [ ] 截圖 LangSmith dashboard（之後寫文章用）
- [ ] 寫學習筆記

### Day 16 — Gemini API + gemini_harness tool（⏱ 1h）
- [ ] 設定 `GOOGLE_API_KEY`
- [ ] 第一次呼叫 Gemini
- [ ] **關鍵設計：`gemini_harness` 包裝成 tool function 供 Agent 呼叫**
- [ ] 寫 `tools/gemini_harness.py`：`def gemini_harness(raw_data: str) -> str`

### Day 17 — 寫 harness.md（⏱ 1h）
- [ ] 設計 Gemini 前處理的 prompt（資料清理、結構化）
- [ ] 重點：「過濾噪音、壓縮 token、確保格式一致」
- [ ] commit：`skill(harness): initial preprocessing skill`

### Day 18 — 整合所有 tools 到 Agent（⏱ 1h）
- [ ] `tools=[get_stock_price, get_news, get_technicals, gemini_harness]`
- [ ] Agent 現在自動決策：分析前先呼叫 gemini_harness
- [ ] 觀察 LangSmith：Agent 的 tool 呼叫順序是否合理
- [ ] commit：`feat(agent): integrate all tools + gemini_harness`

### Day 19 — analyze_for_subscriber + Token 統計（⏱ 1h）
- [ ] 寫 `analyze_for_subscriber(subscriber, stocks)`
- [ ] 從 LangSmith 撈 token 統計（不用自己寫）
- [ ] 算雙引擎 vs 單 Claude 省了多少 token
- [ ] **這數據之後寫文章會用！**

### Day 20 — 測試 + ADR + 學習筆記（⏱ 1h）
- [ ] 寫 `test_agent.py`（mock deepagents 的 invoke）
- [ ] 寫 `docs/ADR/002-dual-ai-engine.md`
- [ ] 寫 `docs/ADR/003-why-deepagents.md`
- [ ] `docs/learning-notes/unit-03-deepagents-harness.md`

**🏁 Week 3-4 完成標誌：**
```python
from agent.stock_agent import create_stock_analyzer
analyzer = create_stock_analyzer("conservative")
result = analyzer.invoke({"messages": [{"role": "user", "content": "分析 NVDA"}]})
print(result["messages"][-1].content)  # 印出 AI 寫的中文分析
# LangSmith dashboard 可看到完整 trace ✅
```

---

## 📅 Week 5-6：Line 推送（Day 21-30）⚠️ 最容易卡

> **本週目標：** 手機收到第一聲 Ka-ching!

### Day 21 — Line Developers 註冊（⏱ 1h）
- [ ] 註冊 [Line Developers](https://developers.line.biz/)
- [ ] 建立 Provider
- [ ] **不要急，看完所有設定選項**

### Day 22 — 建立 Messaging Channel（⏱ 1h）
- [ ] 建立 Messaging API Channel
- [ ] 拿到 Channel Access Token（存進 `.env`）
- [ ] 拿到 Channel Secret
- [ ] 開啟 Webhook（之後 v2 用）

### Day 23 — 拿到自己的 user_id（⏱ 1-2h）⚠️ 容易卡
- [ ] 加 Bot 為好友
- [ ] 用 Webhook 拿 user_id（或用 Line Login Playground）
- [ ] 存進 `subscribers.json`
- [ ] **如果卡超過 2 小時就找 Claude 討論**

### Day 24 — curl 推第一則訊息（⏱ 1h）
- [ ] 用 curl 直接呼叫 Line Push API
- [ ] 收到純文字「Hello Ka-ching!」
- [ ] **這是第一個重要里程碑！**

### Day 25 — Flex Message Builder（⏱ 1h）
- [ ] 玩 [Flex Message Simulator](https://developers.line.biz/flex-simulator/)
- [ ] 設計報告的視覺
- [ ] 匯出 JSON

### 🎯 週末（⏱ 1-2h）
- [ ] 完成 Flex Message 設計
- [ ] 拍照記錄（之後文章用）

### Day 26 — flex_formatter.py（⏱ 1h）
- [ ] 寫 `tools/flex_formatter.py`
- [ ] 把 stock data 轉成 Flex JSON
- [ ] commit：`feat(tools): add flex_formatter`

### Day 27 — notifier.py（⏱ 1h）
- [ ] 寫 `send_flex_to_user(user_id, flex)`
- [ ] 寫 `broadcast(subscribers)`
- [ ] 加 log_delivery
- [ ] commit：`feat(agent): add notifier`

### Day 28 — End-to-End 串接（⏱ 1h）
- [ ] fetcher → stock_agent（deepagents）→ notifier 跑完整流程
- [ ] 在本地手動觸發
- [ ] 收到漂亮的 Flex Message 🎉
- [ ] LangSmith 確認完整 trace

### Day 29 — Debug + 優化（⏱ 1h）
- [ ] 修正樣式問題
- [ ] 補 notifier 測試
- [ ] commit：`test: add notifier tests`

### Day 30 — 慶祝 + 學習筆記 🎉（⏱ 1h）
- [ ] 寫 `unit-05-line-bot.md`
- [ ] 拍照、截圖、分享給朋友
- [ ] **你做到了！**

**🏁 Week 5-6 完成標誌：**
```bash
uv run python -m interfaces.scheduler
# 手機收到 Flex Message
```

---

## 📅 Week 7-8：自動化上線（Day 31-40）

> **本週目標：** GitHub Actions 每天自動跑

### Day 31 — scheduler.py（⏱ 1h）
- [ ] 寫 `interfaces/scheduler.py`
- [ ] 讀 `subscribers.json`
- [ ] 對每個訂閱者執行完整流程
- [ ] commit：`feat(interface): add scheduler entry point`

### Day 32 — subscribers 處理（⏱ 1h）
- [ ] 寫 subscriber loader（驗證格式）
- [ ] 處理 active=false 的訂閱者
- [ ] 加 error isolation（一人失敗不影響其他人）

### Day 33 — GitHub Actions cron（⏱ 1h）
- [ ] 學 cron 語法（[crontab.guru](https://crontab.guru/)）
- [ ] 美股盤後 = UTC 21:30 = 台北 05:30
- [ ] 寫第一版 yml

### Day 34 — workflow yml（⏱ 1h）
- [ ] 寫完整的 `daily_kaching.yml`
- [ ] 加 manual trigger（workflow_dispatch）
- [ ] commit：`ci: add daily kaching workflow`

### Day 35 — 設定 GitHub Secrets（⏱ 1h）
- [ ] Settings → Secrets → 加以下 keys：
  - `ANTHROPIC_API_KEY`
  - `GOOGLE_API_KEY`
  - `LINE_CHANNEL_ACCESS_TOKEN`
  - `LANGSMITH_API_KEY`
  - `LANGSMITH_PROJECT`
- [ ] 加 `SUBSCRIBERS_JSON`（base64 encode）
- [ ] 第一次手動觸發 CI

### 🎯 週末（⏱ 1-2h）
- [ ] 手動觸發 → debug
- [ ] 看 Actions log 找錯

### Day 36 — 時區陷阱（⏱ 1h）⚠️ 容易踩
- [ ] 確認 cron 真的是台北 05:30
- [ ] 處理夏令時間
- [ ] 美股休市日跳過

### Day 37 — Logging 系統（⏱ 1h）
- [ ] 用 `logging` 取代 print
- [ ] 設定 log level
- [ ] CI 跑的 log 可以從 Actions 看

### Day 38 — Error handling（⏱ 1h）
- [ ] CI 失敗時的處理
- [ ] 加 retry 機制
- [ ] 想辦法在失敗時通知自己

### Day 39 — 觀察隔天執行（⏱ 1h）
- [ ] 設定昨晚 21:30 UTC
- [ ] 今早起來看有沒有跑
- [ ] 收集 log 數據

### Day 40 — v1 Phase 完成！🎉（⏱ 1h）
- [ ] 補齊文件
- [ ] 更新 README progress badge
- [ ] LinkedIn 第一篇分享
- [ ] **慶祝！**

**🏁 v1 Phase 完成標誌：**
- [ ] 連續 3 天自動推送成功
- [ ] CI 全綠
- [ ] 朋友能訂閱（加進 subscribers.json）

---

## 📅 Week 9-10：Eval Framework（Day 41-50）⭐ 最重要

> **本週目標：** 有客觀數據證明設計有效，準備求職材料

### Day 41 — 學什麼是 Eval（⏱ 1h）
- [ ] 讀 Anthropic blog: "Evaluating LLM applications"
- [ ] 理解 LLM-as-judge 概念
- [ ] 寫 notes

### Day 42 — 設計 test cases（⏱ 1h）
- [ ] 設計 10 組「股票資料 + 期望分析品質」
- [ ] 涵蓋：漲、跌、橫盤、新聞多、新聞少
- [ ] 存成 `evals/test_cases.json`

### Day 43 — eval_runner.py（⏱ 1h）
- [ ] 寫跑 eval 的主程式
- [ ] 對每個 case 跑 conservative + aggressive
- [ ] **從 LangSmith 撈歷史 traces 當作 eval 輸入**
- [ ] 存結果

### Day 44 — LLM-as-judge skill（⏱ 1h）
- [ ] 寫 `evals/judges/quality_judge.md`
- [ ] 定義評分維度（準確性、有用性、風險辨識）
- [ ] 跑判分

### Day 45 — 第一次 eval（⏱ 1h）
- [ ] 跑完整 eval
- [ ] 看數據：哪個 skill 表現好？
- [ ] **這就是你的「故事」**

### 🎯 週末（⏱ 2h）
- [ ] 整理數據成圖表
- [ ] 拍照、截圖、做 markdown report

### Day 46 — 開始寫文章（⏱ 1h）
- [ ] 標題：「How I Used deepagents + Harness Engineering to Build a Stock AI Agent」
- [ ] 副標：「A framework where the model is replaceable but the architecture isn't」
- [ ] outline 完成

### Day 47 — Draft（⏱ 1h）
- [ ] 寫 1500-2000 字 draft
- [ ] 插入程式碼範例
- [ ] 插入 eval 數據與 LangSmith 截圖

### Day 48 — 潤稿（⏱ 1h）
- [ ] 找朋友 review
- [ ] 改錯字、改架構
- [ ] 加 hero image

### Day 49 — 發布（⏱ 1h）
- [ ] 發到 dev.to / Medium
- [ ] LinkedIn 分享
- [ ] Twitter / Threads 分享
- [ ] **觀察反應**

### Day 50 — 收集回饋（⏱ 1h）
- [ ] 看留言、看 stars
- [ ] 改進專案
- [ ] 寫第二篇 outline

**🏁 Week 9-10 完成標誌：**
- [ ] Eval framework 跑得起來
- [ ] 第一篇文章發布
- [ ] LinkedIn 開始有人看

---

## 📅 Week 11-12：Observability + 求職啟動（Day 51-60）

> **本週目標：** 可講出具體數字 + 第一份履歷投出去

### Day 51 — LangSmith 數據盤點（⏱ 1h）
- [ ] 登入 LangSmith dashboard
- [ ] 確認 Week 3-4 起的所有 trace 都有記錄
- [ ] 規劃要展示的指標：token / latency / cost / tool call 次數

### Day 52 — 數據分析腳本（⏱ 1h）
- [ ] 用 LangSmith Python SDK 撈歷史數據
- [ ] 計算：conservative vs aggressive 的 token 差異
- [ ] 計算：gemini_harness 前後的 token 節省量

### Day 53 — 整合 LangSmith 到 CI（⏱ 1h）
- [ ] 確認 GitHub Actions 每次執行都有寫入 LangSmith
- [ ] 確認 tag / metadata（subscriber skill, date）有正確記錄

### Day 54 — 算數據（⏱ 1h）
- [ ] 從 LangSmith 撈 7 天的數據
- [ ] 算：「gemini_harness 省了 X% token」「平均 latency Y ms」「每月 Z 元」
- [ ] **這些數字會進履歷！**

### Day 55 — 第二篇文章（⏱ 1h）
- [ ] 標題：「Harness Engineering in Practice: Building Observable AI Agents with deepagents + LangSmith」
- [ ] outline + draft

### 🎯 週末（⏱ 2h）
- [ ] 更新履歷
- [ ] 更新 LinkedIn About
- [ ] 列出目標公司清單（包含台積電）

### Day 56 — 找目標（⏱ 1h）
- [ ] 列 5-10 家目標公司
- [ ] 看 JD，圈出能對應的技能

### Day 57 — 第一份履歷（⏱ 1h）
- [ ] 投第一份
- [ ] 客製化 cover letter

### Day 58 — 面試準備（⏱ 1h）
- [ ] 準備 90 秒自我介紹
- [ ] 練習講 Ka-ching! v1 的故事

### Day 59 — 緩衝 / 優化（⏱ 1h）
- [ ] 補進度
- [ ] 改履歷

### Day 60 — v1 完整完成 🎊（⏱ 1h）
- [ ] 寫總結文
- [ ] 統計：commits 數、文章瀏覽數、stars
- [ ] **準備開始 v2！**

**🏁 v1 完整完成標誌：**
- [ ] 完整 eval 數據
- [ ] 完整 LangSmith trace 數據
- [ ] 2 篇文章發布
- [ ] 履歷至少投 1 份

---

# 📱 Phase 2：v2 — 手機 Chat Portal（Day 61-100）

**目標：** 把 v1 後端升級為 FastAPI，建立 React Native Chat App
**Definition of Done：** 真實手機可安裝，能對話並追問
**前提：** v1 完成（deepagents + Claude 可正常執行）

---

## 📅 Week 13-14：FastAPI + SSE 升級（Day 61-70）

> **本週目標：** 後端從排程觸發升級為即時 API，支援 SSE streaming

### Day 61 — FastAPI 環境設定（⏱ 1h）
- [ ] `uv add fastapi uvicorn`
- [ ] 建立 `interfaces/api.py`
- [ ] 最簡單的 `GET /health` 跑通
- [ ] commit：`feat(api): add FastAPI server scaffold`

### Day 62 — 設計 API 路由（⏱ 1h）
- [ ] 規劃 endpoint：
  - `POST /analyze`：觸發分析，回傳 SSE stream
  - `GET /subscribers`：查詢訂閱者
  - `POST /chat`：對話式追問
- [ ] 寫 request / response schema（Pydantic）
- [ ] commit：`feat(api): define route schemas`

### Day 63 — SSE streaming 實作（⏱ 1h）
- [ ] 學 FastAPI `StreamingResponse` + `EventSourceResponse`
- [ ] 把 `stock_agent.invoke()` 的輸出改為 streaming
- [ ] 用 curl 測試：`curl -N http://localhost:8000/analyze`
- [ ] commit：`feat(api): add SSE streaming endpoint`

### Day 64 — deepagents streaming 整合（⏱ 1h）
- [ ] deepagents 支援 async streaming
- [ ] 確認每個 token 都能即時輸出
- [ ] LangSmith 確認 streaming trace 正確記錄
- [ ] commit：`feat(agent): enable streaming in stock_agent`

### Day 65 — CORS + 安全設定（⏱ 1h）
- [ ] 加 CORS middleware（允許 React Native 連線）
- [ ] API Key 驗證（Header: `X-API-Key`）
- [ ] commit：`feat(api): add CORS and API key auth`

### 🎯 週末（⏱ 1-2h）
- [ ] 寫 `docs/ADR/004-fastapi-sse-upgrade.md`
- [ ] 寫 `docs/learning-notes/unit-06-sse-streaming.md`

### Day 66 — 對話歷史管理（⏱ 1h）
- [ ] 設計 conversation_id 機制
- [ ] 多輪對話：把歷史訊息傳給 Agent
- [ ] commit：`feat(api): add conversation history`

### Day 67 — subscribers API（⏱ 1h）
- [ ] `GET /subscribers/{user_id}/stocks`
- [ ] `PATCH /subscribers/{user_id}`
- [ ] commit：`feat(api): add subscribers endpoints`

### Day 68 — 整合測試（⏱ 1h）
- [ ] 寫 `test_api.py`
- [ ] `uv run pytest` 全過
- [ ] commit：`test: add FastAPI integration tests`

### Day 69 — 緩衝日（⏱ 1h）
- [ ] 補進度

### Day 70 — Docker 化（⏱ 1h）
- [ ] 寫 `Dockerfile`
- [ ] 確認 container 可以跑起來
- [ ] commit：`chore: add Dockerfile for API server`

**🏁 Week 13-14 完成標誌：**
```bash
uv run uvicorn interfaces.api:app --reload
curl -N -H "X-API-Key: test" -X POST http://localhost:8000/analyze \
  -d '{"stocks": ["AAPL"], "skill": "conservative"}'
# 看到 SSE token 逐字輸出 ✅
```

---

## 📅 Week 15-16：React Native Chat UI（Day 71-80）

> **本週目標：** 手機畫面能顯示 streaming 對話

### Day 71 — React Native 環境（⏱ 1h）
- [ ] 安裝 Node.js + Expo CLI
- [ ] `npx create-expo-app ka-ching-v2 --template blank-typescript`
- [ ] 在模擬器跑起來
- [ ] commit：`chore: init React Native with Expo`

### Day 72 — 專案結構設計（⏱ 1h）
- [ ] 建立資料夾結構（**hooks / api / components 設計成 v3 可複用**）：
  ```
  src/
  ├── components/         ← v3 複用
  │   ├── ChatWindow.tsx
  │   ├── MessageBubble.tsx
  │   └── TypingIndicator.tsx
  ├── hooks/              ← v3 複用
  │   ├── useSSEStream.ts
  │   └── useChat.ts
  ├── api/                ← v3 複用
  │   └── client.ts
  └── screens/
      └── ChatScreen.tsx
  ```
- [ ] commit：`chore: setup RN project structure`

### Day 73 — MessageBubble 元件（⏱ 1h）
- [ ] 使用者訊息（右側）、Agent 訊息（左側）
- [ ] Streaming 中的打字動畫
- [ ] commit：`feat(ui): add MessageBubble component`

### Day 74 — ChatWindow 元件（⏱ 1h）
- [ ] 訊息列表（FlatList）+ 捲動到最新
- [ ] 輸入框 + 送出按鈕
- [ ] commit：`feat(ui): add ChatWindow component`

### Day 75 — useSSEStream hook（⏱ 1h）
- [ ] 連接後端 SSE endpoint
- [ ] 逐 token 更新 message state
- [ ] 連線中斷時自動重連
- [ ] commit：`feat(hooks): add useSSEStream`

### 🎯 週末（⏱ 1-2h）
- [ ] 錄第一個 Demo 影片（本地後端 + 模擬器）
- [ ] 截圖留存

### Day 76 — useChat hook（⏱ 1h）
- [ ] 對話歷史狀態管理
- [ ] 送出訊息 → 呼叫後端 → 接收 SSE
- [ ] commit：`feat(hooks): add useChat`

### Day 77 — API client（⏱ 1h）
- [ ] 封裝 `/analyze`、`/chat` 呼叫
- [ ] 環境變數管理
- [ ] commit：`feat(api): add API client`

### Day 78 — TypingIndicator（⏱ 1h）
- [ ] Agent 思考中動畫
- [ ] Tool call 執行時顯示「正在查詢資料...」
- [ ] commit：`feat(ui): add TypingIndicator`

### Day 79 — 整合測試（⏱ 1h）
- [ ] 模擬器連本地後端完整跑通
- [ ] 測試多輪對話（追問）
- [ ] LangSmith 確認有記錄

### Day 80 — 學習筆記 + ADR（⏱ 1h）
- [ ] `docs/ADR/005-react-native-v2.md`
- [ ] `docs/learning-notes/unit-07-rn-sse.md`

**🏁 Week 15-16 完成標誌：**
```
模擬器：輸入「NVDA 今天怎麼了？」
       → 看到 Agent 逐字回應 ✅
       → 追問「那 AAPL 呢？」 ✅
```

---

## 📅 Week 17-18：訂閱設定 + 發布準備（Day 81-100）

> **本週目標：** 用戶能管理股票清單，App 可安裝到真實手機

### Day 81 — 導航架構（⏱ 1h）
- [ ] 安裝 `react-navigation`
- [ ] Chat / Settings / History 三個畫面
- [ ] commit：`feat(nav): add navigation structure`

### Day 82 — Settings 畫面（⏱ 1h）
- [ ] 股票清單管理 + Skill 切換
- [ ] commit：`feat(screens): add Settings screen`

### Day 83 — 本地儲存（⏱ 1h）
- [ ] `AsyncStorage` 儲存用戶設定
- [ ] commit：`feat(storage): add local settings persistence`

### Day 84 — History 畫面（⏱ 1h）
- [ ] 查看歷史對話
- [ ] commit：`feat(screens): add History screen`

### Day 85 — 股票快捷按鈕（⏱ 1h）
- [ ] Chat 畫面顯示訂閱股票快捷鍵
- [ ] commit：`feat(ui): add stock shortcut buttons`

### 🎯 週末（⏱ 1-2h）
- [ ] 完整 Demo 錄影（設定 → 切 skill → 分析 → 追問）

### Day 86 — Push 通知（⏱ 1h）
- [ ] Expo Push Notifications 設定
- [ ] v1 推播完成時通知 v2
- [ ] commit：`feat(notify): add push notification from v1`

### Day 87-88 — UI 打磨（⏱ 各 1h）
- [ ] 暗色模式、時間戳、載入動畫
- [ ] 網路斷線提示、重試按鈕

### Day 89 — 後端部署（⏱ 1h）
- [ ] 部署 FastAPI 到 Render / Railway（免費方案）
- [ ] 確認手機可連到遠端後端

### Day 90 — TestFlight / APK（⏱ 1h）
- [ ] Expo EAS Build
- [ ] 產出 iOS TestFlight 或 Android APK
- [ ] 安裝到真實手機

### Day 91-95 — 緩衝 + 優化（⏱ 各 1h）
- [ ] 補進度、UI 細節優化

### Day 96 — Demo 腳本（⏱ 1h）
- [ ] 準備面試 Demo 腳本（v1 推播 + v2 Chat）

### Day 97 — 更新 README（⏱ 1h）
- [ ] 加 v2 截圖 + Demo 影片連結

### Day 98 — ADR 整理（⏱ 1h）
- [ ] `docs/ADR/006-v2-architecture.md`
- [ ] 更新履歷中的 Ka-ching 項目

### Day 99 — 緩衝日（⏱ 1h）
- [ ] 補進度

### Day 100 — v2 完成 🎊（⏱ 1h）
- [ ] 統計 v1 → v2 後端複用率
- [ ] LinkedIn 發文
- [ ] **準備開始 v3！**

**🏁 v2 完成標誌：**
- [ ] 真實手機可安裝執行
- [ ] 後端部署到雲端
- [ ] Demo 影片錄製完成
- [ ] LangSmith 有 v2 的 trace

---

# 🖥 Phase 3：v3 — 桌面分析工作站（Day 101-140）

**目標：** Tauri 桌面 App，整合股票圖表 + LangGraph Sub-agent
**Definition of Done：** macOS .dmg 可安裝，Portfolio 分析可執行
**前提：** v2 完成（FastAPI + React 元件可複用）

---

## 📅 Week 21-22：Tauri + Chat 移植（Day 101-110）

> **本週目標：** 把 v2 的 Chat 功能搬到桌面視窗

### Day 101 — Tauri 環境設定（⏱ 1h）
- [ ] 確認 macOS 開發環境：Xcode Command Line Tools
- [ ] 安裝 Rust：`curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh`
- [ ] `npm create tauri-app ka-ching-v3 -- --template react-ts`
- [ ] commit：`chore: init Tauri + React project`

### Day 102 — 專案結構 + 複製 v2 元件（⏱ 1h）
- [ ] 複製 v2 的 `ChatWindow.tsx`、`MessageBubble.tsx`、`TypingIndicator.tsx`
- [ ] 複製 v2 的 `useSSEStream.ts`、`useChat.ts`、`api/client.ts`
- [ ] 調整 import 路徑（React Native → React Web）
- [ ] commit：`feat: migrate v2 components to v3`

### Day 103 — Tauri 視窗設定（⏱ 1h）
- [ ] 設定視窗大小（1280 × 800）
- [ ] macOS menu bar 設定
- [ ] commit：`chore: configure Tauri window settings`

### Day 104 — 連接 FastAPI 後端（⏱ 1h）
- [ ] v3 連接 v2 的 FastAPI（localhost:8000）
- [ ] 確認 SSE streaming 在 React Web 正常運作
- [ ] commit：`feat: connect v3 to FastAPI backend`

### Day 105 — Python sidecar 設定（⏱ 1h）
- [ ] 了解 Tauri sidecar 機制
- [ ] 把 FastAPI server 打包為 sidecar binary（離線也能用）
- [ ] commit：`feat(sidecar): embed Python backend in Tauri`

### 🎯 週末（⏱ 1-2h）
- [ ] 寫 `docs/ADR/007-tauri-vs-electron.md`
- [ ] 截圖桌面版 Chat

### Day 106 — 側邊欄導航（⏱ 1h）
- [ ] 左側 sidebar：Chat / Charts / Portfolio
- [ ] commit：`feat(ui): add sidebar navigation`

### Day 107-108 — 測試 + 學習筆記（⏱ 各 1h）
- [ ] Chat 功能完整測試
- [ ] `docs/learning-notes/unit-08-tauri.md`

### Day 109-110 — 緩衝（⏱ 各 1h）

**🏁 Week 21-22 完成標誌：**
```
桌面 App 開啟 → Chat 頁面 → 輸入「分析 NVDA」
→ 看到 SSE streaming 回應 ✅（功能同 v2，平台換成桌面）
```

---

## 📅 Week 23-24：股票圖表 + 多股票比較（Day 111-120）

> **本週目標：** 能看 K 線圖，能同時比較多檔股票

### Day 111 — Recharts 安裝與探索（⏱ 1h）
- [ ] `npm install recharts`
- [ ] 寫簡單的 LineChart

### Day 112 — 股價走勢圖（⏱ 1h）
- [ ] `StockLineChart.tsx`：過去 30 天收盤價
- [ ] tooltip（滑鼠移入顯示日期 + 價格）
- [ ] commit：`feat(charts): add StockLineChart`

### Day 113 — 技術指標圖（⏱ 1h）
- [ ] 疊加 MA20 / MA50
- [ ] RSI 子圖
- [ ] commit：`feat(charts): add technical indicators`

### Day 114 — Charts 頁面（⏱ 1h）
- [ ] 輸入股票代碼 → 顯示圖表 + 最新分析報告
- [ ] commit：`feat(pages): add ChartPage`

### Day 115 — 多股票比較（⏱ 1h）
- [ ] 選多檔股票 → 同一張圖比較漲跌幅
- [ ] commit：`feat(charts): add multi-stock comparison`

### 🎯 週末（⏱ 1-2h）
- [ ] 錄製圖表 Demo 影片
- [ ] 更新 README

### Day 116 — Chat + 圖表聯動（⏱ 1h）
- [ ] Chat 中提到股票代碼 → 自動顯示圖表
- [ ] commit：`feat(ui): add chart-chat linkage`

### Day 117 — 圖表互動（⏱ 1h）
- [ ] 點圖表某個日期 → Chat 自動追問「那天發生了什麼？」
- [ ] commit：`feat(ui): add chart-to-chat interaction`

### Day 118-120 — 測試 + 學習筆記（⏱ 各 1h）
- [ ] `docs/learning-notes/unit-09-recharts.md`
- [ ] `docs/ADR/008-chart-chat-linkage.md`

**🏁 Week 23-24 完成標誌：**
```
Charts 頁面 → 輸入 NVDA, AAPL
→ 看到走勢比較圖 ✅
→ 點圖表某天 → Chat 自動追問 ✅
```

---

## 📅 Week 25-26：LangGraph Sub-agent（Day 121-130）

> **本週目標：** 用 LangGraph 支援複雜多任務工作流程（Portfolio 分析）

### Day 121 — LangGraph 概念學習（⏱ 1h）
- [ ] 讀 LangGraph 文件：Graph / Node / Edge
- [ ] 理解與 deepagents 的差異：
  - deepagents：單一 Agent + tools
  - LangGraph：多 Node（多 Agent）+ 狀態機
- [ ] 寫 Hello World Graph
- [ ] commit：`chore: LangGraph exploration`

### Day 122 — 設計 Portfolio Graph（⏱ 1h）
- [ ] 畫出 Graph 結構：
  ```
  START → 分派 Node → Sub-agent × 3（平行）→ 彙整 Node → 報告 Node → END
  ```
- [ ] commit：`docs: design portfolio analysis graph`

### Day 123 — 分派 Node（⏱ 1h）
- [ ] 寫 `agent/portfolio_graph.py`
- [ ] `dispatch_node`：把股票清單分批
- [ ] commit：`feat(graph): add dispatch node`

### Day 124 — Sub-agent Node（⏱ 1h）
- [ ] 每個 Sub-agent 是一個 deepagents Agent（複用！）
- [ ] 平行執行（`asyncio.gather`）
- [ ] commit：`feat(graph): add sub-agent nodes`

### Day 125 — 彙整 + 報告 Node（⏱ 1h）
- [ ] `merge_node` + `report_node`
- [ ] commit：`feat(graph): add merge and report nodes`

### 🎯 週末（⏱ 1-2h）
- [ ] 測試 Graph：10 檔股票 → 彙整報告
- [ ] LangSmith 看 Graph 執行流程截圖

### Day 126 — Portfolio 頁面（⏱ 1h）
- [ ] `PortfolioPage.tsx`：持倉清單 + 分析按鈕
- [ ] commit：`feat(pages): add PortfolioPage`

### Day 127 — 損益計算（⏱ 1h）
- [ ] 即時損益 + 投資組合總值圖表
- [ ] commit：`feat(portfolio): add P&L calculation`

### Day 128 — Human-in-the-loop（⏱ 1h）
- [ ] LangGraph interrupt：執行中暫停讓使用者確認
- [ ] commit：`feat(graph): add human-in-the-loop checkpoint`

### Day 129-130 — 測試 + ADR（⏱ 各 1h）
- [ ] `docs/ADR/009-langgraph-subagent.md`
- [ ] `docs/learning-notes/unit-10-langgraph.md`

**🏁 Week 25-26 完成標誌：**
```python
result = await run_portfolio_analysis(
    stocks=["AAPL","NVDA","TSLA","MSFT","GOOGL","AMZN","META","BRK.B","V","JPM"],
    skill="aggressive"
)
# Sub-agents 平行分析 ✅  LangSmith 顯示完整 Graph ✅
```

---

## 📅 Week 27-28：報告匯出 + 最終 Demo（Day 131-140）

> **本週目標：** .dmg 可安裝，Demo 影片完成，面試材料就緒

### Day 131 — 報告匯出：Markdown（⏱ 1h）
- [ ] 分析報告存成 `.md` 檔（Tauri fs API）
- [ ] commit：`feat(export): add Markdown export`

### Day 132 — 報告匯出：PDF（⏱ 1h）
- [ ] `@react-pdf/renderer` 生成 PDF（含圖表截圖）
- [ ] commit：`feat(export): add PDF export`

### Day 133 — 鍵盤快捷鍵（⏱ 1h）
- [ ] `Cmd+K`：快速輸入框
- [ ] `Cmd+N`：新對話
- [ ] `Cmd+E`：匯出報告
- [ ] commit：`feat(ui): add keyboard shortcuts`

### Day 134 — Tauri 打包（⏱ 1h）
- [ ] `npm run tauri build`
- [ ] 產出 `.dmg`（macOS）
- [ ] 確認可安裝到乾淨的 Mac

### Day 135 — Demo 腳本設計（⏱ 1h）
- [ ] 設計 5 分鐘完整 Demo：
  1. v1：Line Bot 推播截圖
  2. v2：手機 Chat 追問（真機錄影）
  3. v3：桌面圖表 + Portfolio + LangGraph
  4. LangSmith：完整 observability
- [ ] **這是台積電面試的主要素材**

### 🎯 週末（⏱ 2h）
- [ ] 錄製完整 Demo 影片（5 分鐘）
- [ ] 更新 GitHub README（v3 截圖 + Demo 影片連結）
- [ ] 更新履歷（加入 v3 量化數字）

### Day 136 — 面試問題準備（⏱ 1h）
- [ ] 準備 3 個架構問題答案：
  1. 「為什麼 v1/v2/v3 用同一個後端？」
  2. 「deepagents vs LangGraph 你怎麼選？」
  3. 「如果台積電要內部部署，你會怎麼改？」

### Day 137 — 效能比較（⏱ 1h）
- [ ] 統計 v3 App 大小 vs Electron 同等 App
- [ ] 記憶體使用比較
- [ ] **這個數據在面試可以講**

### Day 138 — 文件整理（⏱ 1h）
- [ ] 更新所有 ADR
- [ ] `CHANGELOG.md`
- [ ] 安裝說明

### Day 139 — 緩衝日（⏱ 1h）
- [ ] 補進度
- [ ] 最後優化

### Day 140 — Ka-ching 三部曲完成 🎊（⏱ 1h）
- [ ] 統計：v1/v2/v3 共用程式碼比例
- [ ] LinkedIn 發文（附 Demo 影片）
- [ ] **你完成了！**

**🏁 v3 完成標誌：**
- [ ] macOS .dmg 可安裝
- [ ] Portfolio 分析（LangGraph Sub-agent）可執行
- [ ] 報告可匯出 PDF
- [ ] Demo 影片錄製完成

---

## 📊 進度追蹤

```yaml
v1_phase1_mvp:
  days: "1-40"
  total: 40
  completed: 2
  in_progress: 1

v1_phase1_5_career:
  days: "41-60"
  total: 20
  completed: 0

v2_backend_upgrade:
  days: "61-70"
  total: 10
  completed: 0

v2_chat_ui:
  days: "71-80"
  total: 10
  completed: 0

v2_settings_polish:
  days: "81-100"
  total: 20
  completed: 0

v3_tauri_setup:
  days: "101-110"
  total: 10
  completed: 0

v3_charts:
  days: "111-120"
  total: 10
  completed: 0

v3_langgraph:
  days: "121-130"
  total: 10
  completed: 0

v3_polish:
  days: "131-140"
  total: 10
  completed: 0

articles:
  - title: "How I Used deepagents + Harness Engineering to Build a Stock AI Agent"
    status: planned
    target_date: Day 49
  - title: "Harness Engineering in Practice: Building Observable AI Agents with deepagents + LangSmith"
    status: planned
    target_date: Day 55

adrs:
  - "ADR-001: Why yfinance"
  - "ADR-002: Dual AI engine (Gemini + Claude)"
  - "ADR-003: Why deepagents over raw LLM calls"
  - "ADR-004: FastAPI + SSE upgrade"
  - "ADR-005: React Native for v2"
  - "ADR-006: v2 architecture"
  - "ADR-007: Tauri vs Electron"
  - "ADR-008: Chart-Chat linkage"
  - "ADR-009: LangGraph Sub-agent"

components_reuse_map:
  v1_to_v2:
    - agent/stock_agent.py   → 後端直接複用
    - agent/fetcher.py       → 後端直接複用
    - skills/*.md            → 直接複用
  v2_to_v3:
    - useSSEStream.ts        → 直接複用
    - useChat.ts             → 直接複用
    - api/client.ts          → 直接複用
    - MessageBubble.tsx      → 直接複用
    - ChatWindow.tsx         → 直接複用

job_applications:
  count: 0
  target: 5 by Day 140
  priority: TSMC AI/ML Application Engineer
```

---

## 🚦 紅綠燈警示

🔴 **紅燈（必須處理）**
- 某個 task 卡超過 2 天
- 連續 3 天沒動專案
- 心情很差不想做了

🟡 **黃燈（需要注意）**
- 一週進度落後 30% 以上
- 開始覺得「為什麼要做這個」
- 跟其他事情衝突

🟢 **綠燈（持續前進）**
- 每天都有 commit
- 學習筆記持續更新
- 已經能講出設計理由

---

## 🎤 面試故事線（90 秒版本）

> 台積電面試時，這樣講 Ka-ching：

```
「Ka-ching 是我設計的美股 AI 分析系統，做了三個版本。

v1 是 GitHub Actions 定時跑的 Line Bot，
用 deepagents + Claude 分析股票，每天推播到手機。
核心是 Harness Engineering：模型可換，架構不動。

v2 我把後端升級成 FastAPI + SSE，
建了 React Native App，讓使用者主動追問——
同一個 deepagents 後端，多了一個互動入口。

v3 是 Tauri 桌面版，重點是引入 LangGraph：
一次分析 10 檔股票，Sub-agents 平行處理，
LangSmith 記錄完整的 Agent 決策過程。

整個系統的設計原則是：
後端從 v1 到 v3 完全共用，
前端元件從 v2 到 v3 直接複用，
這就是可維護性設計的體現。」
```

---

## 💡 給未來自己的話

當你想放棄時，記得：

> **完成 80% 的 Ka-ching! 比完美的 0% 強 1000 倍。**

當你進度超前時，記得：

> **緩衝日不是浪費。提前完成的時間花在重構與筆記，比衝下一個 task 有價值。**

當你進度落後時，記得：

> **7 個月不夠？那就 8 個月。重點是完成，不是趕時程。**

---

*Last updated: 2026-06-09*
*Built with Harness Engineering · 模型可換，Harness 不變*
*v1 Line Bot → v2 手機 App → v3 桌面工作站*
*Agent 框架：deepagents + LangGraph · 觀測：LangSmith · 套件管理：uv*
*錢錢來了哦 💰*