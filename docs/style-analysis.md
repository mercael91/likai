# 7-Pass Personality Analysis

> Deep technical breakdown of LikAI's signature feature

## Overview

Most AI tools treat you as a generic user. LikAI treats you as a unique individual with a distinct voice, vocabulary, rhythm, and worldview. The 7-pass analysis system extracts this from your real text samples.

## Why 7 Passes?

Each pass builds on the previous one:

```
Pass 1 (Style)      → "How do they write?"
Pass 2 (Concepts)   → "What do they talk about?"
Pass 3 (Themes)     → "What categories emerge?"
Pass 4 (Portrait)   → "Who IS this person?"
Pass 5 (Patterns)   → "How do they react?"
Pass 6 (Merge)      → "What's consistent across sources?"
Pass 7 (Instructions) → "How to write like them?"
```

## Pass Details

### Pass 1: Extract Style

**Input:** Raw text samples (minimum 500 words recommended)

**Output:**
```json
{
  "catchphrases": ["короче", "слышь", "имхо"],
  "sentencePatterns": {
    "avgLength": 12.4,
    "complexity": "medium",
    "preferredStructure": "short-long-short"
  },
  "favoriteWords": [
    {"word": "реально", "frequency": 0.03},
    {"word": "тип", "frequency": 0.02}
  ],
  "punctuation": {
    "emDashFrequency": 0.1,
    "exclamationRate": 0.05,
    "ellipsisRate": 0.08
  },
  "emojiStyle": {
    "frequency": 0.3,
    "preferred": ["🔥", "💀", "😂"],
    "position": "end-of-message"
  },
  "reactionWords": ["ого", "ну такое", "бомба"]
}
```

### Pass 2: Extract Concepts

**Input:** Same text samples

**Output:**
```json
{
  "concepts": [
    {"name": "джембе", "category": "music", "mentions": 12},
    {"name": "Спиноза", "category": "philosophy", "mentions": 5},
    {"name": "дача", "category": "lifestyle", "mentions": 8}
  ]
}
```

### Pass 3: Group Themes

**Input:** Concepts from Pass 2

**Output:**
```json
{
  "themes": [
    {"name": "Музыка", "concepts": ["джембе", "ритм", "барабаны"]},
    {"name": "Философия", "concepts": ["Спиноза", "пантеизм", "сознание"]},
    {"name": "Технологии", "concepts": ["AI", "код", "архитектура"]}
  ]
}
```

### Pass 4: Generate Portrait

**Input:** All previous passes combined

**Output:** 250-350 word verbal portrait describing the person's communication style, worldview, values, and quirks.

### Pass 5: Extract Response Patterns

**Input:** Conversational text (chat logs preferred)

**Output:**
```json
{
  "questionResponse": "direct, often with counter-question",
  "humorStyle": "self-deprecating irony, wordplay",
  "criticismHandling": "engages directly, doesn't deflect",
  "initiationPattern": "starts with context, then asks"
}
```

### Pass 6: Cross-Dialog Merge

**Input:** Multiple text sources analyzed separately

**Process:**
1. Compare style profiles across sources
2. Identify consistent patterns (high confidence)
3. Identify context-dependent patterns (medium confidence)
4. Resolve contradictions with source weighting

### Pass 7: Generation Instructions

**Input:** All analysis results

**Output:** Concrete rules for the generation pipeline:
```json
{
  "rules": [
    "Use short sentences (8-15 words avg)",
    "Start posts with a question or bold statement",
    "Include exactly one emoji per paragraph",
    "Never use 'давайте разберёмся'",
    "Use '—' sparingly, prefer ',' for pauses",
    "End with a call to action or open question"
  ]
}
```

## Heuristic Analysis

Runs alongside AI passes for cross-validation:

| Metric | Method | Purpose |
|--------|--------|---------|
| Word frequency | TF counting | Favorite vocabulary |
| Emoji frequency | Pattern matching | Emoji habits |
| Punctuation density | Character counting | Style markers |
| Sentiment ratio | Dictionary matching | Emotional tendency |
| Avg sentence length | Tokenization | Complexity indicator |
| Question rate | `?` counting | Engagement style |
| List usage | Line pattern detection | Structure preference |
| Unique word ratio | Set cardinality | Vocabulary richness |
