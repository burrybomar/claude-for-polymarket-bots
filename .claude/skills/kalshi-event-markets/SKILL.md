---
name: kalshi-event-markets
description: Researches Kalshi event and market catalogs, including categories, slugs, and discovery endpoints. Invoke when strategies need market discovery or filtering.
allowed-tools: Task, WebSearch, WebFetch, Read, Write, Grep, Glob, TodoWrite
model: sonnet
---

# Kalshi Event Markets Research Skill

Specialized skill for gathering detailed information about Kalshi's event and market catalog. Use this to identify relevant market IDs, event slugs, and category filters before building bots.

## Purpose

This skill provides the coding agent with comprehensive knowledge about:
- Current market categories and event slugs
- Market discovery endpoints and filters
- Pagination and query patterns
- Market metadata fields needed by bots

## When to Invoke This Skill

Invoke this skill when:
- User wants to trade a category but does not know exact market IDs
- Strategy needs a dynamic market list (e.g., most liquid markets)
- Strategy references "active markets", "top volume", or similar filters

## Research Process

### Phase 1: Gather Documentation

Use Task tool with subagent_type="web-search-researcher" to find:
- Kalshi API documentation for market discovery
- Endpoints for events, markets, and contracts
- Filter parameters (status, category, volume, expiry)

### Phase 2: Fetch Live Market Data

Use WebFetch to query Kalshi's API directly based on the docs:

- List markets/events with filters
- Fetch details for a specific market or event
- Extract fields needed for bot configuration

### Phase 3: Document Findings

Write research output to: `thoughts/shared/kalshi-event-markets-research.md`

Use this format:

```markdown
# Kalshi Event Markets Research

Generated: {timestamp}

## Market Discovery Endpoints

| Endpoint | Purpose | Notes |
|----------|---------|-------|
| /markets | List markets with filters | pagination, status params |
| /events | Event catalog | category tags |

## Useful Filters
- status: open/closed
- category: politics, macro, sports, weather, etc.
- volume/liquidity fields (if available)
- time to expiry

## Example Queries
- [Example URL 1]
- [Example URL 2]

## Active Markets (Sample)
| Market ID | Title | Category | Expiry | Notes |
|-----------|-------|----------|--------|-------|
| ... | ... | ... | ... | ... |

## Implementation Notes
- Pagination behavior
- Rate limits or caching suggestions
- Fields required by the strategy spec
```

## Output Requirements

- Include exact endpoint paths and query parameters from docs
- Provide a short summary of how to map categories to market IDs
- Keep a list of market IDs/slugs required by the strategy
