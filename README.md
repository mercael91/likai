<div align="center">

# 🎭 LikAI

### AI-Powered Content Platform for Creative Producers

**Personality analysis · Style mimicry · Content generation · Telegram publishing**

[![Next.js](https://img.shields.io/badge/Next.js-16-black?logo=next.js)]()
[![TypeScript](https://img.shields.io/badge/TypeScript-5-blue?logo=typescript)]()
[![React](https://img.shields.io/badge/React-19-61DAFB?logo=react)]()
[![Tailwind](https://img.shields.io/badge/Tailwind-3-38BDF8?logo=tailwind-css)]()
[![Prisma](https://img.shields.io/badge/Prisma-6-2D3748?logo=prisma)]()

</div>

---

## What Is LikAI?

LikAI is an AI platform that **learns your writing style** and generates content that sounds like you wrote it. Not generic AI text — *your* text.

It analyzes personality through 7-pass deep analysis, builds a style profile from your real texts, and produces posts, ideas, and scripts that match your voice. Then publishes directly to Telegram.

> Built for creative producers, artists, and content creators who want AI assistance without losing their authentic voice.

---

## How It Works

```
┌─────────────────────────────────────────────────────────────────┐
│                        LikAI PLATFORM                            │
│                                                                  │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐       │
│  │  YOUR TEXTS   │    │  7-PASS      │    │  STYLE       │       │
│  │  & MESSAGES   │───▶│  ANALYSIS    │───▶│  PROFILE     │       │
│  │              │    │              │    │              │       │
│  │ Chat logs    │    │ Extract →    │    │ Tone         │       │
│  │ Social posts │    │ Concepts →   │    │ Vocabulary   │       │
│  │ Articles     │    │ Themes →     │    │ Patterns     │       │
│  │ Brand books  │    │ Portrait →   │    │ Emotions     │       │
│  └──────────────┘    │ Patterns →   │    │ Preferences  │       │
│                      │ Merge →      │    └──────┬───────┘       │
│                      │ Instructions │           │                │
│                      └──────────────┘           │                │
│                                                 ▼                │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐       │
│  │  PUBLISH      │◀──│  ANTI-AI     │◀──│  GENERATE    │       │
│  │              │    │  POST-PROC   │    │              │       │
│  │ Telegram     │    │              │    │ Multi-pass   │       │
│  │ channels     │    │ Remove       │    │ generation   │       │
│  │ with buttons │    │ AI patterns  │    │ with RAG     │       │
│  └──────────────┘    └──────────────┘    └──────────────┘       │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

---

## Core Features

### 🧠 7-Pass Personality Analysis

The most distinctive feature. Seven sequential AI passes extract a deep understanding of who you are:

| Pass | Name | What It Does |
|------|------|-------------|
| 1 | **Extract Style** | Catchphrases, sentence patterns, favorite words, punctuation habits |
| 2 | **Extract Concepts** | Key ideas and their categories from conversations |
| 3 | **Group Themes** | Organize concepts into 5-8 thematic clusters |
| 4 | **Generate Portrait** | 250-350 word verbal personality portrait |
| 5 | **Response Patterns** | How you react to questions, humor, criticism |
| 6 | **Cross-Dialog Merge** | Combine data from multiple conversations |
| 7 | **Generation Instructions** | Concrete rules for content generation |

**Result:** A complete personality profile + style card + AI style profile that makes every generated text sound authentically *you*.

---

### ✍️ Content Generation Pipeline

```
1. Context Assembly
   ├── Personality Profile (portrait + style + emotions + interests)
   ├── Style Profile (heuristic metrics)
   ├── RAG (similar texts by topic)
   ├── Generation Instructions (from pass 7)
   ├── Biographical context
   ├── Smart Library
   ├── News context
   └── Trend-aware context

2. Draft Generation (Pass 1)
   └── Focus on content, temperature: 0.7

3. Style Polish (Pass 2, mystyle only)
   └── Extract specific style instructions, temperature: 0.4

4. Quality Audit
   └── Clickbait check, repetition, length, readability

5. Feedback Loop
   └── Track: totalGenerated, totalAccepted, recentAccuracy
```

### 🎨 7 Tone Modes

| Mode | Description |
|------|-------------|
| `mystyle` | Maximum style authenticity — mirrors your voice |
| `informative` | Expert, educational tone |
| `inspiring` | Motivating, uplifting |
| `casual` | Relaxed, friendly |
| `humorous` | Light, ironic |
| `professional` | Business-ready |
| `neutral` | Balanced, measured |

### 📐 11 Content Frameworks

`hook-story-payoff` · `problem-solution` · `list` · `question-answer` · `storytelling` · `educational` · `controversial` · `comparison` · `behind-scenes` · `trend-commentary` · `free`

### 🔀 Multi-Variant Generation

Generates 3 variants simultaneously and picks the best:

```
accurate  ──┐
engaging  ──┼──▶ Score by 5 metrics ──▶ Best variant
creative  ──┘

Metrics: styleMatch×0.3 + engagement×0.25 + originality×0.2
         + length×0.15 + readability×0.1
```

### 🛡️ Anti-AI Post-Processing

Removes telltale AI patterns from generated text:

- ❌ Excessive em-dashes (—) — only if the author uses them
- ❌ Cliché phrases ("в мире", "давайте разберёмся")
- ❌ Over-emojiing
- ❌ AI sentence structure patterns
- ✅ Preserves author's real quirks and habits

### 📚 RAG (Retrieval-Augmented Generation)

- **Storage:** SQLite with JSON embeddings
- **Model:** nomic-embed-text via Ollama
- **Search:** Cosine similarity with configurable threshold
- **Usage:** Finds your most relevant past texts for each generation

### 😊 Emotional Modulation

| Setting | Options |
|---------|---------|
| **Mood** | uplifting, calm, energetic, melancholic, neutral |
| **Target** | engage, inspire, provoke, comfort, entertain |
| **Intensity** | 1-10 |

### 🦸 Mascot Mode

Create a brand personality through dialogue:

- Archetype selection
- Tone & voice definition
- Target audience mapping
- Brand values & tabus
- Brandbook PDF upload with color palette extraction
- Auto-generated anti-style instructions from brand tabus

---

## Architecture

```
┌─────────────┐     ┌──────────────┐     ┌─────────┐
│  Telegram   │────▶│  Next.js App │────▶│  SQLite  │
│  Bot        │     │  (PM2)       │     │  (Prisma)│
└─────────────┘     └──────┬───────┘     └─────────┘
                           │
                    ┌──────▼───────┐
                    │  DeepSeek API│
                    │  + Ollama    │
                    │  (embeddings)│
                    └──────────────┘
```

### Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | Next.js 16, React 19, TypeScript, Tailwind CSS, shadcn/ui |
| Backend | Next.js API Routes, Prisma ORM |
| Database | SQLite (25+ models, 693-line schema) |
| AI | DeepSeek API + Ollama (nomic-embed-text for RAG) |
| Bot | Telegram Bot API (Telegraf.js) |
| Proxy | Caddy (HTTP → Next.js :3000) |
| Deploy | PM2, webpack build, automated deploy script |
| Backups | Daily at 3:00 AM |

### API Surface

**70 API routes** across:

| Domain | Endpoints |
|--------|-----------|
| Auth | login, register, telegram, session, change-password |
| Chat | AI conversation with context |
| Generate | posts, ideas, shorts, headlines, improvements |
| Analysis | personality, style, queue management |
| Drafts | CRUD + publish to Telegram |
| Mascot | brand personality management |
| Settings | app config, Telegram channels |
| RAG | embeddings, similarity search |

---

## Results

| Metric | Value |
|--------|-------|
| API Routes | 70 |
| Prisma Models | 25+ |
| TypeScript Files | 133 |
| Personality Analysis Passes | 7 |
| Tone Modes | 7 |
| Content Frameworks | 11 |
| Anti-AI Patterns Caught | 15+ |

---

## Project Status

- [x] 7-pass personality analysis
- [x] Style mimicry generation
- [x] Anti-AI post-processing
- [x] RAG with vector embeddings
- [x] Multi-variant generation
- [x] Emotional modulation
- [x] Telegram publishing
- [x] Mascot mode
- [x] Quality audit pipeline
- [x] Feedback loop learning
- [ ] Content calendar automation
- [ ] Multi-language support
- [ ] Instagram/Twitter publishing

---

## Contact

- **Creator:** [mercael91](https://github.com/mercael91)
- **Telegram:** [@mercael](https://t.me/mercael)

## Related Projects

- **[AGI-Zarodysh](https://github.com/mercael91/embryo-agent)** — Autonomous AI agent that contributes to open source. 71 modules, 11 merged PRs.
- **[Nexus Analytica](https://github.com/mercael91/nexus-analitica)** — AI news intelligence with consensus analysis and scenario forecasting.

---

<div align="center">

**Your voice, amplified by AI — not replaced by it.**

*Голос автора, усиленный ИИ — но не подменённый.*

</div>
