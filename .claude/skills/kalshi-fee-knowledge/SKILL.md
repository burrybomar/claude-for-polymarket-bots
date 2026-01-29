---
name: kalshi-fee-knowledge
description: Deep knowledge about Kalshi trading fees, cost modeling, and break-even analysis. Invoke when building bots that need fee awareness.
allowed-tools: Read, Write, WebSearch, WebFetch, TodoWrite
model: sonnet
---

# Kalshi Fee Knowledge

Comprehensive knowledge base for Kalshi fees and cost modeling. Use this when a strategy is sensitive to fees, margin requirements, or maker/taker economics.

## When to Use

Use this skill when:
- A bot trades frequently or with thin margins
- The user asks about fees, costs, or break-even prices
- Market making or arbitrage profitability depends on fee structure

## Research Steps

1. Locate the latest official Kalshi fee schedule documentation.
2. Capture any differences by product category or volume tier.
3. Note how fees are calculated (per contract, per dollar, or percent).
4. Identify if rebates exist for makers or high-volume traders.

## Output Format

Write findings to the strategy spec or research file as a summary, and include:

```markdown
## Kalshi Fee Schedule Summary

### Current Fee Structure
- [Describe fee units: per contract, per dollar, or percent]
- [Maker/taker differences, if any]
- [Any tiered or discounted schedules]

### Cost Modeling Guidance
- Example formula for total fees:
  - `total_fees = contracts * fee_per_contract` (or equivalent)
- How to incorporate fees into P&L
- Break-even spread calculations

### Implementation Notes
- Recommended config variables (fee rate, maker rebate, etc.)
- How often to refresh fee schedules

### Official References
- [Link to Kalshi fee documentation]
```

## Sample Fee Calculator (Template)

Use this when scaffolding a bot to ensure fee-aware decisions:

```python
from dataclasses import dataclass


@dataclass
class FeeSchedule:
    fee_per_contract: float
    maker_rebate_per_contract: float = 0.0

    def taker_fee(self, contracts: int) -> float:
        return contracts * self.fee_per_contract

    def maker_rebate(self, contracts: int) -> float:
        return contracts * self.maker_rebate_per_contract
```

## Safety Notes

- Always verify fees in the official docs before trading.
- Log fee assumptions explicitly in the bot output.
