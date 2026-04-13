# QuaiSentry — QUAI Network Monitor Bot

![License](https://img.shields.io/badge/license-Proprietary-red)
![Status](https://img.shields.io/badge/status-active-brightgreen)
![Discord](https://img.shields.io/badge/Discord-bot-5865F2?logo=discord&logoColor=white)
![Network](https://img.shields.io/badge/network-QUAI-orange)
![Security](https://img.shields.io/badge/security-audited-blue)
![i18n](https://img.shields.io/badge/languages-EN%20%7C%20FR-lightgrey)

A Discord bot that monitors QUAI wallets in near real time and delivers mining, transfer, unlock, and balance alerts — directly to your DMs or a server channel.

> **Closed-source hosted bot.** The bot runs on managed infrastructure. You do not need to run or configure anything yourself.

---

## Add QuaiSentry to Discord

👉 **[Add to your account (recommended)](https://quaisentry.vercel.app)**
Works as a user-install app — no server required.

👉 **[Invite to a server](https://quaisentry.vercel.app)**
Add the bot to a Discord server to use shared channel notifications.

After installing, use `/help` to get started.

---

## Features

- Track multiple wallets per user, with custom labels
- Per-wallet zone selection across all QUAI network zones
- Mining block detection with full unlock lifecycle tracking
- Transfer and balance change alerts
- Weekly unlock recap notifications
- Paginated `/unlocks` view with live confirmation counters
- Multilingual interface (English & French via `/settings language`)
- Slash commands for wallet management, diagnostics, and configuration
- Polling health metrics and circuit breaker visibility via `/status`

---

## Commands

### Wallet Management

| Command | Description |
|---|---|
| `/wallet add` | Add a wallet to monitor |
| `/wallet remove` | Remove a wallet |
| `/wallet list` | List wallets and current balances |
| `/wallet rename` | Rename a wallet label |
| `/wallet zone` | Change the zone assigned to a wallet |
| `/wallet resync` | Re-fetch all historical blocks from QuaiScan |

### Monitoring & Info

| Command | Description |
|---|---|
| `/scan` | Quick scan of any QUAI address |
| `/summary` | Full wallet summary (balance, mining, unlocks) |
| `/balance` | Current QUAI balance and USD value |
| `/mining` | Mining stats and recent block rewards |
| `/unlocks` | Pending unlocks with live confirmation counters |
| `/price` | Current QUAI market price |

### Settings

| Command | Description |
|---|---|
| `/settings view` | Show all current notification settings |
| `/settings toggle` | Enable or disable a specific notification type |
| `/settings channel` | Set a server channel as fallback for notifications |
| `/settings dm` | Enable or disable DM notifications |
| `/settings display` | Switch between compact and detailed embed layouts |
| `/settings language` | Change the bot language (`en` / `fr`) |

### Utilities

| Command | Description |
|---|---|
| `/test-notification` | Send sample notifications to your DM |
| `/status` | Bot health, polling diagnostics, and API latency |
| `/invite` | Get invite links (user install or server invite) |
| `/help` | Command reference |

---

## Notifications

Default delivery is via DM. A server channel fallback can be configured with `/settings channel`.

| Type | Trigger |
|---|---|
| **Mining** | New mined block detected for a watched wallet |
| **Unlock** | A reward block reaches unlock maturity (241,900 confirmations) |
| **Transfer** | Incoming or outgoing transfer detected |
| **Balance** | Wallet balance changed since the last poll |

Each notification type can be toggled independently via `/settings toggle`.

---

## How It Works

### Polling engine

QuaiSentry runs a continuous polling loop that checks all registered wallets on a configurable interval (default: 45 seconds). Each wallet is queried with a controlled per-wallet delay to avoid hammering the QUAI API. The loop tracks cycle duration, skipped cycles, and overrun cycles — all visible in `/status`.

### Circuit breaker

API calls go through a circuit breaker. If a given endpoint fails consecutively (default threshold: 4 failures), the circuit opens and that endpoint is skipped for a cooldown period (default: 30 seconds). This prevents cascade failures during network instability and is surfaced in `/status`.

### Unlock confirmation system

When a block is mined, the reward is locked for a maturity window of **241,900 confirmations** (approximately 14 days). QuaiSentry tracks each pending unlock block individually. The `/unlocks` command shows:

- Block number and estimated unlock timestamp
- Live confirmation count for the 5 nearest upcoming unlocks (fetched at query time)
- Progress toward maturity as a visual bar
- Total locked QUAI value and USD equivalent

Blocks are paginated to handle wallets with large unlock queues without flooding the embed.

### Resync

If a wallet's block history is out of sync (e.g. after a zone correction), `/wallet resync` re-fetches all historical mined blocks from QuaiScan and rebuilds the local unlock tracking database for that wallet.

### Performance diagnostics

`/status` exposes:
- Last poll duration, rolling average, and p95 latency
- Cycle counts (completed / skipped / overrun)
- API endpoint latency (p95)
- Circuit breaker state per endpoint
- Recommended poll interval based on observed p95

If `/status` shows a **Performance warning**, increase `POLL_INTERVAL` or check API circuit breaker status.

---

## Privacy & Security

- The bot stores only wallet addresses, labels, zone assignments, and notification preferences.
- No private keys are ever requested or stored.
- Notification data (block numbers, balances, tx hashes) is stored locally per user and never shared.
- The bot token and all secrets are stored in environment variables and never committed or exposed.
- All Discord interactions are ephemeral by default — replies are visible only to the requesting user.
- The codebase passes ESLint validation on every CI run with zero errors tolerated.

---

## Support

If you encounter issues, open a GitHub issue on this repository with:
- The command you used
- The error or unexpected behavior
- Output of `/status` if relevant

---

*QuaiSentry is an independent community project and is not affiliated with Quai Network.*
