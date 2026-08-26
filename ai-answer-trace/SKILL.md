---
name: ai-answer-trace
description: "Ask Claude, ChatGPT, and Gemini a question and capture the full evidence trail behind each answer: the search queries each engine ran, the pages it retrieved, and the sources it cited. The raw material of GEO measurement. Needs AI engine API keys, not an Xpoz account."
homepage: https://xpoz.ai
metadata:
  {
    "openclaw":
      {
        "requires":
          {
            "bins": ["python3"],
            "network": ["api.anthropic.com", "api.openai.com", "generativelanguage.googleapis.com"],
            "credentials": "Any of ANTHROPIC_API_KEY / OPENAI_API_KEY / GEMINI_API_KEY; each key enables that engine's trace",
          },
      },
  }
tags:
  - geo
  - ai-citations
  - ai-search
  - generative-engine-optimization
  - claude
  - chatgpt
  - gemini
  - citation-tracking
  - ai-visibility
  - measurement
  - marketing
  - xpoz
---

# AI Answer Trace

**Not just what the engines say: which sources made them say it.**

Asks the major AI engines a question exactly the way a real user would, with live web search on, and captures the complete evidence trail. Every trace records three layers: the answer, the search queries the engine silently ran with the pages each search retrieved, and the URLs the answer actually cited (with answer spans where the API provides them).

## Setup

Set whichever engine keys you have; each script runs independently, one key is enough to start:

```bash
export ANTHROPIC_API_KEY=...   # Claude trace
export OPENAI_API_KEY=...      # ChatGPT trace
export GEMINI_API_KEY=...      # Gemini trace
```

## Trace Process

### Step 1: Run the question per engine

Fetch the three self-contained trace scripts (Claude, ChatGPT, Gemini) from the [full skill source](https://github.com/XPOZpublic/xpoz-agent-skills/blob/main/skills/ai-answer-trace/SKILL.md), which carries them inline with per-engine dependency headers; save and run them locally. Each saves a structured JSON trace per run: answer, per-search queries and retrieved pages, cited URLs. Default 2 samples per engine (1 for a quick look, 3 for a stable read); phrase questions the way a real user would, not keyword-style.

### Step 2: Engine quirks, handled at capture time

- **Gemini**: grounding URLs are expiring Google redirects; the script resolves each to its real destination.
- **ChatGPT**: retrieved sources only appear when explicitly requested; `?utm_source=openai` suffixes are stripped from retrieved sources (cited URLs keep them; strip before exact-URL comparisons).
- **Claude**: citations scattered across content blocks are reassembled in answer order.

### Step 3: Aggregate cited domains

A deterministic aggregator counts cited domains across engines and samples.

### Step 4: Analyze

- **Presence**: does the brand appear in each answer, named early or as an afterthought?
- **Citation path**: which cited URL carried it there (own domain, directory, community thread, roundup)?
- **Winners**: which domains dominate, and what surface types are they?
- **Near-misses**: retrieved-but-never-cited pages show what the engine considered and rejected (Claude and ChatGPT; Gemini's retrieved set is essentially its cited set).
- **Stability**: sources recurring across samples are signal; one-sample citations are noise until they repeat.

## Tips

Keep the trace JSONs; re-runs against the same questions are real before/afters | Feeds `geo-visibility-check` (verdicts) and `geo-reddit` (cited-thread mining) | Weekly program: [geo-seo-agent](https://github.com/XPOZpublic/geo-seo-agent)
