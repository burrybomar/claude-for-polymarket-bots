---
description: Creates a specialized Kalshi trading bot for a single strategy. Supports preset strategies OR custom user-defined strategies. Interrogates until full understanding, then researches, scaffolds, and audits.
argument-hint: "<strategy: market_maker|arbitrage|spike_detector|custom> [description]"
allowed-tools: Read, Write, Glob, Grep, Task, TodoWrite, AskUserQuestion, Skill
model: opus
---

# Create Single Strategy Bot

Create a complete, working Kalshi trading bot specialized for a single strategy.

**This command supports BOTH preset strategies AND custom user-defined strategies.**

For custom strategies, you MUST thoroughly interrogate the user until you have COMPLETE understanding of what they want. Never proceed with ambiguity.

## Strategy Types

### Preset Strategies
| Code | Description |
|------|-------------|
| `market_maker` | Two-sided quotes, spread capture |
| `arbitrage` | YES+NO pricing inefficiencies |
| `spike_detector` | React to sudden price movements |

### Custom Strategy
| Code | Description |
|------|-------------|
| `custom` | User-defined strategy - requires clarification |
| (anything else) | Treated as custom strategy description |

## How to Use

```
# Preset strategies
/create_single_strategy_bot market_maker
/create_single_strategy_bot arbitrage

# Custom strategies
/create_single_strategy_bot custom
/create_single_strategy_bot "buy when price drops 10% in 5 minutes"
/create_single_strategy_bot I want a bot that follows whale wallets
```

---

## Dynamic Skills Integration

### Kalshi Event Markets Skill

**When to Invoke**: Automatically invoke the `kalshi-event-markets` skill when ANY of these conditions are detected:

1. **Strategy needs market discovery**:
   - User is unsure of exact market IDs or event slugs
   - Strategy targets a category (politics, macro, sports, weather, etc.)
   - Strategy uses filters like volume, liquidity, or time-to-expiry

2. **Keywords that trigger invocation**:
   - "find markets", "event catalog", "category markets"
   - "Kalshi events", "Kalshi market list"
   - "liquid markets", "active contracts"

3. **Strategy types commonly requiring this skill**:
   - `market_maker` (needs liquid markets)
   - `arbitrage` (needs cross-market relationships)
   - Custom strategies that depend on fresh market lists

**How to Invoke**:

```
Use the Skill tool:
skill: "kalshi-event-markets"
```

The skill will:
1. Research Kalshi market discovery endpoints
2. Gather relevant event/market slugs
3. Document filtering and pagination patterns
4. Write findings to `thoughts/shared/kalshi-event-markets-research.md`

---

### Kalshi Fee Knowledge Skill

**When to Invoke**: Read the `kalshi-fee-knowledge` skill when ANY of these conditions are detected:

1. **User asks about fees or costs**:
   - "What are the fees?"
   - "How do I calculate costs?"
   - "What's my break-even price?"

2. **Profitability or P&L calculations needed**:
   - Strategy depends on thin margins
   - Market making or frequent trading

**Skill Location**: `.claude/skills/kalshi-fee-knowledge/SKILL.md`

**How to Use**:

```
Read the skill file to get:
- Current fee schedule references
- Cost modeling guidance
- Fee impact tables by price/size
```

---

## YOUR PROCESS

### Step 1: Parse Strategy Input

Determine if this is a preset or custom strategy:

**If preset** (`market_maker`, `arbitrage`, `spike_detector`):
- Skip to Step 3 (you already understand the strategy)
- Use the standard strategy specification

**If custom** (anything else, including "custom"):
- Proceed to Step 2 (interrogation phase)
- You MUST fully understand before proceeding

**If no argument provided**:
Ask what strategy they want:
```
What trading strategy would you like to implement?

**Preset options:**
- `market_maker` - Two-sided quotes to capture spread
- `arbitrage` - Exploit YES+NO != $1.00 inefficiencies
- `spike_detector` - React to sudden price movements

**Or describe your custom strategy:**
Just tell me what you want the bot to do, and I'll ask clarifying questions.
```

---

### Step 2: Strategy Interrogation (CUSTOM STRATEGIES ONLY)

**THIS IS CRITICAL. DO NOT SKIP OR RUSH THIS STEP.**

For custom strategies, you must interrogate the user until you have COMPLETE clarity on:

#### 2.1: Core Logic Questions

Ask about the fundamental strategy:

1. **Entry Signal**: What triggers a buy?
   - Price-based? (crosses threshold, drops X%, etc.)
   - Time-based? (every N minutes, at market open, etc.)
   - Event-based? (news, whale activity, volume spike, etc.)
   - Combination?

2. **Exit Signal**: What triggers a sell/close?
   - Take profit target?
   - Stop loss?
   - Time-based exit?
   - Reversal signal?

3. **Position Sizing**: How much to trade?
   - Fixed amount per trade?
   - Percentage of portfolio?
   - Kelly criterion or other formula?
   - Maximum position size?

4. **Direction**: Which side(s)?
   - Only buy YES?
   - Only buy NO?
   - Both directions based on signal?

#### 2.2: Market Selection Questions

5. **Target Markets**: Which markets to trade?
   - Specific market(s) by name/ID?
   - Category-based? (politics, sports, macro, etc.)
   - All markets meeting criteria?
   - Single market or multi-market?

6. **Market Filters**: Any requirements?
   - Minimum liquidity?
   - Minimum volume?
   - Time to expiry?
   - Market status (open only)?

#### 2.3: Risk Management Questions

7. **Risk Limits**: Maximum drawdown or loss per day?
8. **Capital Allocation**: Total budget or account size?
9. **Concurrency**: Max open orders or positions?

#### 2.4: Operational Questions

10. **Run Frequency**: Continuous loop or scheduled intervals?
11. **Data Sources**: External feeds needed (news, prices, etc.)?
12. **Notifications**: Logging or alerts required?

---

### Step 3: Confirm Understanding

Summarize what you understand and ask for confirmation. Do NOT proceed until confirmed.

---

### Step 4: Write Strategy Spec to Thoughts

Create file:
```
thoughts/shared/kalshi-bot-specs/{timestamp}-{strategy-slug}.md
```

Include:
- Strategy overview
- Entry/exit logic
- Market selection
- Risk limits
- Required config variables
- Edge cases

---

### Step 5: Invoke Skills (if applicable)

- Invoke `kalshi-event-markets` when market discovery is needed
- Read `kalshi-fee-knowledge` when fees matter

---

### Step 6: Research Agent

Task with subagent_type="kalshi-researcher":

```
Research how to implement a Kalshi trading bot based on the strategy specification.

**READ THIS FILE FIRST**: `thoughts/shared/kalshi-bot-specs/{filename}.md`

Write your research findings to:
`thoughts/shared/kalshi-bot-specs/{filename}-research.md`
```

---

### Step 7: Scaffolder Agent

Task with subagent_type="kalshi-bot-scaffolder":

```
Create a complete Kalshi trading bot in Python.

Inputs:
1. Strategy Spec: `thoughts/shared/kalshi-bot-specs/{filename}.md`
2. Research: `thoughts/shared/kalshi-bot-specs/{filename}-research.md`

Output location:
`kalshi-bots/{strategy-slug}/`
```

---

### Step 8: Auditor Agent

Task with subagent_type="kalshi-bot-auditor":

```
Comprehensively audit the Kalshi bot against its specification.

Inputs:
1. Strategy Spec: `thoughts/shared/kalshi-bot-specs/{filename}.md`
2. Bot Location: `kalshi-bots/{strategy-slug}/`
```

---

### Step 9: Present Results

Summarize:
- Bot location
- Key files
- Any audit findings
- Next steps for running and testing
