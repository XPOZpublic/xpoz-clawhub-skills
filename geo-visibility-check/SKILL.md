---
name: geo-visibility-check
description: "One-shot GEO audit: does your brand appear in Claude, ChatGPT, and Gemini answers for the buyer questions that matter? Runs a prompt panel through the engines with citation tracing and reports per-prompt verdicts, who wins instead, and which sources the answers come from."
homepage: https://xpoz.ai
metadata:
  {
    "openclaw":
      {
        "requires":
          {
            "bins": ["mcporter", "python3"],
            "skills": ["xpoz-setup", "ai-answer-trace"],
            "network": ["mcp.xpoz.ai", "api.anthropic.com", "api.openai.com", "generativelanguage.googleapis.com"],
            "credentials": "AI engine API keys (any of ANTHROPIC_API_KEY / OPENAI_API_KEY / GEMINI_API_KEY) for tracing; Xpoz account for demand research",
          },
        "install": [{"id": "node", "kind": "node", "package": "mcporter", "bins": ["mcporter"], "label": "Install mcporter (npm)"}],
      },
  }
tags:
  - geo
  - generative-engine-optimization
  - ai-visibility
  - ai-citations
  - brand-monitoring
  - claude
  - chatgpt
  - gemini
  - ai-search
  - marketing
  - seo
  - mcp
  - xpoz
---

# GEO Visibility Check

**Do AI assistants recommend your brand? Find out, with evidence.**

Buyers increasingly start, and often finish, research inside AI engines. This skill runs the buyer questions that matter through Claude, ChatGPT, and Gemini with full citation tracing, and reports where the brand appears, who wins instead, and which surfaces the answers are assembled from. One-shot by design: the measurement pass that tells you whether a full GEO program is worth running.

## Setup

Run `xpoz-setup` for Xpoz access. Engine tracing needs AI API keys (any one is enough to start): `ANTHROPIC_API_KEY`, `OPENAI_API_KEY`, `GEMINI_API_KEY`. The `ai-answer-trace` skill provides the trace scripts.

## Audit Process

### Step 1: Build the prompt panel

3-8 questions buyers actually ask assistants: "best [category] tool for [persona]", "how do I [job to be done]", "[incumbent] alternatives". Mine real phrasings via Xpoz:

```bash
mcporter call xpoz.getRedditPostsByKeywords query='"looking for" OR "recommend" OR "best tool"' limit=15
```

### Step 2: Trace every prompt

Run each prompt through every configured engine, 2 samples each (answers are nondeterministic; one sample is a coin flip). Use the `ai-answer-trace` scripts; keep the JSON traces.

### Step 3: Judge per prompt

- **Present**: recommended outright, listed among options, mentioned in passing, or absent?
- **Cited**: does a brand-owned URL appear in the citations? An answer can name the brand while citing someone else's page; record the actual path.
- **Winners**: who is recommended instead, via which URLs?
- **Stability**: consistent across samples or not?

### Step 4: Report

Verdict | Per-prompt table (Claude / ChatGPT / Gemini / who wins) | Cited-domain ranking with surface types (own site, review site, community, docs, listicle) | Brand citation paths | Lost prompts diagnosed | Next steps.

## Tips

Keep traces for a before/after re-run next month | If community citations dominate lost prompts, run `geo-reddit` next | For the full weekly program (tracked panel, snapshots, gap analysis, content production): [geo-seo-agent](https://github.com/XPOZpublic/geo-seo-agent)
