# 🤑 Ka-ching!

> *Your money is talking. Every morning, in your Line.*
>
> Daily US stock AI analysis delivered straight to your Line app.
>
> Built on **Harness Engineering** philosophy. Models are swappable. Harness is not.

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![GitHub Actions](https://img.shields.io/badge/CI-GitHub%20Actions-2088FF?logo=github-actions&logoColor=white)](.github/workflows/daily_kaching.yml)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Built with Harness](https://img.shields.io/badge/Built_with-Harness_Engineering-FF6B6B)](https://github.com/NousResearch/hermes-agent)

[中文版 README](README.md) · [English](#)

---

## ✨ What does it do?

After US market close, **Ka-ching!** automatically:

```
📊 Fetch prices + news   →   🤖 Dual AI engine analysis   →   💰 Line "Ka-ching!"
```

Open your phone and find a report tailored just for you.

- **Personalized** — each subscriber has their own watchlist and analysis style
- **Dual AI engine** — Gemini handles preprocessing, Claude handles deep analysis
- **Zero ops** — runs on GitHub Actions, no server, no cost
- **Extensible** — from scheduled push → CLI → Web Chat → Line Bot conversation

---

## 🎬 Preview

Every morning at 5:30 AM (Taipei time), your Line gets a *Ka-ching!*:

```
┌─────────────────────────────┐
│  🤑 Ka-ching!               │
│  2026-04-27                 │
├─────────────────────────────┤
│  🟢 AAPL   $189.50  +2.3%   │
│  🔴 NVDA   $875.20  -1.1%   │
│  🟢 TSLA   $245.80  +0.8%   │
├─────────────────────────────┤
│  🤖 AI Insight              │
│  Tech stocks diverged...    │
└─────────────────────────────┘
```

---

## 🚀 Quick Start

### Prerequisites

- Python 3.11+
- Poetry
- API keys for:
  - Anthropic (Claude)
  - Google Gemini
  - Line Channel Access Token
  - News API (optional)

### Installation

```bash
# 1. Clone
git clone https://github.com/yourname/kaching.git
cd kaching

# 2. Install dependencies
poetry install

# 3. Configure environment variables
cp .env.example .env
# Edit .env and fill in your API keys

# 4. Configure subscribers
cp subscribers.example.json subscribers.json
# Edit subscribers.json with your Line user_id and watchlist
```

### Run locally

```bash
poetry run python -m interfaces.scheduler
```

If everything works, your Line will *Ka-ching!* with a test report 🎉

### Deploy to GitHub Actions

```bash
# 1. Push to GitHub
git push origin main

# 2. Add secrets in repo Settings → Secrets:
#    - ANTHROPIC_API_KEY
#    - GEMINI_API_KEY
#    - LINE_CHANNEL_ACCESS_TOKEN
#    - NEWS_API_KEY

# 3. Trigger a test run
gh workflow run daily_kaching.yml
```

After setup, it runs automatically after every US market close.

---

## 🧠 Design Philosophy

### Harness Engineering — Invest in the shell, not the model

Ka-ching! is inspired by Nous Research's [Hermes Agent](https://github.com/NousResearch/hermes-agent):

> **The real AI breakthrough in 2026 isn't smarter models — it's smarter wrappers around models.**

"Harness" means horse tack — the horse's raw power is fixed, but **how you strap it, guide it, and get feedback** determines how much it can pull, how fast it can run, and where it goes. LLMs work the same way.

### Five-Layer Harness Architecture

```
┌────────────────────────────────────────────┐
│  Orchestration  → .github/workflows/        │
│  When, who, in what order to call LLM       │
├────────────────────────────────────────────┤
│  Instructions   → skills/*.md               │
│  What LLM should do, how to output          │
├────────────────────────────────────────────┤
│  Constraints    → config.py + .env          │
│  Which model, what parameters, environment  │
├────────────────────────────────────────────┤
│  Memory         → subscribers.json          │
│  Personalized state, preferences, history  │
├────────────────────────────────────────────┤
│  Feedback       → notifier + logs           │
│  Record results, drive system evolution     │
└────────────────────────────────────────────┘
              ↑↓
        ┌──────────┐
        │   LLM    │ ← swappable core
        └──────────┘
```

### Why not LangChain / LangGraph?

| | LangChain / LangGraph | Ka-ching! |
|---|---|---|
| Prompt location | Python strings | `skills/*.md` |
| Swap models | Must change Chain config | Only change skill |
| Dependency weight | Heavy | Light (pure SDK) |
| Best for | Complex dynamic decisions | Linear pipeline |

Full rationale: [`docs/ADR/001-no-langchain.md`](docs/ADR/001-no-langchain.md)

---

## 📁 Project Structure

```
kaching/
├── .github/workflows/daily_kaching.yml   # Scheduler
├── agent/                                # Core logic
│   ├── fetcher.py                        # Data only
│   ├── analyzer.py                       # LLM calls only
│   ├── notifier.py                       # Delivery only
│   └── alert.py                          # Price alerts (Phase 2)
├── interfaces/                           # Entry points
│   ├── scheduler.py                      # GitHub Actions
│   ├── cli.py                            # CLI (Phase 3)
│   └── web.py                            # Webhook (Phase 3)
├── skills/                               # AI behavior definitions
│   ├── harness.md                        # Gemini preprocessing
│   ├── conservative.md                   # Conservative style
│   └── aggressive.md                     # Aggressive style
├── optional-skills/                      # Future features
├── tools/flex_formatter.py               # Flex Message formatter
├── tests/
├── docs/
├── plans/roadmap.md
├── subscribers.json                      # ⚠️ gitignored
├── subscribers.example.json
├── config.py
├── pyproject.toml
├── .env.example
├── AGENTS.en.md                          # 🤖 For AI coding agents
├── AGENTS.zh.md                          # 🤖 中文版
├── README.en.md                          # 📖 You are here
└── README.md                             # 📖 中文版
```

---

## ⚙️ Personalization

### Subscriber config (`subscribers.json`)

Each subscriber can customize:

```json
{
  "subscribers": [
    {
      "name": "Me",
      "user_id": "Uxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
      "stocks": ["AAPL", "NVDA", "TSLA"],
      "skill": "aggressive",
      "timezone": "Asia/Taipei",
      "active": true
    }
  ]
}
```

### Built-in Skills

| Skill | Style | Best for |
|---|---|---|
| `conservative` | Emphasizes risk, cautious tone | Long-term investors |
| `aggressive` | Emphasizes opportunity, bullish tone | Active traders |
| `summary_only` | Numbers only, no analysis | Quick scanners |

### Create your own skill

```bash
cp skills/conservative.md skills/my_style.md
vim skills/my_style.md
# then set "skill": "my_style" in subscribers.json
```

---

## 🛤 Roadmap

### Phase 1: MVP ✅ (In Progress)
Daily scheduled push with personalized reports.

### Phase 2: Extension
- Price drop alerts
- Line Bot subscription commands

### Phase 3: Interactive Interfaces
- CLI: `kaching "Is NVDA worth buying?"`
- Line Bot conversation mode
- Web Chat

### Phase 4: Self-improvement
- Agent analyzes push history and suggests skill improvements
- Cross-session memory accumulation

### Phase 5: SDK (Long-term)
Extract the harness as a `harness-agent` package, so others can use the same design pattern for their own domain agents.

---

## 🤝 Contributing

Please read [`AGENTS.en.md`](AGENTS.en.md) before submitting a PR:

- ✅ Prompts always live in `skills/*.md` — never in Python strings
- ✅ New features go in `optional-skills/` first
- ✅ Use conventional commits (see AGENTS.en.md for prefixes)

### Pre-commit check

```bash
poetry run ruff check . && \
poetry run black --check . && \
poetry run pytest
```

---

## 🧪 Dev Commands

```bash
poetry install                               # Install
poetry run python -m interfaces.scheduler    # Run locally
poetry run pytest                            # Tests
poetry run pytest tests/test_fetcher.py -v  # Specific test
poetry run ruff check .                      # Lint
poetry run black .                           # Format
gh workflow run daily_kaching.yml            # Trigger CI
```

---

## ❓ FAQ

<details>
<summary><b>Q: How is this different from just asking ChatGPT about stocks?</b></summary>

ChatGPT doesn't have your personalized watchlist, won't push proactively, and can't integrate with Line. Ka-ching! is a personal agent that actively serves you on your schedule.

</details>

<details>
<summary><b>Q: Why two AI models? Isn't that more expensive?</b></summary>

Actually cheaper. Gemini handles large volumes of raw data quickly and cheaply, filtering out noise before passing only the essentials to Claude. Total token consumption is lower than using Claude alone.

</details>

<details>
<summary><b>Q: Is GitHub Actions really free?</b></summary>

Yes. Public repos are completely free. Private repos get 2,000 minutes/month. Ka-ching! runs ~1-2 minutes per day — well within limits.

</details>

<details>
<summary><b>Q: Is this investment advice?</b></summary>

No. Ka-ching! is a **personal information aggregation tool**. All AI-generated "analysis" is statistical summary only and **does not constitute investment advice**. All investment decisions are your own responsibility.

</details>

---

## 📚 Further Reading

- [`AGENTS.en.md`](AGENTS.en.md) — AI coding agent contract
- [`docs/ADR/`](docs/ADR/) — Architecture Decision Records
- [Hermes Agent](https://github.com/NousResearch/hermes-agent) — Harness Engineering origin
- [AGENTS.md spec](https://agents.md/)
- [Line Messaging API](https://developers.line.biz/en/docs/messaging-api/)

---

## 📜 License

MIT License — see [LICENSE](LICENSE)

---

## 🙏 Acknowledgements

- [Nous Research](https://nousresearch.com/) — Harness Engineering philosophy
- [Anthropic](https://anthropic.com/) — Claude API
- [Google](https://ai.google.dev/) — Gemini API

---

<p align="center">
  <i>🤑 Ka-ching! · Built with Harness Engineering · Models swappable, Harness invariant.</i>
</p>

<p align="center">
  <b>In God We Trust 🙏</b>
</p>
