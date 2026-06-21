---
name: xpoz-best-practices
description: "Reference guide for using Xpoz effectively via MCP. Covers query syntax (boolean operators, date filtering), response modes (fast/paging/CSV), field selection, tracking setup, and all platform tool references (Twitter, Instagram, Reddit, TikTok). Load for ANY Xpoz interaction, not just explicit best-practices questions."
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
            "credentials": "Xpoz account (free tier) — auth via xpoz-setup skill (OAuth 2.1)",
          },
        "install": [{"id": "node", "kind": "node", "package": "mcporter", "bins": ["mcporter"], "label": "Install mcporter (npm)"}],
      },
  }
tags:
  - best-practices
  - reference
  - query-syntax
  - pagination
  - field-selection
  - csv-export
  - social-media
  - twitter
  - instagram
  - reddit
  - tiktok
  - mcp
  - xpoz
  - social-intelligence
  - tracking
  - boolean-search
---

# Xpoz Best Practices

**Reference guide for using Xpoz MCP tools effectively.**

Covers query syntax, field selection, response modes, tracking, and all 40 platform tools across Twitter, Instagram, Reddit, and TikTok.

## Setup

Run `xpoz-setup` skill. Verify: `mcporter call xpoz.checkAccessKeyStatus`

## Quick Start

```bash
mcporter call xpoz.getTwitterPostsByKeywords query='"artificial intelligence"' fields='["id","text","authorUsername","likeCount"]'
```

## Query Syntax

All keyword search tools support boolean query syntax:

| Operator | Example | Effect |
|----------|---------|--------|
| Exact phrase | `"machine learning"` | Matches exact phrase |
| OR | `"AI" OR "artificial intelligence"` | Matches either term |
| AND | `"Tesla" AND "earnings"` | Matches both terms |
| Grouping | `("deep learning" OR "neural network") AND python` | Combines operators |

**Date filtering:** Use `startDate` / `endDate` in YYYY-MM-DD format. Omit to use defaults (varies by tool).

**Content filtering** (Twitter only): Set `filterOutRetweets=true` to exclude retweets.

**Forbidden in query string:** `from:`, `to:`, `lang:`, `since:`, `until:`, `filter:` — use dedicated parameters instead.

## Platform Quick Reference

### Twitter/X (13 tools)

| Tool | Purpose |
|------|---------|
| `getTwitterUser` / `getTwitterUsers` | Look up 1-100 users by ID or username |
| `searchTwitterUsers` | Fuzzy search users by name |
| `getTwitterUserConnections` | Get followers or following |
| `getTwitterUsersByKeywords` | Find users who posted about a topic |
| `getTwitterPostsByIds` | Get 1-100 posts by ID |
| `getTwitterPostsByAuthor` | Get all posts from a username |
| `getTwitterPostsByKeywords` | Search posts by keywords |
| `getTwitterPostRetweets` | Get retweets of a post |
| `getTwitterPostQuotes` | Get quote tweets of a post |
| `getTwitterPostComments` | Get replies to a post |
| `getTwitterPostInteractingUsers` | Get commenters, quoters, or retweeters |
| `countTweets` | Count tweets matching a phrase |

### Instagram (9 tools)

| Tool | Purpose |
|------|---------|
| `getInstagramUser` | Look up user by ID or username |
| `searchInstagramUsers` | Fuzzy search users by name |
| `getInstagramUserConnections` | Get followers or following |
| `getInstagramUsersByKeywords` | Find users who posted about a topic |
| `getInstagramPostInteractingUsers` | Get commenters or likers of a post |
| `getInstagramPostsByIds` | Get posts by strong_id |
| `getInstagramPostsByUser` | Get posts from a user |
| `getInstagramPostsByKeywords` | Search posts by keywords in captions/subtitles |
| `getInstagramCommentsByPostId` | Get comments on a post |

**Note:** Instagram post IDs use `strong_id` format: `{media_id}_{user_id}`.

### Reddit (9 tools)

| Tool | Purpose |
|------|---------|
| `getRedditUser` | Look up user by username |
| `searchRedditUsers` | Fuzzy search users by name |
| `getRedditUsersByKeywords` | Find users who posted about a topic |
| `getRedditPostsByKeywords` | Search posts by keywords |
| `getRedditPostWithCommentsById` | Get a post with all its comments |
| `getRedditCommentsByKeywords` | Search comments by keywords |
| `searchRedditSubreddits` | Search subreddits by name |
| `getRedditSubredditWithPostsByName` | Get subreddit details with posts |
| `getRedditSubredditsByKeywords` | Search subreddits by keyword in description |

### TikTok (9 tools)

| Tool | Purpose |
|------|---------|
| `getTiktokUser` | Look up user by ID or username |
| `searchTiktokUsers` | Fuzzy search users by name |
| `getTiktokUsersByKeywords` | Find users who posted about a topic |
| `getTiktokUsersByHashtags` | Find users who used specific hashtags |
| `getTiktokPostsByIds` | Get posts by ID |
| `getTiktokPostsByUser` | Get posts from a user |
| `getTiktokPostsByKeywords` | Search posts by keywords |
| `getTiktokPostsByHashtags` | Search posts by hashtags |
| `getTiktokCommentsByPostId` | Get comments on a post |

**Hashtag rules:** Bare strings (no `#` prefix), alphanumeric only, 1-5 per request.

## Tracking

Setting up tracking is a best practice for getting more complete data. Tracked items are crawled regularly in the background, which means better coverage and more complete data over time.

| Platform | keyword | user | subreddit | hashtag |
|----------|---------|------|-----------|---------|
| Twitter | Yes | Yes | — | — |
| Instagram | Yes | Yes | — | — |
| Reddit | Yes | Yes | Yes | — |
| TikTok | Yes | Yes | — | Yes |

```bash
mcporter call xpoz.getTrackedItems
mcporter call xpoz.addTrackedItems items='[{"phrase":"AI agents","type":"keyword","platform":"twitter"}]'
mcporter call xpoz.removeTrackedItems items='[{"phrase":"AI agents","type":"keyword","platform":"twitter"}]'
```

See [xpoz-social-tracking](https://clawhub.ai/skills/xpoz-social-tracking) for full tracking workflows.

## Response Modes

All paginated tools support three response modes via `responseType`:

| Mode | Behavior | Best For |
|------|----------|----------|
| `"fast"` (default) | Returns up to 300 results immediately | Quick lookups, exploration |
| `"paging"` | Async — returns `operationId`, poll with `checkOperationStatus` | Large datasets, page-by-page |
| `"csv"` | Async CSV export to S3 — returns download URL | Bulk export, offline analysis |

**Async polling pattern:**

```bash
mcporter call xpoz.getTwitterPostsByKeywords query='"AI agents"' responseType="paging"
mcporter call xpoz.checkOperationStatus operationId="op_..."  # Poll every 5s until status != "running"
```

For CSV export, use `dataDumpExportOperationId` from the response, then poll `checkOperationStatus` for the download URL.

## Field Selection

Pass `fields` to request only the data you need:

```bash
mcporter call xpoz.getTwitterPostsByKeywords query='"AI"' fields='["id","text","authorUsername","likeCount"]'
```

Each platform has different available fields — check tool responses for the full list.

## Common Patterns

**Search → Analyze → Export:**
1. Search posts by keywords (fast mode) to preview results
2. Analyze engagement, sentiment, or themes
3. Export full dataset to CSV for deeper analysis

**Find Users → Get Their Posts → Analyze:**
1. Search users by keywords to find relevant accounts
2. Get posts by author for top accounts
3. Analyze content patterns, posting frequency, engagement

**Data Freshness:**
- Data is cached with automatic API fallback when stale — results are kept fresh automatically
- Use `forceLatest=true` to bypass cache and force a live fetch (increases latency and cost)

## Troubleshooting

| Problem | Solution |
|---------|----------|
| "Unauthorized" | Re-run `xpoz-setup` skill |
| Empty results | Check query syntax, widen date range, try different keywords |
| Stale data | Use `forceLatest=true` to bypass cache |
| Operation timeout | Keep polling `checkOperationStatus` every ~5s until status is no longer `running` |
