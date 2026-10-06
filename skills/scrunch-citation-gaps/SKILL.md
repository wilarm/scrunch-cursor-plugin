---
name: scrunch-citation-gaps
description: Find the sources AI platforms cite for a Scrunch brand's non-branded prompts. Splits citations by owner (brand, competitor, third party), ranks the most-cited domains and pages, finds topics and platforms where the brand is rarely cited, and turns the results into a prioritized outreach and content list. Use when someone asks "which sites does AI cite in our category", "citation gap analysis", "why aren't we being cited", or "where should we get coverage". Read-only. Requires the Scrunch MCP server.
---

# Scrunch citation gap analysis

## When to use

- "Which domains are AI platforms citing for our category?"
- "Where are competitors getting cited and we're not?"
- "What third-party sites should we target for coverage to show up in AI answers?"

## Ground rules

- Use `get_citations_metrics` and `query_metrics` for every number. Use `list_responses` and `get_response` only to read individual citations as evidence.
- Use `brand_mentioned=false` (in `get_citations_metrics`) or a `branded` = `false` filter (in `query_metrics`) for non-branded prompts. That's where citation gaps matter. Don't use `citation_owner` to separate branded from non-branded prompts.
- Before filtering on dimensions such as `owner` or `citation_segment`, call `get_metric_glossary` (section `dimensions`) to confirm the accepted values.
- Check `list_query_patterns` first for a citation-ownership or top-domains pattern.
- State the resolved date range and the denominators.
- Stay read-only. Do not call `update_brand` (for example, to edit citation segments) unless the user explicitly asks.

## Steps

1. **Resolve the brand and dates.** Call `get_caller_identity` (and `list_brands` if needed), then `get_current_date`. Default to the last 30 days.
2. **Ownership split.** Call `get_citations_metrics` with `brand_mentioned=false` and the chosen dates. Report the brand, competitor, and third-party share, and the trend if one is visible.
3. **Most-cited domains.** Call `query_metrics` with `fields` = `domain`, `owner`, `citation_unique_responses`, `citation_count`, `filters` = `[{"field": "branded", "values": [false]}]`, `order_by` = `citation_unique_responses` descending, and `limit` = 25.
4. **Competitor-owned sources.** Repeat step 3 filtered to competitor-owned citations, adding `cited_competitor_name` to `fields`. This shows which competitor pages AI leans on.
5. **Specific pages.** Call `query_metrics` with `fields` = `source_url`, `domain`, `owner`, `citation_unique_responses`, plus the same non-branded filter, ordered descending, with `limit` = 25. If URL-level detail isn't returned, fall back to `list_responses(ids_only=false, limit=10)` for a sample and read the citations, or use `get_response` on specific IDs. Treat that fallback as a sample, not a count.
6. **Topic gaps.** Call `query_metrics` with `fields` = `prompt_topic`, `brand_citation_rate`, `competitor_citation_rate`, `citation_rate`, `responses`, the non-branded filter, and `order_by` = `competitor_citation_rate` descending. Topics where competitors are cited often and the brand rarely is are the content gaps.
7. **Platform gaps.** Call `query_metrics` with `fields` = `ai_platform`, `brand_citation_share_of_voice`, `brand_citation_rate`, `responses`, and the non-branded filter.
8. **Explorer link.** For the ownership donut, call `generate_explorer_chart` with `metrics` = `[{"metric": "citation", "aggregation": "unique_response_domains"}]`, `breakdowns` = `["owner"]`, and `viz` = `"pie"`. This is the unit the Citations dashboard uses. For top domains, use `metrics` = `[{"metric": "citation", "aggregation": "unique_responses"}]`, `breakdowns` = `["domain"]`, and `viz` = `"hbars"`. Present the `url` as a markdown link.

## Output format

1. **Summary:** the owner split for non-branded prompts, the top third-party domain, the top competitor source, and the weakest topic.
2. **Third-party targets (outreach and PR):** domain, responses citing it, and a note on what type of site it is, if obvious from the domain or a quoted citation.
3. **Competitor sources to counter (content):** competitor, domain or URL, and responses.
4. **Topic and platform gaps:** brand vs. competitor citation rate, with responses.
5. **Prioritized actions:** at most 5–8 items, each tied to a number above.
6. **Explore further:** the Explorer link.

Don't claim a site will produce citations if you get coverage there. Present these as the sources AI platforms currently cite.
