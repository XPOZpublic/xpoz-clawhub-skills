---
name: social-lead-gen
description: "Lead generation from social media — find high-intent buyers in live Twitter, Instagram, and Reddit conversations. Auto-researches your product, generates targeted search queries, and discovers people actively looking for solutions you offer. Social selling and prospecting powered by 1.5B+ indexed posts."
homepage: https://xpoz.ai
metadata:
  {
    "openclaw":
      {
        "requires": {
          "bins": ["mcporter"],
          "skills": ["xpoz-setup"],
          "tools": ["web_search", "web_fetch"],
          "network": ["mcp.xpoz.ai"],
          "credentials": "Xpoz account (free tier) — auth via xpoz-setup skill (OAuth 2.1)"
        },
      },
  }
tags:
  - lead-generation
  - sales
  - prospecting
  - social-media
  - twitter
  - instagram
  - reddit
  - find-leads
  - social-selling
  - buyer-intent
  - outreach
  - growth
  - marketing
  - customer-discovery
  - leads
  - mcp
  - xpoz
  - leads
  - intent
  - discovery
---

# Social Lead Gen

**Find people who need your product — from what they're actually saying on social media.**

Unlike traditional lead gen tools that search company databases, this skill finds **high-intent leads from live conversations**. It discovers people actively expressing the problems your product solves across Twitter, Instagram, and Reddit — powered by 1.5B+ indexed posts via Xpoz MCP.

---

## ⚡ Prerequisites

1. **Xpoz MCP** must be configured and authenticated. Follow the [xpoz-setup](https://clawhub.ai/skills/xpoz-setup) skill.
2. **Web search and web fetch** tools must be available (included with OpenClaw).

Verify Xpoz is ready:
```bash
mcporter call xpoz.checkAccessKeyStatus
```
If not `hasAccessKey: true`, follow xpoz-setup first, then return here.

---

## How It Works

```
┌──────────────────┐    ┌──────────────────┐    ┌──────────────────┐
│  PHASE 1: LEARN  │ →  │ PHASE 2: SEARCH  │ →  │ PHASE 3: SCORE   │
│                  │    │                  │    │                  │
│ Research product │    │ Generate queries  │    │ Score by intent  │
│ Analyze website  │    │ Search Twitter    │    │ Rank leads       │
│ Find competitors │    │ Search Instagram  │    │ Write outreach   │
│ Map pain points  │    │ Search Reddit     │    │ Export results   │
│ Validate w/ user │    │ Poll for results  │    │ Track sent leads │
└──────────────────┘    └──────────────────┘    └──────────────────┘
     (one-time)             (repeatable)            (repeatable)
```

---

## Phase 1: Product Research (One-Time Setup)

**This phase builds deep context about the user's product. Run it once; the results are stored and reused.**

### Step 1: Ask the user for a reference

Ask the user:

> "What product or service do you want to find leads for? Give me a reference — a website URL, GitHub repo, product description, or anything that describes what you offer."

The user may provide:
- A website URL (e.g., `https://example.com`)
- A GitHub repo (e.g., `https://github.com/org/repo`)
- A product name + description
- Multiple references

### Step 2: Deep research

For each reference provided, gather as much context as possible:

**For websites:**
- Use `web_fetch` to read the homepage, pricing page, about page, docs
- Use `web_search` to find reviews, mentions, comparisons, press coverage

**For GitHub repos:**
- Use `web_fetch` to read the README
- Check stars, description, topics
- Use `web_search` for mentions, discussions, blog posts about it

**For product names:**
- Use `web_search` to find the product website, reviews, alternatives
- Then fetch and analyze the results

**Extract and organize:**

```json
{
  "product": {
    "name": "",
    "website": "",
    "tagline": "",
    "description": "",
    "category": "",
    "pricing": ""
  },
  "value_proposition": {
    "what_it_does": "",
    "key_features": [],
    "differentiators": []
  },
  "target_audience": {
    "primary_icp": "",
    "segments": [
      {
        "name": "",
        "description": "",
        "pain_points": [],
        "where_they_hang_out": {
          "subreddits": [],
          "hashtags": [],
          "communities": []
        }
      }
    ]
  },
  "pain_points_solved": [],
  "competitors": [
    {
      "name": "",
      "website": "",
      "how_different": ""
    }
  ],
  "social_proof": {
    "testimonials": [],
    "case_studies": [],
    "notable_customers": []
  },
  "keywords": {
    "product_terms": [],
    "pain_point_terms": [],
    "competitor_terms": [],
    "industry_terms": []
  }
}
```

### Step 3: Validate with the user

Present your findings in a clear summary:

> "Here's what I learned about your product:
>
> **[Product Name]** — [tagline]
>
> **What it does:** [description]
>
> **Target audience:** [segments]
>
> **Pain points you solve:**
> - [pain point 1]
> - [pain point 2]
>
> **Competitors:** [list]
>
> **Key differentiators:** [list]
>
> Does this look right? Anything I should add or correct?"

**WAIT for the user to confirm or correct.** Do not proceed until they approve.

If the user corrects something, update the profile and re-validate.

### Step 4: Generate search queries

Based on the validated profile, generate targeted search queries for each platform:

**Query categories:**
1. **Pain point queries** — People expressing the problems the product solves
2. **Competitor frustration queries** — People complaining about competitors
3. **Tool/solution seeking queries** — People actively looking for what the product offers
4. **Industry discussion queries** — People in the target audience discussing relevant topics

**For each query, specify:**
- Platform (Twitter, Instagram, Reddit)
- Query string (using boolean operators)
- Target subreddits (for Reddit)
- Minimum engagement thresholds
- Lookback period

Generate 4-6 queries per platform (12-18 total).

### Step 5: Store the profile

Save the validated profile and generated queries:

```bash
mkdir -p data/social-lead-gen
# Save product-profile.json and search-queries.json
```

Present the generated queries to the user:

> "I've generated [N] search queries across Twitter, Instagram, and Reddit. Here are a few examples:
>
> **Twitter (pain points):** `"[query example]"`
> **Reddit (tool seeking):** `"[query example]"` in r/[subreddit]
>
> Ready to search for leads?"

---

## Phase 2: Lead Discovery (Repeatable)

**Run this phase whenever you want fresh leads. Uses the stored profile and queries.**

### Step 1: Load profile

```bash
cat data/social-lead-gen/product-profile.json
cat data/social-lead-gen/search-queries.json
```

If these files don't exist, run Phase 1 first.

### Step 2: Execute searches

For each generated query, call the appropriate Xpoz MCP tool:

**Twitter:**
```bash
mcporter call xpoz.getTwitterPostsByKeywords \
  query="GENERATED_QUERY" \
  startDate="LOOKBACK_DATE" \
  limit=50 \
  fields='["id","text","authorUsername","likeCount","retweetCount","replyCount","impressionCount","createdAtDate"]'
```

**Instagram:**
```bash
mcporter call xpoz.getInstagramPostsByKeywords \
  query="GENERATED_QUERY" \
  startDate="LOOKBACK_DATE" \
  limit=50
```

**Reddit:**
```bash
mcporter call xpoz.getRedditPostsByKeywords \
  query="GENERATED_QUERY" \
  startDate="LOOKBACK_DATE" \
  limit=50
```

**Always poll for results:**
```bash
mcporter call xpoz.checkOperationStatus operationId="OPERATION_ID"
```

Poll every 5 seconds until status is `completed`.

### Step 3: Find people (not just posts)

For high-engagement posts, also search for the people behind them:

```bash
mcporter call xpoz.getTwitterUsersByKeywords \
  query="GENERATED_QUERY" \
  limit=50
```

This finds users who frequently post about the topic — potential repeat customers or influencers.

---

## Phase 3: Lead Scoring & Output

### Scoring Framework (1-10)

Score each lead based on signals from the product profile:

| Signal | Points | Example |
|--------|--------|---------|
| **Explicitly asking for a solution** | +3 | "Can anyone recommend a [product category]?" |
| **Complaining about a competitor** | +2 | "[Competitor] is too expensive / broken / limited" |
| **Has a project blocked by the pain point** | +2 | "I need [capability] but can't find a good tool" |
| **Active in target community** | +1 | Posts in relevant subreddits / uses relevant hashtags |
| **High engagement on the post** | +1 | >10 likes or >5 comments |
| **Recent post (< 48 hours)** | +1 | Time-sensitive opportunity |
| **Profile matches ICP** | +1 | Developer, marketer, researcher — matches target segment |
| **Selling a competing solution** | -3 | They're a competitor, not a lead |
| **Irrelevant context** | -2 | Mentioned keyword but in unrelated context |

**Tiers:**
- **Tier 1 (Score 8-10):** Hot leads — high intent, act fast
- **Tier 2 (Score 6-7):** Warm leads — worth engaging
- **Tier 3 (Score 5):** Watchlist — monitor for future intent
- **Below 5:** Skip

### Deduplication

Before reporting any lead, check against previously sent leads:

```bash
cat data/social-lead-gen/sent-leads.json
```

Key format: `{platform}:{author}:{post_id}`

After reporting, add to the tracking file.

### Output Format

For each lead, provide:

1. **Who:** Username, platform, profile summary
2. **What they said:** Direct quote from their post
3. **Post URL:** Clickable link
   - Twitter: `https://twitter.com/{username}/status/{id}`
   - Reddit: `https://www.reddit.com/r/{subreddit}/comments/{id}/`
   - Instagram: `https://www.instagram.com/p/{shortcode}/`
4. **Score:** X/10 with reasoning
5. **Why they're a fit:** Connection between their pain and your product
6. **Suggested outreach:** Pre-written reply customized to their situation
7. **Engagement:** Likes, comments, shares
8. **Posted:** Date and relative time

### Outreach Guidelines

When writing suggested replies:
- Reference their **exact** situation from the post
- Mention specific features of the product that solve their problem
- Keep it conversational, not salesy
- Be honest — if the product doesn't perfectly fit, say so
- **Always include a disclosure** if the user is affiliated with the product

Example:
> "I had the same problem! Ended up using [Product] — it does [specific capability they need]. [Brief differentiator]. Worth checking out: [URL]
>
> (Disclosure: I work with [Product])"

---

## Updating the Profile

If the user's product evolves, they can re-run Phase 1:

> "My product has changed — we now also support [X]. Can you update the profile?"

Re-run the research, validate, and regenerate queries. The stored profile is overwritten.

---

## Example Session

**User:** "Find leads for my product. Here's our website: https://example.com"

**Agent:**
1. Fetches and analyzes example.com (homepage, pricing, docs)
2. Searches for reviews, competitors, mentions
3. Presents findings: "Here's what I learned..."
4. User confirms: "Yes, but we also target enterprise customers"
5. Agent updates profile, generates 15 search queries
6. User says "Go find leads"
7. Agent runs queries across Twitter/Instagram/Reddit
8. Returns: "Found 12 leads — 3 Tier 1, 5 Tier 2, 4 Tier 3"
9. Each lead has URL, score, quote, outreach copy

**Next day:** "Find more leads" → Agent loads saved profile, runs fresh queries, deduplicates against yesterday's results.

---

## Responsible Use

- **Respect platform terms of service** for Twitter, Instagram, and Reddit
- **Don't spam** — outreach should be genuine and helpful
- **Disclose affiliations** in any outreach messaging
- **Respect privacy** — only use publicly available information
- **Quality over quantity** — 5 great leads beat 50 mediocre ones

---

## Resources

- **Xpoz:** [xpoz.ai](https://xpoz.ai) — social intelligence MCP powering the searches
- **Setup:** [xpoz-setup on ClawHub](https://clawhub.ai/skills/xpoz-setup) — one-time auth
- **Search reference:** [xpoz-social-search on ClawHub](https://clawhub.ai/skills/xpoz-social-search) — full search patterns

---

**Built for ClawHub • Powered by Xpoz**
