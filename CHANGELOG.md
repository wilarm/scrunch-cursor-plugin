# Changelog

All notable changes to this plugin will be documented here.

## 1.0.0 — initial release

- Added the `scrunch` MCP server pointing at `https://app.scrunchai.com/api/mcp` (Streamable HTTP; transport inferred from `url`).
- Auth is OAuth with Dynamic Client Registration. Scopes pinned to `profile`, `email`, `offline_access` (Scrunch's sign-in flow does not grant `openid`).
- Logo: Scrunch's official mark (black octagon and star on light blue), `assets/logo.png`, 512×512.
- Added skills: `scrunch-visibility-report`, `scrunch-competitor-share-of-voice`, `scrunch-citation-gaps`.
