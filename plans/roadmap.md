# 🛤 Ka-ching! Roadmap

> 給人類與 AI 編碼代理共讀的執行路線圖。
> 配速：**每天 1 小時，週末 1-2 小時**，3 個月完成可求職作品。

---

## 📍 Current Status

```yaml
phase: 1 — MVP
week: 1 / 12
day: 1 / 60
last_updated: 2026-04-27
```

---

## 🎯 整體目標

```
Week 1-8  → Phase 1 MVP（能跑的 Agent）
Week 9-12 → Phase 1.5 求職強化（可量化作品）
Phase 2+  → MVP 上線後再規劃
```

**3 個月後產出：**
- ✅ 能自動跑的 LLM Agent
- ✅ 2 篇技術文章
- ✅ 可量化的 Eval 數據
- ✅ 完整作品集 → 開始投履歷

---

## ⏱ 時間預算

```
每天 1 小時（平日）  × 5 天 = 5h/週
週末加碼 1-2 小時             = 1-2h/週
─────────────────────────────────────
總計：每週 ~6 小時，12 週 ~72 小時
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
- [ ] 加 GitHub Topics 標籤
- [ ] About 區塊填寫
- [ ] 取得 Anthropic API Key（Phase 1 前需要）
- [ ] 取得 Gemini API Key（Phase 1 前需要）
- [ ] 申請 Line Developer 帳號（Phase 1 前需要）

---

# 🚧 Phase 1：MVP — 每日推播

**目標：** 每天美股盤後自動推送個人化報告到 Line
**Definition of Done：** 隔天早上手機自動收到 Ka-ching! 推送

---

## 📅 Week 1-2：環境與資料層（Day 1-10）

> **本週目標：** fetcher.py 完成，能抓股價與新聞

### Day 1 — Poetry 與專案骨架（⏱ 1h）
- [ ] 在本地 clone repo
- [ ] `poetry config virtualenvs.in-project true`
- [ ] 建立 `pyproject.toml`（範本已給）
- [ ] `poetry install` 跑通
- [ ] 建立資料夾骨架（`agent/`、`interfaces/`、`skills/` 等）
- [ ] 寫各模組的 `__init__.py`
- [ ] commit：`chore: setup Poetry and project scaffold`

### Day 2 — yfinance REPL 探索（⏱ 1h）
- [ ] 在 `poetry shell` 裡玩 yfinance
- [ ] `yf.Ticker("AAPL").history(period="2d")` 看回傳結構
- [ ] 記錄你看到什麼欄位（成交量、開盤、收盤⋯）
- [ ] 不寫程式，純探索

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
- [ ] `poetry run pytest` 通過
- [ ] commit：`test: add fetcher tests with mocks`

### 🎯 週末（⏱ 1-2h）
- [ ] 寫 `docs/learning-notes/unit-01-poetry.md`
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
poetry run python -c "from agent.fetcher import get_stock_data; print(get_stock_data(['AAPL']))"
# 應該印出股價資料
```

---

## 📅 Week 3-4：AI 雙引擎（Day 11-20）

> **本週目標：** 跑出第一次 AI 分析，理解 Skill 系統

### Day 11 — Anthropic API 起手式（⏱ 1h）
- [ ] 申請 Anthropic API key（如果還沒）
- [ ] 設定 `.env`（記得加 .gitignore）
- [ ] 第一次呼叫 Claude API
- [ ] 觀察回傳格式

### Day 12 — Claude SDK 深度探索（⏱ 1h）
- [ ] 試不同 `system` prompt
- [ ] 試不同 `temperature`
- [ ] 觀察 token 使用量
- [ ] 算成本（每次大概多少錢）

### Day 13 — 寫 conservative.md（⏱ 1h）
- [ ] 設計保守風格的 prompt
- [ ] 加入 When / Instructions / Output / Examples 四段
- [ ] commit：`skill(conservative): initial conservative analysis style`

### Day 14 — 寫 aggressive.md（⏱ 1h）
- [ ] 設計積極風格的 prompt
- [ ] 兩個 skill 比較差異
- [ ] commit：`skill(aggressive): initial aggressive analysis style`

### Day 15 — analyzer.py Claude 部分（⏱ 1h）
- [ ] 寫 `claude_analyze(data, skill)`
- [ ] 載入 skill 從 `skills/*.md`
- [ ] 完全不要硬編碼 prompt
- [ ] commit：`feat(agent): add claude_analyze`

### 🎯 週末（⏱ 1-2h）
- [ ] A/B 測試：同一份資料、兩個 skill、看差別
- [ ] 拍照記錄結果（之後寫文章會用到）
- [ ] 寫學習筆記

### Day 16 — Gemini API 起手式（⏱ 1h）
- [ ] 申請 Gemini API key
- [ ] 第一次呼叫 Gemini
- [ ] 比較 Gemini vs Claude 的回應風格

### Day 17 — 寫 harness.md（⏱ 1h）
- [ ] 設計前處理 prompt
- [ ] 重點：「過濾、結構化」
- [ ] commit：`skill(harness): initial preprocessing skill`

### Day 18 — analyzer.py Gemini 部分（⏱ 1h）
- [ ] 寫 `gemini_harness(raw_data)`
- [ ] 串接 Gemini → Claude
- [ ] commit：`feat(agent): add gemini_harness preprocessing`

### Day 19 — 整合雙引擎 + Token 統計（⏱ 1h）
- [ ] 寫 `analyze_for_subscriber(subscriber, data)`
- [ ] 印出每次 token 使用量
- [ ] 算雙引擎 vs 單 Claude 省了多少 token
- [ ] **這數據之後寫文章會用！**

### Day 20 — 測試 + 學習筆記（⏱ 1h）
- [ ] 寫 `test_analyzer.py`（mock LLM SDK）
- [ ] `docs/learning-notes/unit-03-skills.md`
- [ ] `docs/ADR/002-dual-ai-engine.md`

**🏁 Week 3-4 完成標誌：**
```python
from agent.fetcher import get_stock_data
from agent.analyzer import analyze_for_subscriber

data = {"stocks": get_stock_data(["NVDA"])}
report = analyze_for_subscriber({"skill": "conservative"}, data)
print(report)  # 印出 AI 寫的中文分析
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
- [ ] 開啟 Webhook（之後 Phase 3 用）

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
- [ ] fetcher → analyzer → notifier 跑完整流程
- [ ] 在本地手動觸發
- [ ] 收到漂亮的 Flex Message 🎉

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
poetry run python -m interfaces.scheduler
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
- [ ] Settings → Secrets → 加 5 個 keys
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

### Day 40 — Phase 1 完成！🎉（⏱ 1h）
- [ ] 補齊文件
- [ ] 更新 README progress badge
- [ ] LinkedIn 第一篇分享
- [ ] **慶祝！**

**🏁 Phase 1 完成標誌：**
- [ ] 連續 3 天自動推送成功
- [ ] CI 全綠
- [ ] 朋友能訂閱（加進 subscribers.json）

---

# 🌟 Phase 1.5：求職強化（Week 9-12）

**目標：** 把 MVP 升級成求職作品
**Definition of Done：** 可量化數據 + 2 篇文章 + 開始投履歷

---

## 📅 Week 9-10：Eval Framework（Day 41-50）⭐ 最重要

> **本週目標：** 有客觀數據證明你的設計有效

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
- [ ] 標題：「Why I Refused LangChain When Building My AI Agent」
- [ ] outline 完成

### Day 47 — Draft（⏱ 1h）
- [ ] 寫 1500-2000 字 draft
- [ ] 插入程式碼範例
- [ ] 插入 eval 數據

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

> **本週目標：** 可講出具體數字 + 履歷投出去

### Day 51 — 設計 tracing（⏱ 1h）
- [ ] 規劃要記錄什麼（prompt / latency / cost / tokens）
- [ ] 設計 JSON schema

### Day 52 — tracer.py（⏱ 1h）
- [ ] 寫 `agent/tracer.py`
- [ ] decorator 包裝 LLM 呼叫

### Day 53 — 整合 tracer（⏱ 1h）
- [ ] 在 analyzer 加上 tracing
- [ ] 確認 log 有產生

### Day 54 — 算數據（⏱ 1h）
- [ ] 跑 7 天的資料
- [ ] 算：「省了 X% token」「latency Y ms」「每月 Z 元」
- [ ] **這些數字會進履歷！**

### Day 55 — 第二篇文章（⏱ 1h）
- [ ] 標題：「Harness Engineering in Practice: Lessons from Building Ka-ching!」
- [ ] outline + draft

### 🎯 週末（⏱ 2h）
- [ ] 更新履歷（用之前給的範本）
- [ ] 更新 LinkedIn About
- [ ] 列出目標公司清單

### Day 56 — 找目標（⏱ 1h）
- [ ] 列 5-10 家目標公司
- [ ] 看 JD，圈出能對應的技能

### Day 57 — 第一份履歷（⏱ 1h）
- [ ] 投第一份
- [ ] 客製化 cover letter

### Day 58 — 面試準備（⏱ 1h）
- [ ] 準備 90 秒自我介紹
- [ ] 練習講 Ka-ching! 的故事

### Day 59 — 緩衝 / 優化（⏱ 1h）
- [ ] 補進度
- [ ] 改履歷

### Day 60 — 3 個月里程碑慶祝 🎊（⏱ 1h）
- [ ] 寫總結文（給自己看）
- [ ] 統計：commits 數、文章瀏覽數、stars
- [ ] **你完成了！**

**🏁 Phase 1.5 完成標誌：**
- [ ] 完整 eval 數據
- [ ] 完整 trace 數據
- [ ] 2 篇文章發布
- [ ] 履歷至少投 1 份

---

# 🔮 Phase 2-5（MVP 上線後再詳細規劃）

> 完成 Phase 1 + Phase 1.5 後，根據實際使用情況再規劃。
> **不要過早規劃**，先把 Phase 1 做完。

### Phase 2：擴充
- 跌破價格警報
- Line Bot 指令訂閱
- 多人訂閱管理

### Phase 3：互動介面
- CLI: `kaching "NVDA 怎麼樣？"`
- Web Chat / Line Bot Webhook

### Phase 4：自我改善
- 回饋層分析
- Skill 自動建議

### Phase 5：SDK 化
- `harness-agent` 套件
- PyPI 發布

---

## 📊 進度追蹤

```yaml
phase_1_mvp:
  total_days: 40
  completed: 0
  in_progress: 1
  
phase_1_5_career:
  total_days: 20
  completed: 0
  
articles:
  - title: "Why I Refused LangChain..."
    status: planned
    target_date: Day 49
  - title: "Harness Engineering in Practice"
    status: planned
    target_date: Day 55

job_applications:
  - count: 0
  - target: 5 by Day 60
```

---

## 🚦 紅綠燈警示

當你看到這些訊號，**停下來找 Claude 討論**：

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

## 💡 給未來自己的話

當你想放棄時，記得：

> **完成 80% 的 Ka-ching! 比完美的 0% 強 1000 倍。**

當你進度超前時，記得：

> **緩衝日不是浪費。提前完成的時間花在重構與筆記，比衝下一個 task 有價值。**

當你進度落後時，記得：

> **3 個月不夠？那就 4 個月。重點是完成，不是趕時程。**

---

*Last updated: 2026-04-27*
*Built with Harness Engineering · 模型可換，Harness 不變*
*錢錢來了哦 💰*
