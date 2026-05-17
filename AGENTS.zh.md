# AGENTS.zh.md

> **Ka-ching!** 🤑 — 美股每日 AI 分析與 Line 推送系統
> *Your money is talking. 錢錢來了哦 💰*
>
> 採 Harness Engineering 哲學，模型可換、Harness 不變
>
> 🌐 語言版本：[中文版](AGENTS.zh.md) · [English](AGENTS.en.md)
>
> ⚠️ 此檔案為 AI 編碼代理（Claude Code / Cursor / Codex / Copilot）的操作指南。人類請看 `README.md`。

---

## 🤖 Agent 啟動規則

開始任何任務前，**必須**依序完成：

1. 讀取此檔案完整內容
2. 檢查 `plans/roadmap.md` 確認目前所在 Phase
3. 檢查 `skills/` 與 `optional-skills/` 目錄結構
4. 檢查 `.env.example` 確認所需環境變數
5. 違反「禁止事項」區的任何規則前，必須先停下來向使用者確認

---

## 📋 專案速覽

```yaml
name: kaching
display_name: "Ka-ching! 🤑"
tagline: "Your money is talking. 錢錢來了哦 💰"
language: Python 3.11+
package_manager: poetry
test_framework: pytest
linter: ruff
formatter: black
runtime: GitHub Actions (cron)
core_models:
  preprocessing: gemini-2.0-flash
  analysis: claude-opus-4-7
external_apis:
  - yfinance       # 股價資料
  - newsapi        # 新聞
  - line-messaging # 推送
  - anthropic      # Claude
  - google-genai   # Gemini
```

---

## 🎯 核心設計哲學：Harness Engineering

**不可違反的核心原則：**

- ✅ 模型呼叫邏輯與 prompt 內容**完全分離**
- ✅ Prompt 一律存放在 `skills/*.md`，**禁止**寫死在 Python 字串
- ✅ 換模型只需改 `config.py` 與 skill，**禁止**修改 `agent/` 核心邏輯
- ✅ 業務邏輯只存在 `agent/`，`interfaces/` 只做翻譯
- ✅ 新功能優先寫進 `optional-skills/`，**禁止**膨脹 `agent/` 核心檔案

---

## 📁 專案結構

```text
kaching/
├── .github/workflows/daily_kaching.yml
├── agent/                      # 核心邏輯（嚴禁加入 prompt 字串）
│   ├── __init__.py
│   ├── fetcher.py              # 只負責拿資料
│   ├── analyzer.py             # 只負責呼叫 LLM
│   ├── notifier.py             # 只負責推送
│   └── alert.py                # 價格警報（Phase 2）
├── interfaces/                 # 入口層（每檔不超過 100 行）
│   ├── scheduler.py            # GitHub Actions 入口
│   ├── cli.py                  # CLI 入口（Phase 3）
│   └── web.py                  # Web/Bot Webhook（Phase 3）
├── skills/                     # AI 行為定義
│   ├── harness.md              # Gemini 前處理指令
│   ├── conservative.md         # Claude 保守風格
│   └── aggressive.md           # Claude 積極風格
├── optional-skills/            # 未來功能停車場
├── tools/
│   └── flex_formatter.py       # Line Flex Message 格式化
├── tests/
├── evals/                      # Eval framework（Phase 1.5）
│   ├── test_cases.json
│   └── judges/                 # LLM-as-judge skill 檔
├── docs/
│   ├── ADR/                    # Architecture Decision Records
│   └── learning-notes/         # 每週學習筆記
├── scripts/                    # 一次性工具腳本
├── plans/roadmap.md
├── subscribers.json            # ⚠️ git ignored
├── subscribers.example.json
├── config.py
├── pyproject.toml
└── .env.example
```

---

## ⚙️ 開發指令

### 環境設定

```bash
poetry install
cp .env.example .env
cp subscribers.example.json subscribers.json
```

### 執行

```bash
# 本地手動跑一次（測試用）
poetry run python -m interfaces.scheduler

# 跑單元測試
poetry run pytest

# 跑特定模組測試
poetry run pytest tests/test_fetcher.py -v

# Lint 與格式化
poetry run ruff check .
poetry run black .

# 全套檢查（提交前必跑）
poetry run ruff check . && poetry run black --check . && poetry run pytest
```

### CI 觸發

```bash
# 手動觸發 GitHub Actions
gh workflow run daily_kaching.yml
```

---

## 🧱 模組契約

每個模組的職責邊界**嚴格定義**，違反等於破壞 Harness 設計。

### `agent/fetcher.py`

```python
# 允許的函式簽名
def get_stock_data(symbols: list[str]) -> list[dict]: ...
def get_news(symbols: list[str], hours: int = 24) -> list[dict]: ...
def get_market_index() -> dict: ...

# 禁止：呼叫 LLM、推送訊息、決定推送對象
# 通過標準：把 fetcher.py 整個替換成讀 CSV 也能跑
```

### `agent/analyzer.py`

```python
def gemini_harness(raw_data: dict, skill: str = "harness") -> dict: ...
def claude_analyze(processed: dict, skill: str) -> str: ...
def analyze_for_subscriber(subscriber: dict, raw_data: dict) -> str: ...

# 禁止：硬編碼 prompt 字串
# 強制：所有 prompt 從 skills/{name}.md 讀入
# 通過標準：搜尋整個 analyzer.py 不應該找到任何長度 > 50 字的字串字面值
```

### `agent/tracer.py`

```python
def trace_llm_call(model: str, prompt_tokens: int, completion_tokens: int, latency_ms: float) -> None: ...
def get_cost_estimate(model: str, prompt_tokens: int, completion_tokens: int) -> float: ...

# 用 decorator 包裝 LLM 呼叫，不侵入業務邏輯
# 禁止：修改 analyzer.py 的核心邏輯
# 通過標準：移除 tracer 後 analyzer 仍能正常運作
```

### `agent/notifier.py`

```python
def send_flex_to_user(user_id: str, flex: dict) -> bool: ...
def broadcast(subscribers: list[dict], build_msg_fn) -> dict: ...
def log_delivery(user_id: str, status: str, error: str | None = None) -> None: ...

# 禁止：產生分析內容、決定推什麼股票
```

### `interfaces/*.py`

```python
# 每個介面層檔案的限制
MAX_LINES = 100
ALLOWED_IMPORTS = ["agent.*", "config", "subscribers loader"]
FORBIDDEN = ["直接呼叫 anthropic / google.generativeai", "業務邏輯"]
```

---

## 📜 Skill 規格

每個 `skills/*.md` 檔案必須包含這四個區段：

```markdown
# {Skill Name}

## When to use
{觸發條件描述}

## Instructions
{給 AI 的具體指令}

## Output format
{期望輸出格式：JSON schema / Markdown / 純文字}

## Examples
{1-2 個輸入輸出範例}
```

**Skill 撰寫規則：**

- 單一 skill 不超過 100 行（超過就拆分）
- 不可包含 Python 變數佔位符 `{xxx}` 以外的程式邏輯
- 修改 commit 必須以 `skill({name}):` 開頭

---

## 🗂 資料格式契約

### `subscribers.json`

```json
{
  "subscribers": [
    {
      "name": "string",
      "user_id": "U + 32 hex chars (Line user ID)",
      "stocks": ["AAPL", "NVDA"],
      "skill": "conservative | aggressive | summary_only",
      "timezone": "Asia/Taipei",
      "active": true
    }
  ]
}
```

### Fetcher 輸出格式

```json
{
  "stocks": [
    {
      "symbol": "string",
      "price": "number",
      "change_pct": "number",
      "volume": "integer",
      "timestamp": "ISO8601"
    }
  ],
  "news": [
    {
      "symbol": "string",
      "title": "string",
      "url": "string",
      "published_at": "ISO8601"
    }
  ]
}
```

---

## 🔐 環境變數

所有密鑰**必須**從環境變數讀取，**禁止**硬編碼。

```env
ANTHROPIC_API_KEY=sk-ant-...
GEMINI_API_KEY=...
LINE_CHANNEL_ACCESS_TOKEN=...
LINE_CHANNEL_SECRET=...
NEWS_API_KEY=...
```

GitHub Actions 透過 Repository Secrets 注入，本地透過 `.env`（已 gitignore）。

---

## 🚫 禁止事項（違反須先確認）

Agent 在以下情況**必須先停下來向使用者確認**，不可自行決定：

| 場景 | 原因 |
| --- | --- |
| 修改 `agent/` 內任何模組超過 30 行 | 可能破壞 Harness 邊界 |
| 在 Python 程式碼中加入超過 50 字的字串字面值（特別是 prompt） | 違反 prompt-code 分離原則 |
| 引入新的依賴套件（特別是 langchain / langgraph / haystack 等框架） | 違反「不依賴框架」的設計決策 |
| 修改 `subscribers.json` 結構 | 影響記憶層格式相容性 |
| 跳過測試直接 commit | 違反品質閘 |
| 在 `interfaces/` 內寫業務邏輯 | 違反介面層職責 |
| 在 `fetcher.py` 內呼叫 LLM | 違反模組契約 |
| 自動產生 / 大幅改寫此 `AGENTS.md` | 此檔案應由人類維護 |

---

## ✅ 提交前檢查清單

完成任何修改後，agent 必須自我驗證以下項目：

- [ ] `poetry run ruff check .` 通過
- [ ] `poetry run black --check .` 通過
- [ ] `poetry run pytest` 通過
- [ ] 沒有把 prompt 字串寫進 `.py` 檔案
- [ ] 沒有在 `agent/` 模組之間互相 import
- [ ] 新增的功能有對應的 skill 或 optional-skill 文件
- [ ] `subscribers.json` 沒有被 commit（檢查 `.gitignore`）
- [ ] 環境變數沒有被硬編碼

---

## 🔄 修改決策樹

收到新需求時，依序問自己：

```text
1. 能用調整 subscribers.json 解決嗎？
   YES → 改 JSON 設定，不動程式碼
   NO ↓
2. 能用調整現有 skill.md 解決嗎？
   YES → 改 skill，commit message 用 skill(name): 前綴
   NO ↓
3. 能新增 optional-skill 解決嗎？
   YES → 建新檔，不動 agent/ 核心
   NO ↓
4. 必須改 agent/ 程式碼嗎？
   YES → 確認影響範圍，必要時開議題討論
   ↓
5. 影響跨多個核心模組？
   YES → 停下來，可能是設計需要演進，向使用者確認
```

---

## 📊 Commit 訊息規範

使用 conventional commits，前綴限定如下：

| 前綴 | 用途 |
| --- | --- |
| `feat(agent):` | `agent/` 核心新功能 |
| `feat(interface):` | 新增介面層 |
| `skill(name):` | 修改某個 skill |
| `fix(module):` | 修錯誤 |
| `test:` | 加測試 |
| `chore:` | 環境/依賴/設定 |
| `docs:` | 文件（不含此 AGENTS.md） |
| `eval:` | 新增或修改 eval cases / judges |
| `arch:` | 架構決策變更（**需附 ADR**） |

---

## 🧪 測試策略

| 模組 | 測試類型 | 重點 |
| --- | --- | --- |
| `fetcher.py` | mock external API | 解析錯誤、空資料、限流 |
| `analyzer.py` | mock LLM SDK | skill 載入、prompt 組合、回應解析 |
| `notifier.py` | mock Line API | 推送失敗重試、批次邏輯 |
| `tracer.py` | 單元測試 | decorator 不侵入原邏輯、token 計算正確 |
| `interfaces/` | 整合測試 | end-to-end 但 mock 外部 |

**禁止**：在 CI 中呼叫真實的 LLM API 或 Line API。

---

## 🛤 Phase 狀態

當前所在 Phase 由 `plans/roadmap.md` 為準。各 Phase 工作邊界：

```yaml
phase_1_mvp:
  scope: [fetcher, analyzer, notifier, scheduler, 3 skills]
  out_of_scope: [CLI, Web, alerts, multi-channel]

phase_1_5_career:
  scope: [evals/, tracer.py, 2 articles, job applications]
  out_of_scope: [new user-facing features]

phase_2_extension:
  scope: [alert.py, multi-subscribe via Bot]
  
phase_3_interfaces:
  scope: [cli.py, web.py, line bot webhook]

phase_4_self_improve:
  scope: [skill auto-tuning, memory accumulation]

phase_5_sdk:
  scope: [extract harness_agent package]
  trigger_conditions:
    - 核心模組 6 個月未大改
    - 完成至少一個非股票領域 Agent
    - ≥ 3 個實際用戶
```

不在當前 Phase scope 內的工作，agent **必須**先確認再執行。

---

## 🔗 參考標準

- [AGENTS.md 規範](https://agents.md/)
- [Harness Engineering](https://github.com/NousResearch/hermes-agent)
- [Anthropic Skills 標準](https://agentskills.io)
- [Line Messaging API](https://developers.line.biz/en/docs/messaging-api/)

---

## 📝 此檔案的維護規則

- 由人類手動維護，**禁止 LLM 自動改寫**（即使是 agent 自己）
- 每次架構決策變更，agent 應建議更新項目，但等待人類確認
- 修改後必須 commit，並使用 `arch:` 前綴
- 此檔案的 token 預算控制在 3000 以內（目前約 2400）
