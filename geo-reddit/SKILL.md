---
name: geo-reddit
description: "Reddit influence map for AI visibility: which subreddits and threads Claude, ChatGPT, and Gemini actually cite for your category, where those buyer questions get asked, and which live threads are worth answering now. Recommends where and what; a human writes and posts everything."
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
            "credentials": "AI engine API keys for citation tracing (optional but primary evidence); Xpoz account for venue research",
          },
        "install": [{"id": "node", "kind": "node", "package": "mcporter", "bins": ["mcporter"], "label": "Install mcporter (npm)"}],
      },
  }
tags:
  - geo
  - reddit
  - ai-citations
  - ai-visibility
  - generative-engine-optimization
  - community-marketing
  - brand-presence
  - claude
  - chatgpt
  - gemini
  - marketing
  - mcp
  - xpoz
---

# GEO Reddit

**The subreddits and threads that actually move AI answers for your brand.**

Reddit is among the most-cited domains in AI answers, and a helpful comment in a thread the engines already cite inherits that thread's citation. This skill builds a ranked Reddit influence map from two evidence streams: the engines' own citations (which Reddit URLs they cite for your buyer questions, including answers competitors win) and live demand via Xpoz (where those questions get asked, which threads are answerable now).

## Setup

Run `xpoz-setup` for Xpoz access. Citation tracing needs AI engine API keys via the `ai-answer-trace` skill; with no engine access, the skill delivers the demand-side map and labels it as such.

## Mapping Process

### Step 1: Trace engine citations (primary evidence)

Run the brand's buyer prompts through the engines and record every cited Reddit URL: which prompt, which engine, cited or only retrieved, competitor named or not. Parse subreddits from the URLs; a subreddit cited on 3 prompts outranks one cited once.

### Step 2: Research the venues

```bash
mcporter call xpoz.getRedditPostsByKeywords query='"best [category] tool" OR "[incumbent] alternative"' limit=15 startDate="YYYY-MM-DD"
mcporter call xpoz.searchRedditSubreddits name="[category]"
mcporter call xpoz.getRedditSubredditWithPostsByName name="[subreddit]"
mcporter call xpoz.getRedditPostWithCommentsById postId="[id]"
```

### Step 3: Build the map

- **Cited threads**: engines already cite these; what would a winning answer add?
- **Influential subreddits**: ranked by citation instances, with subscriber counts
- **Live threads worth answering now**: fresh asks with real engagement
- **Watch list**: venues one signal away from mattering

### Step 4: Report

Verdict | Cited threads table | Influential subreddits | Live threads | Watch list | "Before anyone posts" notes (each venue's self-promotion rules, checked).

## Ground Rules

- A human writes and posts everything. The skill never posts, comments, or messages anyone.
- Brand-affiliated answers must disclose the affiliation in the post itself; a profile bio is not enough.
- If a subreddit bans vendor participation, the report says so instead of suggesting workarounds.
- Manufactured threads, vote manipulation, and sockpuppet accounts are off the table.

## Tips

Re-run after meaningful engagement: the point is watching venues move from "live thread" to "cited thread" | Pairs with `geo-visibility-check` (which prompts are lost) | Full program: [geo-seo-agent](https://github.com/XPOZpublic/geo-seo-agent)
