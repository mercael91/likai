# Content Generation

> How LikAI generates content that sounds authentically human

## The Problem

Generic AI content is recognizable. Em-dashes everywhere. "В современном мире..." Listicles with perfect formatting. LikAI solves this by:

1. Learning YOUR specific style from YOUR texts
2. Generating in YOUR voice, not AI's voice
3. Post-processing to remove AI fingerprints
4. Tracking accuracy through feedback loops

## Generation Pipeline

### Step 1: Context Assembly

Before generating a single word, LikAI assembles a rich context:

```
┌─────────────────────────────────────────────┐
│              CONTEXT WINDOW                  │
│                                              │
│  Personality Profile                        │
│  ├── Portrait (250 words)                   │
│  ├── Style metrics                          │
│  ├── Emotional profile                      │
│  └── Interest categories                    │
│                                              │
│  Style Card (compact)                       │
│  ├── Avg sentence length                    │
│  ├── Top 10 favorite words                  │
│  ├── Emoji frequency                        │
│  └── Tone indicators                        │
│                                              │
│  RAG Results                                │
│  ├── 3-5 most similar past texts            │
│  └── Relevance scores                       │
│                                              │
│  Generation Instructions (Pass 7)           │
│  ├── "Use short sentences"                  │
│  ├── "Start with a question"                │
│  └── "Never use X, Y, Z"                    │
│                                              │
│  Biographical Context                       │
│  ├── Key life events                        │
│  └── Personal references                    │
│                                              │
│  Smart Library                              │
│  └── Curated reference content              │
│                                              │
│  News & Trends                              │
│  ├── Seasonal events                        │
│  └── Current trends                         │
└─────────────────────────────────────────────┘
```

### Step 2: Draft Generation (Pass 1)

- **Temperature:** 0.7 (creative but controlled)
- **Focus:** Content substance over style
- **Input:** Full context + topic/prompt

### Step 3: Style Polish (Pass 2)

Only for `mystyle` tone mode:

- **Temperature:** 0.4 (precise, style-focused)
- **Input:** Draft from Pass 1 + style-specific instructions
- **Task:** Rewrite to match author's voice exactly

### Step 4: Quality Audit

Automated checks on the generated text:

| Check | Threshold | Action |
|-------|-----------|--------|
| Clickbait words | > 2 detected | Flag + suggest alternatives |
| Word repetition | > 15% ratio | Flag + suggest synonyms |
| Length compliance | ±20% of target | Flag if out of range |
| Readability | Avg sentence > 25 words | Simplify |
| Vocabulary diversity | Unique words < 40% | Enrich |

### Step 5: Anti-AI Cleanup

Rule-based pattern removal:

```
Input:  "В современном мире цифровизации, давайте разберёмся—
         как именно контент может трансформировать ваш бренд..."

Output: "Разберёмся, как контент работает на бренд..."
```

### Step 6: Feedback Loop

Every generation is tracked:

```typescript
GenerationAccuracy {
  totalGenerated: 142
  totalAccepted: 89      // User kept the text as-is or with minor edits
  totalRejected: 23      // User discarded
  totalModified: 30      // User made significant changes
  recentAccuracy: 0.67   // Rolling window accuracy
}
```

The system uses this to:
- Adjust temperature for the user
- Weight certain style rules higher
- Identify which anti-AI patterns need strengthening

## Tone Modes in Detail

### mystyle
Maximum authenticity. Uses the full 7-pass analysis + RAG of user's past texts. Two-pass generation (draft → polish). Anti-AI cleanup is most aggressive.

### informative
Expert voice. Emphasizes data, statistics, structured arguments. Uses educational frameworks.

### inspiring
Emotional resonance. Higher emotional modulation (intensity 7-9). Uses storytelling frameworks.

### casual
Everyday conversation. Shorter sentences, more emoji, colloquialisms preserved.

### humorous
Irony and wit. Uses the user's humor style from Pass 5 analysis.

### professional
Business-ready. Formal structure, no slang, clear CTAs.

### neutral
Balanced baseline. No strong emotional coloring.

## Multi-Variant Generation

When enabled, generates 3 versions simultaneously:

```
Topic: "Новый альбом Radiohead"

Variant 1 (accurate):  Style match: 92 | Engagement: 71 | Original: 65
Variant 2 (engaging):  Style match: 78 | Engagement: 91 | Original: 72
Variant 3 (creative):  Style match: 65 | Engagement: 79 | Original: 94

Weighted scores:
  V1: 92×0.3 + 71×0.25 + 65×0.2 + ... = 76.4
  V2: 78×0.3 + 91×0.25 + 72×0.2 + ... = 79.1  ← Winner
  V3: 65×0.3 + 79×0.25 + 94×0.2 + ... = 75.8
```

## Content Frameworks

Each framework shapes the structure differently:

### hook-story-payoff
```
[Hook]  → Attention-grabbing first line
[Story] → Narrative with tension
[Payoff] → Lesson or call to action
```

### problem-solution
```
[Problem]  → Pain point the audience feels
[Solution] → How to fix it
```

### behind-scenes
```
[Setup]    → What people normally see
[Reveal]   → What actually happens
[Insight]  → Why it matters
```

## Emotional Modulation

The generation prompt includes emotional instructions:

```typescript
const modulatedPrompt = `
  Base mood: ${mood}          // e.g., "energetic"
  Target emotion: ${target}   // e.g., "inspire"
  Intensity: ${intensity}/10  // e.g., 8

  Adjust the emotional tone accordingly.
  Use vocabulary and rhythm that evoke ${target}
  at intensity level ${intensity}.
`;
```

This affects:
- Word choice (stronger vs softer)
- Sentence rhythm (short/punchy vs flowing)
- Punctuation (!!! vs .)
- Emoji density
