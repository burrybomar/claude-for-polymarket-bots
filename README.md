# Claude Code for Kalshi Bots

A comprehensive Claude Code configuration for building Kalshi prediction market trading bots. This project provides specialized agents, skills, and commands that enable Claude Code to research, scaffold, and audit trading bots with deep knowledge of Kalshi's APIs.

## What This Project Does

This is a **Claude Code configuration project** - it enhances Claude Code with:

- **Specialized Agents**: Research and implementation agents with Kalshi domain expertise
- **Skills**: On-demand knowledge modules for Kalshi market discovery, fee schedules, and event mechanics
- **Commands**: Workflow automation for bot creation, auditing, and verification

When you use Claude Code in this directory, it gains the ability to build complete, working Kalshi trading bots through a structured research → scaffold → audit pipeline.

## Quick Start

1. **Install Claude Code** if you haven't already:
   ```bash
   npm install -g @anthropic-ai/claude-code
   ```

2. **Clone this repository**:
   ```bash
   git clone https://github.com/your-username/claude-for-kalshi-bots.git
   cd claude-for-kalshi-bots
   ```

3. **Start Claude Code**:
   ```bash
   claude
   ```

4. **Create your first bot**:
   ```
   /create_single_strategy_bot market_maker
   ```

## Available Commands

| Command | Description |
|---------|-------------|
| `/create_single_strategy_bot` | Create a complete trading bot (market_maker, arbitrage, spike_detector, or custom) |
| `/verify_changes` | Verify code changes work by creating and running test scripts |
| `/audit_logic` | Deep, skeptical audit of code paths and logic |
| `/check_concern` | Evaluate whether a specific concern about code behavior is valid |
| `/create_implementation_plan` | Create detailed implementation plans through interactive research |
| `/execute_plan` | Execute a pre-written implementation plan using coordinated subagents |
| `/research_codebase` | Research codebase comprehensively using parallel sub-agents |

## Supported Trading Strategies

### Preset Strategies

| Strategy | Description |
|----------|-------------|
| `market_maker` | Two-sided quotes around midpoint to capture spread |
| `arbitrage` | Exploit YES + NO pricing inefficiencies (when sum ≠ $1.00) |
| `spike_detector` | React to sudden price movements |

### Custom Strategies

Describe any strategy in natural language:
```
/create_single_strategy_bot "buy when price drops 10% in 5 minutes"
/create_single_strategy_bot custom
```

Claude will interrogate you to fully understand your strategy before implementation.

## Skills

Skills provide specialized knowledge that Claude can invoke on-demand:

| Skill | When It's Used |
|-------|----------------|
| `kalshi-event-markets` | Market discovery, event slugs, and category research for Kalshi |
| `kalshi-fee-knowledge` | Fee schedules, cost modeling, and break-even analysis for Kalshi |
| `context-optimizer` | Optimize context window before compaction |

### Fee Knowledge Highlights

Kalshi fees can change by product, volume tier, or execution type. The `kalshi-fee-knowledge` skill guides the agent to:
- Locate the current fee schedule in official documentation
- Model per-contract or per-dollar fees for a strategy
- Incorporate fee drag into P&L and risk limits

## Specialized Agents

This configuration includes 30+ specialized agents for research and implementation:

### Research Agents
- `kalshi-researcher` - Kalshi APIs, trading strategies, best practices
- `news-researcher` / `news-researcher-lite` - Current news and developments
- `financial-markets-researcher` - Market indicators, asset prices, derivatives
- `social-media-researcher` - Sentiment analysis, viral discussions
- `expert-opinion-researcher` - Think tank reports, analyst opinions
- `contrarian-researcher` - Devil's advocate finding counter-arguments
- `historical-precedent-researcher` - Past events and base rates
- `data-researcher` - Statistics, polls, prediction market odds
- `technical-researcher` - Code snippets, official documentation

### Implementation Agents
- `kalshi-bot-scaffolder` - Creates complete bot project structures
- `kalshi-bot-auditor` - Comprehensive bot auditing against specifications
- `codebase-analyzer` - Analyzes implementation details
- `logic-auditor` - Deep, skeptical audit of code logic
- `change-verifier` - Creates and runs verification scripts
- `plan-executor` - Executes implementation phases

## How Bot Creation Works

The `/create_single_strategy_bot` command follows a structured pipeline:

```
1. Parse Strategy Input
   ↓
2. Interrogate (custom strategies only)
   ↓
3. Confirm Understanding with User
   ↓
4. Write Strategy Spec to thoughts/
   ↓
5. Invoke Market Research Skill (if applicable)
   ↓
6. Spawn Research Agent → writes findings
   ↓
7. Spawn Scaffolder Agent → creates bot files
   ↓
8. Spawn Auditor Agent → verifies implementation
   ↓
9. Present Results
```

All context passes through markdown files in `thoughts/shared/kalshi-bot-specs/`.

## Project Structure

```
.claude/
├── agents/           # 30+ specialized agent configurations
│   ├── kalshi-researcher.md
│   ├── kalshi-bot-scaffolder.md
│   ├── kalshi-bot-auditor.md
│   └── ...
├── commands/         # Slash command definitions
│   ├── create_single_strategy_bot.md
│   ├── verify_changes.md
│   └── ...
├── skills/           # On-demand knowledge modules
│   ├── kalshi-event-markets/
│   ├── kalshi-fee-knowledge/
│   └── context-optimizer/
└── settings.local.json

thoughts/             # Working directory for agent context
└── shared/
    └── kalshi-bot-specs/
        ├── {timestamp}-{strategy}.md
        └── {timestamp}-{strategy}-research.md

kalshi-bots/      # Generated bots are created here
└── {strategy-slug}/
    ├── bot.py
    ├── config.py
    ├── requirements.txt
    └── .env.example
```

## Kalshi API Knowledge

The configuration includes guidance on Kalshi's trading APIs and market data surfaces, including:

| API Surface | Purpose | Auth |
|-------------|---------|------|
| **Trading API** (REST/WebSocket) | Orders, fills, positions | Required |
| **Market discovery** (public REST) | Market metadata, event catalogs | Usually none |

The exact base URLs and authentication steps are captured in the `kalshi-researcher` output and used by the scaffolder to configure the bot.

## Requirements

- [Claude Code](https://docs.claude.ai/claude-code) CLI
- Python 3.9+ (for generated bots)
- Kalshi account with API credentials

## Generated Bot Dependencies

Bots created by this configuration use:
- `requests` + `websockets` (or the official Kalshi SDK, if available)
- `python-dotenv` - Environment configuration
- Standard libraries for logging, scheduling, etc.

## Safety Notes

- Generated bots include `.env.example` files - never commit real credentials
- All strategies include risk management parameters
- Test with small amounts before production use
- The auditor agent verifies implementation matches specification

## License

MIT
