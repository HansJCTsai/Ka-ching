# AGENTS.md

> **Ka-ching!** 🤑 — Daily US Stock AI Analysis & Line Notification System
> *Your money is talking. 錢錢來了哦 💰*
>
> Built on Harness Engineering philosophy. Models are swappable. Harness is not.
>
> ⚠️ This file is an operational contract for AI coding agents (Claude Code / Cursor / Codex / Copilot). For humans, see `README.en.md`.

---

## 🤖 Agent Startup Rules

Before starting any task, you **must** complete the following in order:

1. Read this file in its entirety
2. Check `plans/roadmap.md` to confirm the current Phase
3. Inspect the `skills/` and `optional-skills/` directory structure
4. Check `.env.example` to confirm required environment variables
5. Before violating any rule in the "Forbidden Actions" section, stop and confirm with the user

---

## 📋 Project Overview

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
  - yfinance       # stock price data
  - newsapi        # news
  - line-messaging # delivery
  - anthropic      # Claude
  - google-genai   # Gemini
```

---

## 🎯 Core Design Philosophy: Harness Engineering

**Non-negotiable core principles:**

- ✅ Model call logic and prompt content are **completely separated**
- ✅ All prompts live in `skills/*.md` — **never** hardcoded in Python strings
- ✅ Swapping models only requires changing `config.py` and skills — **never** touch `agent/` core logic
- ✅ Business logic lives only in `agent/` — `interfaces/` only translates
- ✅ New features go into `optional-skills/` first — **never** bloat `agent/` core files

---

## 📁 Project Structure

```
kaching/
├── .github/workflows/daily_kaching.yml
├── agent/                      # Core logic (no prompt strings allowed)
│   ├── __init__.py
│   ├── fetcher.py              # Data fetching only
│   ├── analyzer.py             # LLM calls only
│   ├── notifier.py             # Delivery only
│   └── alert.py                # Price alerts (Phase 2)
├── interfaces/                 # Entry points (max 100 lines each)
│   ├── scheduler.py            # GitHub Actions entry
│   ├── cli.py                  # CLI entry (Phase 3)
│   └── web.py                  # Web/Bot Webhook (Phase 3)
├── skills/                     # AI behavior definitions
│   ├── harness.md              # Gemini preprocessing instructions
│   ├── conservative.md         # Claude conservative style
│   └── aggressive.md           # Claude aggressive style
├── optional-skills/            # Future features parking lot
├── tools/
│   └── flex_formatter.py       # Line Flex Message formatter
├── tests/
├── plans/roadmap.md
├── subscribers.json            # ⚠️ git ignored
├── subscribers.example.json
├── config.py
├── pyproject.toml
└── .env.example
```

---

## ⚙️ Development Commands

### Environment Setup

```bash
poetry install
cp .env.example .env
cp subscribers.example.json subscribers.json
```

### Run

```bash
# Run locally (for testing)
poetry run python -m interfaces.scheduler

# Run tests
poetry run pytest

# Run specific module tests
poetry run pytest tests/test_fetcher.py -v

# Lint and format
poetry run ruff check .
poetry run black .

# Full check (run before every commit)
poetry run ruff check . && poetry run black --check . && poetry run pytest
```

### Trigger CI

```bash
gh workflow run daily_kaching.yml
```

---

## 🧱 Module Contracts

Each module's responsibility boundary is **strictly defined**. Violating it breaks Harness design.

### `agent/fetcher.py`

```python
# Allowed function signatures
def get_stock_data(symbols: list[str]) -> list[dict]: ...
def get_news(symbols: list[str], hours: int = 24) -> list[dict]: ...
def get_market_index() -> dict: ...

# Forbidden: calling LLM, sending messages, deciding who to notify
# Pass criterion: replacing fetcher.py entirely with CSV reading still works
```

### `agent/analyzer.py`

```python
def gemini_harness(raw_data: dict, skill: str = "harness") -> dict: ...
def claude_analyze(processed: dict, skill: str) -> str: ...
def analyze_for_subscriber(subscriber: dict, raw_data: dict) -> str: ...

# Forbidden: hardcoded prompt strings
# Required: all prompts loaded from skills/{name}.md
# Pass criterion: grep the entire analyzer.py — no string literals longer than 50 chars
```

### `agent/notifier.py`

```python
def send_flex_to_user(user_id: str, flex: dict) -> bool: ...
def broadcast(subscribers: list[dict], build_msg_fn) -> dict: ...
def log_delivery(user_id: str, status: str, error: str | None = None) -> None: ...

# Forbidden: generating analysis content, deciding what stocks to push
```

### `interfaces/*.py`

```python
# Constraints for each interface layer file
MAX_LINES = 100
ALLOWED_IMPORTS = ["agent.*", "config", "subscribers loader"]
FORBIDDEN = ["direct calls to anthropic / google.generativeai", "business logic"]
```

---

## 📜 Skill Specification

Each `skills/*.md` file must contain these four sections:

```markdown
# {Skill Name}

## When to use
{Description of trigger conditions}

## Instructions
{Specific instructions for the AI}

## Output format
{Expected output format: JSON schema / Markdown / plain text}

## Examples
{1-2 input/output examples}
```

**Skill writing rules:**

- Single skill must not exceed 100 lines (split if over)
- Must not contain logic beyond Python variable placeholders `{xxx}`
- Commit messages for changes must start with `skill({name}):`

---

## 🗂 Data Format Contracts

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

### Fetcher Output Format

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

## 🔐 Environment Variables

All secrets **must** be read from environment variables — **never** hardcoded.

```
ANTHROPIC_API_KEY=sk-ant-...
GEMINI_API_KEY=...
LINE_CHANNEL_ACCESS_TOKEN=...
LINE_CHANNEL_SECRET=...
NEWS_API_KEY=...
```

GitHub Actions injects via Repository Secrets. Local development uses `.env` (gitignored).

---

## 🚫 Forbidden Actions (must confirm before proceeding)

The agent **must stop and confirm with the user** in the following situations:

| Situation | Reason |
|---|---|
| Modifying any `agent/` module by more than 30 lines | May break Harness boundaries |
| Adding string literals longer than 50 chars in Python (especially prompts) | Violates prompt-code separation |
| Introducing new dependencies (especially langchain / langgraph / haystack) | Violates "no framework" design decision |
| Modifying `subscribers.json` structure | Breaks memory layer format compatibility |
| Skipping tests before committing | Violates quality gate |
| Writing business logic inside `interfaces/` | Violates interface layer responsibility |
| Calling LLM inside `fetcher.py` | Violates module contract |
| Auto-generating or rewriting this `AGENTS.md` | This file must be maintained by humans |

---

## ✅ Pre-commit Checklist

After completing any change, the agent must self-verify the following:

- [ ] `poetry run ruff check .` passes
- [ ] `poetry run black --check .` passes
- [ ] `poetry run pytest` passes
- [ ] No prompt strings written inside `.py` files
- [ ] No cross-imports between `agent/` modules
- [ ] New features have corresponding skill or optional-skill documentation
- [ ] `subscribers.json` not committed (check `.gitignore`)
- [ ] No hardcoded environment variables

---

## 🔄 Change Decision Tree

When receiving a new requirement, ask yourself in order:

```
1. Can this be solved by adjusting subscribers.json?
   YES → Change JSON config, no code changes
   NO ↓
2. Can this be solved by adjusting an existing skill.md?
   YES → Edit skill, use skill(name): commit prefix
   NO ↓
3. Can a new optional-skill solve this?
   YES → Create new file, do not touch agent/ core
   NO ↓
4. Must agent/ code be changed?
   YES → Assess impact scope, open discussion if needed
   ↓
5. Does it affect multiple core modules?
   YES → Stop, this may require architectural evolution, confirm with user
```

---

## 📊 Commit Message Convention

Use conventional commits with these prefixes:

| Prefix | Use |
|---|---|
| `feat(agent):` | New feature in `agent/` core |
| `feat(interface):` | New interface layer |
| `skill(name):` | Modifying a skill |
| `fix(module):` | Bug fix |
| `test:` | Adding tests |
| `chore:` | Environment/dependencies/config |
| `docs:` | Documentation (excluding this AGENTS.md) |
| `arch:` | Architecture decision change (**ADR required**) |

---

## 🧪 Testing Strategy

| Module | Test Type | Focus |
|---|---|---|
| `fetcher.py` | mock external API | parsing errors, empty data, rate limits |
| `analyzer.py` | mock LLM SDK | skill loading, prompt assembly, response parsing |
| `notifier.py` | mock Line API | push failure retry, batch logic |
| `interfaces/` | integration test | end-to-end with mocked externals |

**Forbidden**: calling real LLM APIs or Line API in CI.

---

## 🛤 Phase Status

Current Phase is determined by `plans/roadmap.md`. Work boundaries per Phase:

```yaml
phase_1_mvp:
  scope: [fetcher, analyzer, notifier, scheduler, 3 skills]
  out_of_scope: [CLI, Web, alerts, multi-channel]

phase_2_extension:
  scope: [alert.py, multi-subscribe via Bot]

phase_3_interfaces:
  scope: [cli.py, web.py, line bot webhook]

phase_4_self_improve:
  scope: [skill auto-tuning, memory accumulation]

phase_5_sdk:
  scope: [extract harness_agent package]
  trigger_conditions:
    - core modules unchanged for 6 months
    - at least one non-stock domain Agent completed
    - ≥ 3 active users
```

Work outside the current Phase scope **must** be confirmed before proceeding.

---

## 🔗 References

- AGENTS.md spec: https://agents.md/
- Harness Engineering: https://github.com/NousResearch/hermes-agent
- Anthropic Skills standard: https://agentskills.io
- Line Messaging API: https://developers.line.biz/en/docs/messaging-api/

---

## 📝 Maintenance Rules for This File

- Maintained manually by humans — **LLMs must not auto-rewrite this** (including the agent itself)
- When architecture decisions change, agent should suggest updates and wait for human approval
- Changes must be committed with `arch:` prefix
- Token budget for this file: keep under 3,000 (currently ~2,500)
