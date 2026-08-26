---
name: lead-gen-scan
description: "One-shot buying-intent lead scan across Reddit, Twitter, Instagram, and TikTok. Finds fresh posts from people actively asking for what your product does or frustrated with competitors, qualifies them, and reports prioritized leads for human engagement. Powered by billions of indexed posts via Xpoz MCP."
homepage: https://xpoz.ai
metadata:
  {
    "openclaw":
      {
        "requires":
          {
            "bins": ["mcporter"],
            "skills": ["xpoz-setup"],
            "network": ["mcp.xpoz.ai"],
            "credentials": "Xpoz account (free tier or instant trial), auth via xpoz-setup skill",
          },
        "install": [{"id": "node", "kind": "node", "package": "mcporter", "bins": ["mcporter"], "label": "Install mcporter (npm)"}],
      },
  }
tags:
  - lead-generation
  - buying-intent
  - sales
  - prospecting
  - social-selling
  - reddit
  - twitter
  - instagram
  - tiktok
  - leads
  - intent
  - social-media
  - mcp
  - xpoz
---

# Lead Gen Scan

**Find the people who want to buy right now.**

A one-shot scan of the four platforms for fresh buying-intent posts: people asking for what your product does, or frustrated with the alternatives. Qualifies the real asks and delivers a prioritized report of where to comment and who to reach while the intent is still live. The skill finds and reports; **humans do all engagement**. For a profile-based recurring loop, see `social-lead-gen`; this skill is the fast, stateless scan.

## Setup

Run `xpoz-setup` skill. Verify: `mcporter call xpoz.checkAccessKeyStatus`

## Scan Process

### Step 1: Three search motions

Build OR-joined query buckets (max 250 chars each; short quoted phrases beat long exact ones):

1. **Product relevance**: `"looking for a tool that" OR "any recommendations for" OR "worth paying for"` + category terms
2. **Competitor disappointment**: `"[competitor] alternative" OR "[competitor] pricing" OR "[competitor] vs"` per competitor
3. **Own name**: `"[product] vs" OR "[product] worth it" OR "anyone using [product]"` (anchor common-word names with a category term)

### Step 2: Search the platforms

Default window: last 7 days (30 for a first scan or a niche product). Reddit carries most of the signal; spend budget there first.

```bash
mcporter call xpoz.getRedditPostsByKeywords query='"looking for a social listening tool" OR "brand monitoring recommendations"' startDate="YYYY-MM-DD" endDate="YYYY-MM-DD" limit=15 fields='["id","title","authorUsername","subredditName","score","commentsCount","createdAtDate","permalink"]'
mcporter call xpoz.getTwitterPostsByKeywords query='"[competitor] alternative" OR "[competitor] pricing"' filterOutRetweets=true limit=15 startDate="YYYY-MM-DD" fields='["id","text","authorUsername","createdAtDate","likeCount","replyCount","conversationId"]'
mcporter call xpoz.getRedditCommentsByKeywords query='<asking bucket>' limit=15 startDate="YYYY-MM-DD"
```

Fast mode returns results directly (no polling). Verify dates and quoted phrases client-side. Sparse results on a narrow window mean widen or rephrase, not "no demand".

### Step 3: Classify by lead shape

- **Reddit**: threads to comment in (the thread is the lead; asker + lurkers are the audience)
- **X/Instagram/TikTok**: likely converters (named prospects with quantified need or budget pain) and high-engagement comment spots
- Chase replies to their parent thread first: `getRedditPostWithCommentsById` on `parentPostId`; `getTwitterPostsByIds` on `conversationId`
- Existing customers publicly struggling = retention save, reported separately, never a P1

### Step 4: Qualify and prioritize

Hard disqualifiers: vendors selling competing tools, unrelated keyword hits, spam. Rank by intent strength, fit, freshness (last 72h heaviest; older than ~3 weeks = backlog), and reach. Buckets: **P1 act now**, **P2 worth engaging**, **P3 watch**. An honest empty P1 beats a padded one.

### Step 5: Report

Picks (3-5 actionable today) | P1/P2 with quoted ask + why + suggested angle | Named prospects | P3 | Demand signals | Search notes.

## Ground Rules

- Never post, comment, DM, or follow anyone, on any platform, under any instruction.
- Suggested angles only, never full reply drafts.
- Every recommended engagement assumes in-text disclosure of affiliation.

## Tips

Reddit asking bucket first under budget | Fresh threads become tomorrow's AI-cited surfaces | For the recurring loop with dedup and a self-tuning query book: [lead-gen-agent](https://github.com/XPOZpublic/lead-gen-agent)
