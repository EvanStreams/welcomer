# Welcome Bot

Assigns new Slack members to welcome channels in rolling batches.

## What it does

- Adds new users to `welcome-1`, `welcome-2`, etc.
- Rotates to next channel when batch size (default 500) is hit
- Posts & pins a welcome message in each channel
- Handles MCG/SCG guests - adds them after they're promoted
- `/i-need-help` command to ping the welcome committee

## Setup

### 1. Create a Slack App

Go to [api.slack.com/apps](https://api.slack.com/apps) → Create New App → From scratch

### 2. Add Bot Scopes

OAuth & Permissions → Bot Token Scopes:

- `channels:manage`
- `channels:read`
- `channels:join`
- `chat:write`
- `pins:write`
- `commands`
- `users:read`

### 3. Enable Socket Mode

Socket Mode → Enable → Create app token with `connections:write`

### 4. Subscribe to Events

Event Subscriptions → Enable → Subscribe to bot events:

- `team_join`
- `member_joined_channel`
- `user_change`

### 5. Create Slash Command

Slash Commands → Create:

- Command: `/i-need-help`
- Description: Request help from welcome committee

### 6. Install & Run

```bash
pip install -r requirements.txt
cp .env.example .env
# fill in your tokens
python app.py
```

## Config

| Variable | Description |
|----------|-------------|
| `BOT_ENABLED` | Kill switch (`true`/`false`) |
| `BATCH_SIZE` | Users per channel before rotating |
| `CHANNEL_PREFIX` | Prefix for channel names |
| `CHANNEL_NAME_FORMAT` | Pattern like `{prefix}-{n}` |
| `WELCOME_MESSAGE` | Message posted in new channels |
| `DEFAULT_CHANNELS` | Comma-separated channel IDs to auto-add |
| `OPTIN_PROMPT_CHANNEL` | Where to send opt-in prompts |
| `WELCOME_COMMITTEE_CHANNEL` | Where help requests go |
| `REDIS_URL` | Redis connection URL for persistence |

## Persistence

Set `REDIS_URL` for persistent state across restarts. Without it, uses in-memory (state lost on restart).

## Local Dev

```bash
# create venv
python -m venv venv
source venv/bin/activate  # or venv\Scripts\activate on Windows

# install deps
pip install -r requirements.txt

# copy env and fill in your tokens
cp .env.example .env

# run (uses Socket Mode, no public URL needed)
python app.py
```

To test without Redis, just leave `REDIS_URL` empty - state will be in-memory.

For local Redis:
```bash
# with Docker
docker run -d -p 6379:6379 redis

# or install locally
brew install redis && redis-server
```
