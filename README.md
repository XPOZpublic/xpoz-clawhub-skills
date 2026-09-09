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
| **lead-gen-scan** | One-shot buying-intent lead scan across four platforms, prioritized for human engagement |
| **geo-visibility-check** | One-shot GEO audit: does AI recommend your brand, and who wins instead |
| **geo-reddit** | Reddit influence map: the subreddits and threads AI engines cite for your category |
| **ai-answer-trace** | Capture the searches, retrieved pages, and cited sources behind Claude, ChatGPT, and Gemini answers |
| **creator-authenticity-score** | Vet Instagram creators: view-based engagement rate, bot share of comments, and a 0-100 authenticity score |

## Setup

All skills require Xpoz MCP. Run `xpoz-setup` first:

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