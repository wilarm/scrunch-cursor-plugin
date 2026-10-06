---
name: scrunch-visibility-report
description: Build a period-over-period AI visibility report for one Scrunch brand, covering presence (mention rate), share of voice, position, sentiment, citations, per-platform breakdown, weakest topics, and the notable signals Scrunch detected, with an Explorer link. Use when someone asks "how are we doing in AI search", "give me our AI visibility report", "what changed this month", or wants a weekly, monthly, or QBR summary for a brand. Read-only. Requires the Scrunch MCP server.
---

# Scrunch: AI visibility report

## When to use

- "How visible is our brand in ChatGPT, Perplexity, and the other AI platforms?"
- "Give me a monthly (or weekly, or quarterly) AI visibility report for <brand>."
- "What changed for us in AI search since last period?"

## Ground rules

- Every number comes from `query_metrics` or the `get_*_metrics` tools. Never count, average, or compute a rate from `list_responses`. That tool is only for quoting what an AI actually said.
- Before composing a query, call `list_query_patterns`. If a curated pattern answers the question, reuse it with `get_query_pattern` so the numbers match the Scrunch dashboard.
- If you're unsure what a metric means, check `get_metric_glossary` before explaining it. For example, presence counts name mentions, and a citation of the brand's domain does not count as presence.
- Always state the resolved date range and include `responses` next to every rate, so the denominator is real.
- Stay read-only. Do not call any create, update, archive, import, or `create_explorer_dashboard` tool unless the user explicitly asks.

## Steps

1. **Resolve the brand.** Call `get_caller_identity` to get the user's organization and accessible brands. If the user named a brand, match it, using `list_brands(organization_id, search=...)` if needed. If more than one brand matches, or none was named and several are accessible, ask which one to use.
2. **Anchor the dates.** Call `get_current_date`. Default to the last 30 full days compared with the 30 days before that, unless the user gave a period. Use explicit `start_date` and `end_date` values in YYYY-MM-DD format.
3. **Headline metrics.** Call `query_metrics` with `fields` = `brand_presence_percentage`, `brand_share_of_voice`, `brand_position_score`, `brand_sentiment_score`, `brand_citation_rate`, `responses`, plus `comparison` = `prior_period`. Report current, prior, and change for each.
4. **By AI platform.** Call `query_metrics` with `fields` = `ai_platform`, `brand_presence_percentage`, `brand_sentiment_score`, `brand_citation_rate`, `responses`, plus `comparison` = `prior_period`. Flag the strongest and weakest platform and the biggest movers.
5. **Distributions.** Call `get_position_metrics` for the top, middle, and bottom split. Call `get_sentiment_metrics` for the positive, mixed, and negative split. Call `get_citations_metrics` for the brand, competitor, and third-party citation split. Pass the same dates, because these tools default to 90 days.
6. **Weakest topics.** Call `query_metrics` with `fields` = `prompt_topic`, `brand_presence_percentage`, `responses`, `order_by` = `brand_presence_percentage` ascending, `having` = `responses` gte 10 (or a threshold that fits the volume), and `limit` = 5.
7. **Signals.** Call `list_signals` with `anchor_from` and `anchor_to` set to the report period. Use default tiers and the default sort. For the top two or three signals, call `get_signal` to get the narrative. Always state each signal's `detected_for_date` and `tier`, and don't judge significance yourself.
8. **Evidence (optional).** To illustrate a finding, call `list_responses` with filters such as `platform` and `prompt_id`, then call `get_response` on one or two IDs and quote briefly. Label quotes as examples, not as metrics.
9. **Explorer link.** Call `generate_explorer_chart` with `metrics` = `[{"metric": "mention"}]`, `breakdowns` = `["ai_platform"]`, and a `range` that matches the report (or `custom` with dates). Present the returned `url` as a markdown link.

## Output format

1. **Summary:** 3–5 bullets with the period, the headline movement, the biggest platform story, and the top signal.
2. **Headline table:** metric, current, prior, change, and responses.
3. **By platform:** a table.
4. **Position, sentiment, and citation mix:** short tables or bullets.
5. **Weakest topics:** the topic, presence, and responses for each.
6. **Signals:** a headline, `detected_for_date`, `tier`, and a one-line "what to do" from the narrative.
7. **Explore further:** the Explorer link.

If the brand has little or no response data yet (for example, it was set up recently), say so plainly instead of building a report around empty results.
