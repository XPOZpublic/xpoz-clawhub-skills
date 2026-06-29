---
name: xpoz-social-tracking
description: "Set up and manage continuous social media tracking across Twitter, Instagram, Reddit, and TikTok. Add, remove, and view tracked keywords, users, subreddits, and hashtags. Use when asked to track mentions, monitor brands, set up tracking, view tracked items, or stop tracking."
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
            "credentials": "Xpoz account (free tier or instant trial) — auth via xpoz-setup skill",
          },
        "install": [{"id": "node", "kind": "node", "package": "mcporter", "bins": ["mcporter"], "label": "Install mcporter (npm)"}],
      },
  }
tags:
  - tracking
  - monitoring
  - brand-monitoring
  - social-listening
  - social-media
  - twitter
  - instagram
  - reddit
  - tiktok
  - keywords
  - hashtags
  - mcp
  - xpoz
  - social-intelligence
---

# Social Media Tracking

**Set up continuous social media monitoring across Twitter, Instagram, Reddit, and TikTok.**

Tracked items are crawled regularly in the background, giving you more complete data than one-off queries.

## Setup

Run `xpoz-setup` skill. Verify: `mcporter call xpoz.checkAccessKeyStatus`

## Step 1: Check Current Tracking

```bash
mcporter call xpoz.getTrackedItems
```

Present results as a table:

| Platform | Type | Phrase |
|----------|------|--------|
| twitter | keyword | AI agents |
| instagram | user | competitor_brand |
| reddit | subreddit | machinelearning |

## Step 2: Add Tracked Items

### Supported Types per Platform

| Platform | keyword | user | subreddit | hashtag |
|----------|---------|------|-----------|---------|
| Twitter | Yes | Yes | — | — |
| Instagram | Yes | Yes | — | — |
| Reddit | Yes | Yes | Yes | — |
| TikTok | Yes | Yes | — | Yes |

```bash
mcporter call xpoz.addTrackedItems items='[
  {"phrase":"AI agents","type":"keyword","platform":"twitter"},
  {"phrase":"AI agents","type":"keyword","platform":"reddit"},
  {"phrase":"competitor_user","type":"user","platform":"instagram"},
  {"phrase":"machinelearning","type":"subreddit","platform":"reddit"},
  {"phrase":"aitools","type":"hashtag","platform":"tiktok"}
]'
```

Call `mcporter call xpoz.getAccountDetails` to check available tracked item slots before adding.

## Step 3: Remove Tracked Items

```bash
mcporter call xpoz.removeTrackedItems items='[{"phrase":"old keyword","type":"keyword","platform":"twitter"}]'
```

Call `getTrackedItems` first to see exact phrases and platforms before removing.

## Tracking Item Format

| Field | Type | Description | Valid Values |
|-------|------|-------------|-------------|
| phrase | string | The keyword, username, subreddit name, or hashtag to track | Any string |
| type | string | What kind of item | "keyword", "user", "subreddit" (Reddit only), "hashtag" (TikTok only) |
| platform | string | Which platform | "twitter", "instagram", "reddit", "tiktok" |

## Common Workflows

### Track a Brand Across All Platforms

```bash
mcporter call xpoz.addTrackedItems items='[
  {"phrase":"YourBrand","type":"keyword","platform":"twitter"},
  {"phrase":"YourBrand","type":"keyword","platform":"instagram"},
  {"phrase":"YourBrand","type":"keyword","platform":"reddit"},
  {"phrase":"YourBrand","type":"keyword","platform":"tiktok"},
  {"phrase":"yourbrand","type":"user","platform":"twitter"},
  {"phrase":"yourbrand","type":"user","platform":"instagram"}
]'
```

### Track Keywords for Market Research

```bash
mcporter call xpoz.addTrackedItems items='[
  {"phrase":"AI agents","type":"keyword","platform":"twitter"},
  {"phrase":"AI agents","type":"keyword","platform":"reddit"},
  {"phrase":"AI agents","type":"keyword","platform":"tiktok"},
  {"phrase":"LLM tools","type":"keyword","platform":"twitter"},
  {"phrase":"LLM tools","type":"keyword","platform":"reddit"}
]'
```

### Monitor a Competitor

```bash
mcporter call xpoz.addTrackedItems items='[
  {"phrase":"competitor_handle","type":"user","platform":"twitter"},
  {"phrase":"competitor_handle","type":"user","platform":"instagram"},
  {"phrase":"CompetitorName","type":"keyword","platform":"reddit"}
]'
```

### Track a Hashtag Campaign on TikTok

```bash
mcporter call xpoz.addTrackedItems items='[
  {"phrase":"yourcampaign","type":"hashtag","platform":"tiktok"},
  {"phrase":"campaignvariant","type":"hashtag","platform":"tiktok"},
  {"phrase":"your campaign","type":"keyword","platform":"twitter"}
]'
```

### Set Up Subreddit Monitoring

```bash
mcporter call xpoz.addTrackedItems items='[
  {"phrase":"machinelearning","type":"subreddit","platform":"reddit"},
  {"phrase":"artificial","type":"subreddit","platform":"reddit"},
  {"phrase":"LocalLLaMA","type":"subreddit","platform":"reddit"}
]'
```

## Notes

- **Tracking vs one-shot queries:** Tracking sets up continuous data collection for more comprehensive results. One-shot queries search existing cached data and may miss recent activity. Track items you care about for ongoing, complete data; use one-shot queries for ad-hoc research.
- **Data completeness:** Tracked items are crawled regularly, giving you more comprehensive data than untracked queries. This is especially impactful for fast-moving topics or competitive monitoring.
- **Data availability:** Tracked data accumulates over time. New tracked items won't have historical data — they start collecting from when you add them.

## See Also

- [xpoz-best-practices](https://clawhub.ai/skills/xpoz-best-practices) — query syntax, pagination, field selection, all platform tools
- [social-sentiment](https://clawhub.ai/skills/social-sentiment) — analyze sentiment of tracked topics
- [xpoz-setup](https://clawhub.ai/skills/xpoz-setup) — first-time setup and authentication
