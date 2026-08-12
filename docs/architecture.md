# Architecture

> Technical deep-dive into LikAI's architecture and design decisions

## System Overview

LikAI is built on Next.js 16 with App Router, using a monolithic architecture optimized for a single-team workflow. The backend lives in API routes, the AI pipeline runs as server-side functions, and the Telegram bot operates as a standalone process managed by PM2.

## Data Flow

```
User Input (text/chat/upload)
    ↓
┌─────────────────────────────┐
│     Next.js API Route       │
│  (auth → validate → route)  │
└──────────────┬──────────────┘
               │
    ┌──────────▼──────────┐
    │   Context Assembly   │
    │                      │
    │  ┌────────────────┐  │
    │  │ Personality DB  │  │
    │  │ (Prisma/SQLite) │  │
    │  └────────┬───────┘  │
    │           │          │
    │  ┌────────▼───────┐  │
    │  │  RAG Engine     │  │
    │  │ (embeddings +   │  │
    │  │  cosine search) │  │
    │  └────────┬───────┘  │
    │           │          │
    │  ┌────────▼───────┐  │
    │  │ News + Trends   │  │
    │  │ + Bio Facts     │  │
    │  └────────┬───────┘  │
    └───────────┼──────────┘
                │
    ┌───────────▼──────────┐
    │    AI Pipeline        │
    │                       │
    │  Draft → Polish →     │
    │  Quality Audit →      │
    │  Anti-AI Cleanup      │
    └───────────┬──────────┘
                │
    ┌───────────▼──────────┐
    │   Output / Publish    │
    │                       │
    │  Save draft →         │
    │  Edit →               │
    │  Publish to Telegram  │
    └───────────────────────┘
```

## Database Schema

25+ Prisma models organized into domains:

### Auth & Users
- `User` — accounts with email/password or Telegram auth
- `Session` — session management with encryption
- `Role` — admin / user

### Projects
- `Project` — multi-project support per user
- `ProjectMember` — team collaboration

### Content
- `Post` — generated and saved posts
- `Draft` — work-in-progress content
- `Idea` — generated content ideas
- `Short` — short-form video scripts

### Analysis
- `PersonalityProfile` — full personality data (JSON)
- `StyleProfile` — heuristic style metrics
- `AIStyleProfile` — AI-extracted style elements
- `EmotionalProfile` — Big Five + emotion vectors
- `BiographyFact` — extracted biographical data

### RAG
- `Embedding` — vector embeddings (JSON in SQLite)
- `SmartLibraryItem` — curated content library

### Communication
- `TelegramChannel` — channel configurations
- `Message` — chat history
- `QueueTask` — async analysis queue

### Generation
- `GenerationAccuracy` — feedback loop metrics
- `GenerationLog` — audit trail

## 7-Pass Analysis Pipeline

```
Input: Raw text samples (chat logs, posts, articles)
    ↓
Pass 1: EXTRACT STYLE
    ├── Catchphrases ("короче", "слышь")
    ├── Sentence patterns (avg length, complexity)
    ├── Favorite words (top 20 by frequency)
    ├── Punctuation habits (em-dash frequency, exclamation marks)
    ├── Emoji style (which, how often, position)
    └── Reaction words ("ого", "ну такое", "имхо")
    ↓
Pass 2: EXTRACT CONCEPTS
    ├── Named entities (people, places, brands)
    ├── Key topics (music, tech, philosophy)
    └── Value indicators (what matters to this person)
    ↓
Pass 3: GROUP THEMES
    └── Cluster concepts into 5-8 thematic categories
    ↓
Pass 4: GENERATE PORTRAIT
    └── 250-350 word verbal portrait of the personality
    ↓
Pass 5: EXTRACT RESPONSE PATTERNS
    ├── How they answer questions
    ├── Humor style (sarcasm, self-deprecation, wordplay)
    ├── Criticism handling (deflect, engage, ignore)
    └── Conversation initiation patterns
    ↓
Pass 6: CROSS-DIALOG MERGE
    └── Combine insights from multiple text sources
    ↓
Pass 7: GENERATION INSTRUCTIONS
    └── Concrete rules for "write like this person"
```

### Heuristic Analysis (No AI Required)

Runs in parallel with AI passes:

- **Word frequency analysis** — top words, stop-word filtered
- **Emoji frequency** — which emojis, how often
- **Punctuation profile** — dash usage, exclamation density
- **Parasite words** — filler phrases
- **Slang detection** — informal vocabulary
- **Sentiment analysis** — positive/negative/neutral word dictionaries

## RAG System

### Embedding Generation
```
Text chunk → Ollama (nomic-embed-text) → 768-dim vector → SQLite
```

### Similarity Search
```
Query → embed → cosine_similarity(query, all_stored) → top-K results
```

### Threshold Filtering
```
Results with similarity < threshold → discarded
Default threshold: 0.7 (configurable per project)
```

## Anti-AI Post-Processing

A rule-based system that strips AI-generated patterns:

### Patterns Detected & Removed

| Pattern | Rule |
|---------|------|
| Em-dash abuse | Remove if author doesn't use em-dashes |
| Cliche phrases | "В современном мире", "Давайте разберёмся" |
| Excessive emoji | > 3 emoji per paragraph → reduce |
| AI transitions | "Тем не менее", "Стоит отметить" |
| List addiction | Convert bullet lists to prose if author writes prose |
| Conclusion pattern | Remove "В заключение..." if author never uses it |

### Style-Dependent Rules

The anti-AI filter adapts to the author's real style:
- If the author uses lots of emoji → keep emoji
- If the author writes short sentences → keep short sentences
- If the author uses slang → preserve slang

## Deployment Architecture

```
Production Server
├── Caddy (reverse proxy)
│   ├── HTTP :80  → Next.js :3000
│   └── HTTPS :443 → Matrix :8008
│
├── PM2 (process manager)
│   ├── next-app (Next.js, port 3000)
│   └── telegram-bot (standalone process)
│
├── SQLite (file-based database)
│   └── /opt/producer-ai/dist/prisma/data/db.sqlite
│
├── Ollama (local AI)
│   └── nomic-embed-text model
│
└── Backups
    └── Daily 3:00 AM → F:\ProducerAI_BU
```

### Deploy Pipeline

```bash
deploy.sh:
1. rm -rf .next          # Clean build
2. next build --webpack  # Production build
3. Copy manifests        # From dev build (Turbopack workaround)
4. Preserve encryptionKey
5. PM2 restart
6. Health check
```
