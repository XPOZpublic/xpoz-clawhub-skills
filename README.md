# Xpoz ClawHub Skills

OpenClaw skills for Xpoz — the social intelligence platform for AI agents.

## Quick Install

```bash
clawhub install xpoz-setup
```

## Available Skills

| Skill | Description |
|-------|-------------|
| **xpoz-setup** | Set up and authenticate Xpoz MCP |
| **xpoz-best-practices** | Reference guide — query syntax, response modes, field selection, all tools |
| **xpoz-social-tracking** | Track keywords, users, subreddits, hashtags across platforms |
| **xpoz-social-search** | Search Twitter, Instagram, TikTok, Reddit |
| **reddit-api** | Reddit search and data |
| **instagram-search** | Instagram search |
| **social-sentiment** | Brand/topic sentiment analysis |
| **social-lead-gen** | Lead generation from social |
| **expert-finder** | Find influencers/thought leaders |
| **twitter-api-alternative** | Twitter API alternative |
| **social-intelligence** | General social data intelligence |

## Setup

**Instant access (no signup):**

```bash
TOKEN=$(curl -s -X POST https://api.xpoz.ai/api/trial/token | jq -r .data.accessKey)
mcporter config add xpoz https://mcp.xpoz.ai/mcp --header "Authorization=Bearer $TOKEN"
```

Trial tokens return up to 5 results per call, valid for 5 days. For full access, run `xpoz-setup`:

**Full access:** All skills require Xpoz MCP. Run `xpoz-setup` first:

```bash
clawhub install xpoz-setup
mcporter config add xpoz https://mcp.xpoz.ai/mcp --auth oauth
mcporter config login xpoz
```

## Documentation

- [xpoz.ai](https://xpoz.ai)
- [ClawHub](https://clawhub.ai/atyachin)

## License

MIT