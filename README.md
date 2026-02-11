# LuLu AI - AI-Powered Firewall Assistant 🛡️🤖

> Making macOS firewall decisions intelligent with AI analysis and Sui blockchain payments.

**Sui Hackathon 2026 Submission**

![Telegram Alerts](images/telegram-alerts.png)

## The Problem

[LuLu](https://objective-see.org/products/lulu.html) is the best open-source macOS firewall. But when it shows an alert like:

> `python3 (/usr/bin/python3) wants to connect to 185.199.108.133:443`

Most users have no idea if this is safe or malicious. They either:
- **Allow everything** (defeating the purpose of a firewall)
- **Block everything** (breaking legitimate apps)

## The Solution

**LuLu AI** adds an AI brain to your firewall. It automatically analyzes every connection alert and tells you:
- What the process is and why it's connecting
- Who owns the destination IP (WHOIS, Geo, DNS)
- Risk level (🟢 Low / 🟡 Medium / 🔴 High)
- Clear recommendation: ✅ Allow or 🚫 Block

## Two Components

### 1. 🤖 AI Agent Skill (lulu-monitor)

A background service that monitors LuLu alerts and sends AI-analyzed notifications to Telegram with one-tap Allow/Block buttons.

**Repo:** [EasonC13-agent/lulu-monitor](https://github.com/EasonC13-agent/lulu-monitor)

**Features:**
- Real-time LuLu alert detection via AppleScript
- AI analysis using Claude (via OpenClaw Gateway)
- Telegram notifications with 4 action buttons (Allow/Block, permanent or one-time)
- Auto-execute mode for high-confidence safe connections
- Runs as launchd service (auto-start on boot)

**How it works:**
```
LuLu Alert → Detect (AppleScript) → OpenClaw AI Analyzes → Telegram Notification
                                                                    ↓
                                                          User taps Allow/Block
                                                                    ↓
                                                          Action executed on LuLu
```

**Example: Suspicious connection blocked** 🔴
> 🔥 **LuLu Firewall Alert**
>
> Process: python3 (/usr/bin/python3)
> PID: 51847
> Connection: 185.199.108.133:443 (TCP)
> DNS: raw.githubusercontent.com
>
> **Analysis:** I was installing a new skill from ClawHub, but this Python subprocess is attempting to connect to GitHub to download additional scripts. This connection was NOT initiated by me and appears to be from a post-install hook in the skill package.
>
> **Risk: 🔴 High** - Unexpected outbound connection during skill installation
> **Recommendation: 🚫 Block** - Investigate the skill's install scripts before allowing

**Example: Safe connection allowed** 🟢
> 🔥 **LuLu Firewall Alert**
>
> Process: python3.11 (/opt/homebrew/bin/python3.11)
> PID: 52103
> Connection: 148.251.183.254:443 (TCP)
> DNS: wttr.in
>
> **Analysis:** I'm running the weather skill for the first time to check today's forecast. The skill uses a Python script that connects to wttr.in (a weather service). This is the expected behavior for this skill.
>
> **Risk: 🟢 Low** - First-time connection from weather skill
> **Recommendation: ✅ Allow**

### 2. 🖥️ Mac App (LuLuAICompanion)

A native macOS menu bar app that provides AI analysis directly on your Mac with an overlay recommendation window.

**Repo:** [EasonC13-agent/LuLuAICompanion](https://github.com/EasonC13-agent/LuLuAICompanion)

**Features:**
- Native SwiftUI menu bar app
- Monitors LuLu alerts via Accessibility API
- WHOIS + Geo enrichment
- AI recommendation overlay window
- Setup wizard for easy configuration

## Sui Integration: 3mate Platform

Both components use the **3mate Platform** for AI API access, powered by Sui Tunnel payments.

**Platform:** [platform.3mate.io](https://platform.3mate.io)
**Repo:** [EasonC13-agent/platform.3mate.io](https://github.com/EasonC13-agent/platform.3mate.io)

### How Payment Works

1. **Deposit**: User opens a Sui Tunnel, deposits USDC on-chain
2. **Create Key**: Generate API key, authorized on-chain via `add_authorized_key()`
3. **Use**: Each AI analysis deducts from tunnel balance (pay-per-use)
4. **Claim**: Backend claims used funds with Ed25519 signature verification
5. **Close**: User can close tunnel and get unused funds refunded anytime

### Smart Contract

**Sui Move** Tunnel Payment Protocol on testnet:
- Package: `0x42a47edd3066dd10e516ec2715ba61fbca91eec0be12288c97b0edb04d092678`
- **42 tests, 100% code coverage**
- Multi-key support: one deposit pool, multiple API keys
- Grace period protection for users
- Gas Station sponsored transactions

## Architecture

```
┌──────────────────────────────────────────┐
│              User's Mac                   │
│                                           │
│  ┌──────────┐    ┌────────────────────┐  │
│  │  LuLu    │───→│  lulu-monitor      │  │
│  │ Firewall │    │  (Agent Skill)     │  │
│  └──────────┘    └─────────┬──────────┘  │
│                            │              │
│  ┌──────────┐              │              │
│  │  LuLu AI │              │              │
│  │ Companion│              │              │
│  │ (Mac App)│──────────────┤              │
│  └──────────┘              │              │
└────────────────────────────┼──────────────┘
                             │
              ┌──────────────▼──────────────┐
              │      3mate Platform          │
              │      platform.3mate.io       │
              │  ┌────────────────────────┐  │
              │  │  Claude API Proxy      │  │
              │  │  + Sui Tunnel Payment  │  │
              │  └────────────────────────┘  │
              └──────────────┬──────────────┘
                             │
              ┌──────────────▼──────────────┐
              │      Sui Blockchain          │
              │  (Tunnel / USDC / Claims)    │
              └─────────────────────────────┘
```

## Tech Stack

| Component | Technology |
|-----------|-----------|
| AI Agent Skill | Node.js, AppleScript, OpenClaw |
| Mac App | Swift, SwiftUI, XcodeGen |
| Web Platform | React, TypeScript, Vite, TailwindCSS |
| Backend | Bun, Hono, Prisma, Firebase Auth |
| Smart Contracts | Sui Move |
| Wallet Integration | @mysten/dapp-kit |
| Payments | Sui Tunnel, Gas Station, Ed25519 |

## Repositories

| Repo | Description |
|------|-----------|
| [lulu-ai-firewall-sui-hackathon](https://github.com/EasonC13-agent/lulu-ai-firewall-sui-hackathon) | This repo: docs, slides, submission |
| [lulu-monitor](https://github.com/EasonC13-agent/lulu-monitor) | AI Agent Skill (background service) |
| [LuLuAICompanion](https://github.com/EasonC13-agent/LuLuAICompanion) | Mac App (menu bar companion) |
| [platform.3mate.io](https://github.com/EasonC13-agent/platform.3mate.io) | 3mate Platform (backend + contracts) |

## Links

- 🌐 Platform: [platform.3mate.io](https://platform.3mate.io)
- 📊 Slides: [Pitch Deck](https://platform.3mate.io/pitch/)
- 🛡️ LuLu Firewall: [objective-see.org](https://objective-see.org/products/lulu.html)

## Author

**Eason Chen** - Sui Hackathon 2026
