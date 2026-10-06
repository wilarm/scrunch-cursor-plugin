---
name: scrunch-competitor-share-of-voice
description: Rank a Scrunch brand's tracked competitors by AI share of voice, mention rate, average rank, and sentiment, broken down by AI platform, then find the specific prompts where a competitor appears and the brand doesn't, with response evidence. Use when someone asks "who's beating us in AI search", "competitor share of voice", "which competitors are winning on Perplexity", or wants a competitive battlecard input. Read-only. Requires the Scrunch MCP server.
---

# Scrunch competitor share of voice

## When to use

- "Rank our competitors by share of voice in AI answers."
- "Which competitors are winning on ChatGPT or Perplexity?"
- "Where is <competitor> showing up and we aren't?"

## Ground rules

- Metric names carry the entity. `brand_*` fields describe the user's brand, and `competitor_*` fields describe competitors. Never present one entity's numbers as another's.
- Use `query_metrics` for every number. Use `list_responses` and `get_response` only to quote evidence.
- Check `list_query_patterns` first and reuse a matching share-of-voice pattern via `get_query_pattern`.
- State the resolved date range and include `responses` with every rate.
- Stay read-only. Do not add, edit, or archive competitors unless the user explicitly asks.

## Steps

1. **Resolve the brand and dates.** Call `get_caller_identity` (and `list_brands` if needed), then `get_current_date`. Default to the last 30 days.
2. **Competitor set.** Call `list_competitors(brand_id)` so you know exactly who is tracked. If the user names a rival that isn't tracked, say so. Don't add it.
3. **Brand baseline.** Call `query_metrics` with `fields` = `brand_share_of_voice`, `brand_presence_percentage`, `brand_avg_rank`, `brand_sentiment_score`, `responses`.
4. **Competitor leaderboard.** Call `query_metrics` with `fields` = `competitor_name`, `competitor_share_of_voice`, `competitor_presence_percentage`, `competitor_avg_rank`, `competitor_sentiment_score`, `responses`, and `order_by` = `competitor_share_of_voice` descending. Put the brand's baseline row in the same table, clearly labeled.
5. **By platform.** Call `query_metrics` with `fields` = `ai_platform`, `competitor_name`, `competitor_share_of_voice`, `responses`, and separately with `fields` = `ai_platform`, `brand_share_of_voice`, `responses`. For each platform, call out which competitor leads and where the brand trails most.
6. **Trend (optional).** Repeat step 4 with explicit dates and `comparison` = `prior_period` to show who is gaining or losing. For a time series of everyone at once, call `get_presence_metrics` with `include_competitors=true`.
7. **Displacement prompts.** For the top one to three competitors, call `query_metrics` with `fields` = `prompt_id`, `prompt`, `competitor_presence_percentage`, `brand_presence_percentage`, `responses`, `filters` = `[{"field": "competitor_name", "values": ["<competitor>"]}]`, `order_by` = `competitor_presence_percentage` descending, and a `having` threshold on `responses`. Keep the prompts where the competitor's presence is high and the brand's is low.
8. **Evidence.** For one or two displacement prompts, call `list_responses(prompt_id=..., ids_only=false, limit=3)` or `get_response` and quote how the AI framed the competitor. Mark quotes as examples.
9. **Explorer link.** Call `generate_explorer_chart` with `metrics` = `[{"metric": "sov", "entity": "competitor"}]`, `breakdowns` = `["competitor_name"]`, and `viz` = `"hbars"` (or a share-of-voice pattern link from step 1). Present the `url` as a markdown link.

## Output format

1. **Summary:** the brand's rank among tracked competitors, the leader, the biggest gainer, and the weakest platform.
2. **Leaderboard table:** entity, share of voice, mention rate, average rank, sentiment, and responses, with the brand row highlighted.
3. **Platform matrix:** the leader on each platform and the brand's gap.
4. **Prompts to reclaim:** prompt text, competitor presence, brand presence, responses, and a one-line suggested angle.
5. **Explore further:** the Explorer link.
