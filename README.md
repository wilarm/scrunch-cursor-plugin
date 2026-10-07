# Scrunch

Optimize your brand for AI search: monitor how AI talks about you, and close the gaps.

Cursor plugin that connects agents to [Scrunch](https://scrunch.com) through Scrunch's official hosted [Model Context Protocol](https://modelcontextprotocol.io/) server.

See how AI platforms such as ChatGPT, Claude, Perplexity, Gemini, Google AI Overviews, Google AI Mode, Microsoft Copilot, Meta, and Grok mention, rank, and cite your brand and your competitors. You can also read the raw AI responses, see the changes Scrunch's nightly signal detection finds, build Scrunch Explorer charts and dashboards, and manage brands, competitors, personas, and prompts, all from chat.

## Install

1. Open **Cursor Settings → Plugins**.
2. Search for **Scrunch**.
3. Click **Install**, then complete the Scrunch sign-in prompt.

Or run `/add-plugin scrunch` in chat.

## Requirements

- **Free to install.** The plugin itself costs nothing.
- **OAuth sign-in required.** You connect by signing in with your Scrunch account. There's no API key to manage.
- **A Scrunch account with at least View permissions** on the brands you want to query. Tools run with your own Scrunch brand permissions, so what you can change depends on your Scrunch role.

## MCP

```json
{
  "mcpServers": {
    "scrunch": {
      "url": "https://app.scrunchai.com/api/mcp",
      "auth": {
        "scopes": ["profile", "email", "offline_access"]
      }
    }
  }
}
```

Auth is OAuth with Dynamic Client Registration against Scrunch's normal sign-in. There is no API key, client ID, or client secret to configure. The `auth.scopes` block is required: without it, Cursor requests `openid`, which Scrunch's sign-in flow does not grant, and authentication stalls on "Waiting for callback…". If you hit that error, open the server's **Configure** dialog and click **Reload** to re-register.

## What agents can do

| Category | Capabilities |
| --- | --- |
| Metrics | Presence (mention rate), position, sentiment, and citation ownership over time, filterable by AI platform, country, persona, funnel stage, topic, tag, and branded vs. non-branded prompts |
| Custom queries | `query_metrics` GROUP BY-style queries over a closed metric vocabulary (share of voice, citation rate, average rank, and more), with period-over-period comparison. Curated query patterns and a metric glossary keep answers consistent with the Scrunch dashboard |
| Signals | Nightly, statistically tested level changes and trends in AI visibility, with narratives |
| Responses | Raw AI responses with full text, citations, and competitor evaluations |
| Explorer | Deep links to Scrunch Explorer charts and saved dashboards |
| Configuration | Brands, competitors, sub-brands, personas, prompts, stages, topics, and tags |
| Agent traffic | Import AI-crawler logs from CSV and export crawl data |
| Help | Search the Scrunch Help Center and developer docs |

The hosted server is the source of truth for tool names and schemas. As a read-only smoke test after connecting, ask: `List my Scrunch brands`.

## Skills

| Skill | What it does |
| --- | --- |
| `scrunch-visibility-report` | Builds a period-over-period AI visibility report for one brand: presence, position, sentiment, citations, and notable signals, with an Explorer link |
| `scrunch-competitor-share-of-voice` | Ranks tracked competitors by AI share of voice and mention rate, broken down by platform, and finds the prompts where competitors appear and the brand doesn't |
| `scrunch-citation-gaps` | Breaks citations down by owner and domain, finds the third-party and competitor sources AI platforms cite for non-branded prompts, and turns them into a prioritized outreach and content list |

All three skills are read-only. They never call create, update, archive, import, or dashboard-saving tools unless you explicitly ask.

## Notes

- Tool calls run as the Scrunch user who authorizes the connection and can't exceed that user's brand permissions.
- Some tools change data in Scrunch: they create or update brands, competitors, sub-brands, personas, and prompts, archive or restore them, change tags, topics, stages, and personas on prompts, import agent traffic, and save dashboards. Agents should confirm with you before calling them.
- Results are capped at 1,000 records per request. If you track a lot of prompts, filter by tag or date range.
- Sitemap and Page Audit data aren't available through MCP yet.

## Docs

- Scrunch MCP overview: https://developers.scrunch.com/mcp/overview
- Setup guide: https://developers.scrunch.com/mcp/setup
- Feature reference: https://developers.scrunch.com/mcp/tools
- Scrunch Agent Skills: https://developers.scrunch.com/mcp/skills/overview
- Server URL: https://app.scrunchai.com/api/mcp
- Status: https://status.scrunchai.com
- Privacy Policy: https://scrunch.com/privacy/
- Terms of Service: https://scrunch.com/terms/
- Support: support@scrunchai.com

## License

MIT
