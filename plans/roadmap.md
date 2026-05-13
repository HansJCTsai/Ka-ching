# 🛤 Ka-ching! Roadmap

> 記錄當前 Phase 與待辦事項，agent 與人類都會讀此檔案。

---

## 📍 Current Phase: **Phase 0 — Project Setup**

剛完成 GitHub repo 建立與初始 commit。

---

## ✅ Phase 0：專案啟動

- [x] 設計專案架構與 Harness 哲學
- [x] 撰寫 AGENTS.md（AI 編碼代理操作契約）
- [x] 撰寫 README.md（人類入口）
- [x] 建立 GitHub repo
- [x] 初始 commit
- [ ] 設定 GitHub Actions Secrets
- [ ] 建立 Line Bot Channel

---

## 🚧 Phase 1：MVP — 每日推播

**目標：** 每天美股盤後自動推送個人化報告到 Line

### Setup
- [ ] 建立 `pyproject.toml` 與依賴
- [ ] 建立基礎資料夾結構（agent / interfaces / skills / tests）
- [ ] 設定 `.github/workflows/daily_kaching.yml`

### Core Modules
- [ ] `agent/fetcher.py` — yfinance 抓股價、newsapi 抓新聞
- [ ] `agent/analyzer.py` — Gemini 前處理 + Claude 分析
- [ ] `agent/notifier.py` — Line Messaging API 推送
- [ ] `tools/flex_formatter.py` — Flex Message 格式化

### Skills
- [ ] `skills/harness.md` — Gemini 前處理指令
- [ ] `skills/conservative.md` — Claude 保守風格
- [ ] `skills/aggressive.md` — Claude 積極風格

### Interface
- [ ] `interfaces/scheduler.py` — GitHub Actions 入口

### Tests
- [ ] `tests/test_fetcher.py`
- [ ] `tests/test_analyzer.py`
- [ ] `tests/test_notifier.py`

### Config
- [ ] `config.py` — 模型版本、參數
- [ ] `subscribers.json` — 訂閱者清單

---

## 🔜 Phase 2：擴充

- [ ] `agent/alert.py` — 跌破價格警報
- [ ] `optional-skills/alert.md` — 警報邏輯
- [ ] Line Bot 指令訂閱（多人加入）
- [ ] `optional-skills/multi-subscribe.md`

---

## 🔮 Phase 3：互動介面

- [ ] `interfaces/cli.py` — CLI 即時查詢
- [ ] `interfaces/web.py` — Line Bot Webhook
- [ ] 對話式調整訂閱設定

---

## 🌟 Phase 4：自我改善

- [ ] 推送結果分析
- [ ] Skill 改進建議產生
- [ ] 跨次執行記憶累積

---

## 🚀 Phase 5：SDK 化（長期目標）

啟動條件（至少滿足兩項）：
- [ ] 核心模組 6 個月未大改
- [ ] 完成至少一個非股票領域 Agent
- [ ] ≥ 3 個實際用戶
- [ ] 有人主動詢問「能不能用這套做 XXX」

工作項目：
- [ ] 抽象 `agent/` 為 `harness_agent` 套件
- [ ] 定義 Fetcher / Analyzer / Notifier base class
- [ ] 發布到 PyPI
- [ ] SDK quickstart 文件
- [ ] CLI 鷹架工具 `harness-agent init`

---

*Last updated: 2026-04-27*
