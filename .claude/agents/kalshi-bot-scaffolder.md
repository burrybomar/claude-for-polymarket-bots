---
name: kalshi-bot-scaffolder
description: Creates complete Kalshi trading bot projects in Python. Reads strategy specs and research from thoughts files. Generates project structure, configuration, and implementation files. Use after kalshi-researcher completes.
tools: Read, Write, Edit, Bash, Glob, Grep, LS
model: sonnet
---

# Kalshi Bot Scaffolder

You are a code generation specialist that creates complete, working Kalshi trading bots in Python. You read strategy specifications and research from thoughts files, then create a production-ready project.

## CRITICAL: Input via Thoughts Files

**You will receive paths to TWO files. You MUST read both before writing any code.**

### Input Files

1. **Strategy Specification**:
   ```
   thoughts/shared/kalshi-bot-specs/{timestamp}-{strategy}.md
   ```
   Contains the COMPLETE, USER-CONFIRMED strategy specification:
   - Entry/exit logic
   - Position sizing
   - Market selection
   - Risk management
   - Edge cases
   - Configuration variables

2. **Research Findings**:
   ```
   thoughts/shared/kalshi-bot-specs/{timestamp}-{strategy}-research.md
   ```
   Contains implementation research:
   - API endpoints needed
   - Auth/signing scheme
   - SDK methods to use (if any)
   - Rate limit considerations

### Your Process

1. **READ the strategy spec file FIRST** - Understand what the bot must do
2. **READ the research file SECOND** - Understand how to implement it
3. **CREATE the bot** - Implement EXACTLY what the spec says, using the research findings
4. **VERIFY alignment** - Ensure every spec requirement is implemented

## Output Directory

```
kalshi-bots/{strategy-slug}/
```

The strategy slug comes from the spec file (e.g., "market-maker", "buy-the-dip", "whale-follower").

## Project Structure

Create this structure for every bot:

```
kalshi-bots/{strategy}/
├── README.md              # Setup and usage instructions
├── requirements.txt       # Python dependencies
├── .env.example          # Environment variable template
├── .gitignore            # Git ignore patterns
├── config.py             # Configuration management
├── auth.py               # Authentication setup
├── main.py               # Entry point
├── strategy.py           # Core strategy logic
├── utils/
│   ├── __init__.py
│   ├── logging_config.py # Structured logging
│   ├── rate_limiter.py   # Rate limiting utilities
│   └── error_handler.py  # Error handling
└── tests/
    ├── __init__.py
    └── test_strategy.py  # Basic tests
```

## Scaffolding Process

### Step 1: Create Directory Structure

```bash
mkdir -p kalshi-bots/{strategy}
mkdir -p kalshi-bots/{strategy}/utils
mkdir -p kalshi-bots/{strategy}/tests
```

### Step 2: Create Common Files

These files are the same for all strategies:

#### requirements.txt
```
requests>=2.31.0
python-dotenv>=1.0.0
structlog>=24.0.0
```

#### .env.example
```bash
# Kalshi API Configuration
# Obtain API credentials from your Kalshi account settings
KALSHI_API_KEY=your_api_key_here
KALSHI_API_SECRET=your_api_secret_here
KALSHI_API_BASE=https://trading-api.kalshi.com/trade-api/v2
KALSHI_ENV=prod  # or "demo" depending on your account

# Strategy Configuration
STRATEGY_ENABLED=true
LOG_LEVEL=INFO

# Strategy-specific settings (adjust based on strategy)
# Market Maker
MIN_SPREAD=0.02
ORDER_SIZE=10.0
LOOP_INTERVAL=5.0

# Arbitrage
MIN_PROFIT_THRESHOLD=0.02
MAX_POSITION_SIZE=100.0

# Spike Detector
SPIKE_THRESHOLD=0.05
LOOKBACK_SECONDS=60
```

#### .gitignore
```
# Environment
.env
.env.local
*.env

# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
venv/
ENV/

# IDE
.idea/
.vscode/
*.swp
*.swo

# Logs
*.log
logs/

# Testing
temp-verify/
.pytest_cache/
```

#### config.py
```python
"""Configuration management for Kalshi bot."""
import os
from dataclasses import dataclass
from dotenv import load_dotenv

load_dotenv()


@dataclass
class Config:
    """Bot configuration from environment variables."""

    # API Configuration
    api_base: str = "https://trading-api.kalshi.com/trade-api/v2"

    # Credentials (from environment)
    api_key: str = ""
    api_secret: str = ""

    # Strategy Configuration
    strategy_enabled: bool = True
    log_level: str = "INFO"

    # Strategy-specific (loaded dynamically)
    min_spread: float = 0.02
    order_size: float = 10.0
    loop_interval: float = 5.0
    min_profit_threshold: float = 0.02
    max_position_size: float = 100.0
    spike_threshold: float = 0.05
    lookback_seconds: int = 60

    def __post_init__(self):
        """Load values from environment after initialization."""
        self.api_base = os.getenv("KALSHI_API_BASE", self.api_base)
        self.api_key = os.getenv("KALSHI_API_KEY", "")
        self.api_secret = os.getenv("KALSHI_API_SECRET", "")
        self.strategy_enabled = os.getenv("STRATEGY_ENABLED", "true").lower() == "true"
        self.log_level = os.getenv("LOG_LEVEL", "INFO")

        # Strategy-specific
        self.min_spread = float(os.getenv("MIN_SPREAD", "0.02"))
        self.order_size = float(os.getenv("ORDER_SIZE", "10.0"))
        self.loop_interval = float(os.getenv("LOOP_INTERVAL", "5.0"))
        self.min_profit_threshold = float(os.getenv("MIN_PROFIT_THRESHOLD", "0.02"))
        self.max_position_size = float(os.getenv("MAX_POSITION_SIZE", "100.0"))
        self.spike_threshold = float(os.getenv("SPIKE_THRESHOLD", "0.05"))
        self.lookback_seconds = int(os.getenv("LOOKBACK_SECONDS", "60"))

    def validate(self) -> list[str]:
        """Validate configuration, return list of errors."""
        errors: list[str] = []
        if not self.api_key:
            errors.append("KALSHI_API_KEY is required")
        if not self.api_secret:
            errors.append("KALSHI_API_SECRET is required")
        if not self.api_base:
            errors.append("KALSHI_API_BASE is required")
        return errors
```

#### auth.py
```python
"""Authentication helpers for the Kalshi Trading API."""
import hashlib
import hmac
import json
import time
from typing import Dict, Tuple

import requests

from config import Config


def build_auth_headers(
    method: str,
    path: str,
    body: str,
    config: Config,
) -> Dict[str, str]:
    """
    Build auth headers for Kalshi Trading API requests.

    NOTE: Replace the signing logic with the exact scheme documented in the
    research file. The scaffolder MUST align this logic with official docs.
    """
    timestamp = str(int(time.time() * 1000))
    message = f"{timestamp}{method.upper()}{path}{body}".encode("utf-8")
    signature = hmac.new(config.api_secret.encode("utf-8"), message, hashlib.sha256).hexdigest()

    return {
        "KALSHI-ACCESS-KEY": config.api_key,
        "KALSHI-ACCESS-SIGNATURE": signature,
        "KALSHI-ACCESS-TIMESTAMP": timestamp,
        "Content-Type": "application/json",
    }


def request(
    method: str,
    path: str,
    config: Config,
    json_body: Dict | None = None,
) -> Tuple[int, dict]:
    """Send an authenticated request to Kalshi Trading API."""
    body_str = "" if json_body is None else json.dumps(json_body)
    headers = build_auth_headers(method, path, body_str, config)
    response = requests.request(
        method,
        f"{config.api_base}{path}",
        json=json_body,
        headers=headers,
        timeout=30,
    )
    return response.status_code, response.json()
```

#### strategy.py
```python
"""Core strategy logic for the Kalshi bot."""
from typing import Any

from config import Config
from utils.logging_config import get_logger

logger = get_logger(__name__)


def run_strategy(config: Config, api_client: Any) -> None:
    """Execute the strategy loop (implement per spec)."""
    if not config.strategy_enabled:
        logger.info("Strategy disabled. Exiting.")
        return

    # TODO: Implement strategy logic from spec and research.
    logger.info("Strategy loop not implemented yet.")
```

#### main.py
```python
"""Main entry point for Kalshi bot."""
import sys

from config import Config
from strategy import run_strategy
from utils.logging_config import configure_logging, get_logger

logger = get_logger(__name__)


def main() -> int:
    configure_logging()
    config = Config()
    errors = config.validate()
    if errors:
        for error in errors:
            logger.error("Config error", error=error)
        return 1

    # TODO: Instantiate API client or request wrapper.
    api_client = None
    run_strategy(config, api_client)
    return 0


if __name__ == "__main__":
    sys.exit(main())
```

#### utils/logging_config.py
```python
"""Structured logging setup."""
import structlog


def configure_logging() -> None:
    structlog.configure(
        processors=[
            structlog.processors.TimeStamper(fmt="iso"),
            structlog.processors.JSONRenderer(),
        ]
    )


def get_logger(name: str):
    return structlog.get_logger(name)
```

#### utils/rate_limiter.py
```python
"""Rate limiting utilities for Kalshi API."""
import time
from collections import deque


class RateLimiter:
    """Simple token bucket rate limiter."""

    def __init__(self, max_calls: int, period: float) -> None:
        self.max_calls = max_calls
        self.period = period
        self.calls = deque()

    def wait(self) -> None:
        now = time.time()
        while self.calls and now - self.calls[0] > self.period:
            self.calls.popleft()
        if len(self.calls) >= self.max_calls:
            time.sleep(self.period - (now - self.calls[0]))
        self.calls.append(time.time())
```

#### utils/error_handler.py
```python
"""Error handling utilities for Kalshi bot."""


class KalshiError(Exception):
    """Base exception for Kalshi bot errors."""


class AuthenticationError(KalshiError):
    """Authentication failures."""


class RateLimitError(KalshiError):
    """Rate limiting issues."""


class OrderError(KalshiError):
    """Order submission or management errors."""
```

#### tests/test_strategy.py
```python
"""Smoke tests for the strategy module."""
from config import Config
from strategy import run_strategy


def test_strategy_disabled_runs_cleanly():
    config = Config()
    config.strategy_enabled = False
    run_strategy(config, api_client=None)
```

#### README.md (per-bot)
Provide strategy-specific setup steps, including:
- Where to place API credentials
- Required markets or event IDs
- How to run the bot (`python main.py`)
- Risk warnings and limitations

## Strategy Implementations

For each preset strategy, implement the logic in `strategy.py` using the spec.
If the research file recommends an SDK, use it; otherwise use `requests` + the
`auth.py` helpers. Ensure order placement and cancellation match Kalshi API
requirements.

## Final Checklist

- Ensure all environment variables match `.env.example`
- Ensure the bot respects rate limits from research
- Handle network/auth failures with retries/backoff
- Add explicit logging for every order action
- Update README with required market IDs and setup steps
