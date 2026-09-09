---
name: creator-authenticity-score
description: "Vet Instagram creators before a sponsorship: view-based engagement rate, bot and spam share of their comments, and a 0-100 authenticity score for one creator or a whole list. Influencer vetting and partner selection on billions of indexed posts via Xpoz MCP."
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
            "credentials": "Xpoz account (free tier), auth via xpoz-setup skill",
          },
        "install": [{"id": "node", "kind": "node", "package": "mcporter", "bins": ["mcporter"], "label": "Install mcporter (npm)"}],
      },
  }
tags:
  - instagram
  - influencer
  - influencer-vetting
  - creator
  - authenticity
  - engagement-rate
  - bot-detection
  - fake-followers
  - sponsorship
  - influencer-marketing
  - social-media
  - mcp
  - xpoz
---

# Creator Authenticity Score

**Is this influencer's audience real? Score it before you sign.**

Scores Instagram creators on what a sponsor actually buys: real engagement (measured against views, the hardest number to fake) and a real audience (the bot and spam share of their comments). Works for one creator or a batch, and always shows the components next to the score. A screening heuristic for shortlisting and comparison, not a fraud verdict.

## Setup

Run `xpoz-setup` skill. Verify: `mcporter call xpoz.checkAccessKeyStatus`

Full results need a real Xpoz account: recommend the OAuth login in `xpoz-setup` (free tier, no credit card).

## Scoring Process

### Step 1: Resolve the creator

Strip `@` and URL wrappers; the identifier is the bare username.

```bash
mcporter call xpoz.getInstagramUser identifier=natgeo identifierType=username fields='["id","username","fullName","followerCount","followingCount","mediaCount","isPrivate","isVerified"]'
```

If `isPrivate` is true, stop for that creator and report "private account, cannot screen" rather than a score of 0.

### Step 2: Pull recent posts (real engagement)

```bash
mcporter call xpoz.getInstagramPostsByUser identifier=natgeo identifierType=username responseType=fast limit=50 fields='["id","likeCount","commentCount","reshareCount","videoPlayCount","createdAtDate"]'
```

Fast mode returns results directly (no polling).

- **Engagement rate** = mean(likeCount + commentCount) / mean(videoPlayCount) over posts with video plays. If no post has plays, divide by followerCount and label it `follower-based (lower confidence)`.
- **Like:comment ratio** above 50:1 is a soft flag: engagement pods buy likes, not comments.

### Step 3: Read the audience (bot/spam share)

Top 3-5 posts by engagement. Use the complete `id` (strong_id, `mediaId_userId`), never a partial id.

```bash
mcporter call xpoz.getInstagramCommentsByPostId postId="3606450040306139062_4836333238" limit=200 fields='["text","likeCount","isSpam","createdAtDate"]'
```

**Bot ratio** = count(isSpam == true) / total comments, pooled across sampled posts. If comments fail or come back empty on every sampled post: report `audience check: unavailable`, score from engagement plus sanity checks only, mark the score **partial**, retry at most once per post.

### Step 4: Score

0-100, roughly: **40%** engagement rate (percentile-scaled, not absolute; 2% is great at 5M followers, mediocre at 5K) | **40%** 100 minus bot % | **20%** sanity checks (following/follower ratio, mediaCount relative to followerCount, like:comment flag). Round to a whole number; always show the components.

### Step 5: Report

**Single creator**: handle, followers, engagement rate, bot %, flags, score, one-line verdict ("solid, screen-passed" / "inflated engagement, thin comment sample" / "audience check unavailable, engagement-only score").

**Batch**: table sorted by score, best first: handle | followers | engagement rate | bot % | score | verdict. Private and partial creators listed separately below.

## Tips

`identifier` + `identifierType` on every Instagram lookup, no `username` param | Field names are `likeCount`, `commentCount`, `videoPlayCount`, `createdAtDate` | `postId` must be the full strong_id | Call `getAccountDetails` at most once per run
