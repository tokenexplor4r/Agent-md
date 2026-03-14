# Agent.md

Open source skills framework for Pump.fun Tokenized Agents. Forked from [pump-fun/pump-fun-skills](https://github.com/pump-fun/pump-fun-skills).

Pump.fun ships one skill: payments. That is the minimum an agent needs to collect revenue. Agent.md adds everything else. Identity, services, trading, social presence, analytics, and onchain operations. A complete framework for building agents that actually work.

## Why

Tokenized Agents on Pump.fun let your token earn onchain revenue. A portion of that revenue automatically buys back and burns the token. The problem is that the official skill set only covers payment processing. If you want your agent to do anything beyond accepting payments, you are on your own.

Agent.md fixes that. It is an open collection of skills that cover the full lifecycle of an autonomous agent. From defining who your agent is, to what it sells, to how it trades, to how it talks to the world, to how it tracks its own performance.

## Architecture

```
Agent-md/
├── skills/
│   ├── payments/              Accepting payments, verifying invoices
│   │   └── SKILL.md           (extended from pump-fun-skills)
│   │
│   ├── identity/              Who the agent is
│   │   ├── SKILL.md
│   │   └── templates/
│   │       ├── AGENT.template.md
│   │       ├── VOICE.template.md
│   │       └── CONTEXT.template.md
│   │
│   ├── services/              What the agent sells
│   │   └── SKILL.md
│   │
│   ├── trading/               Autonomous trading logic
│   │   └── SKILL.md
│   │
│   ├── social/                Public communication
│   │   └── SKILL.md
│   │
│   ├── analytics/             Revenue and performance tracking
│   │   └── SKILL.md
│   │
│   └── onchain/               Solana operations
│       └── SKILL.md
│
├── examples/
│   ├── chatbot-agent/         Paid chatbot with identity
│   ├── trading-agent/         Autonomous trader with buybacks
│   └── content-agent/         Content generation service
│
└── docs/
    ├── GETTING_STARTED.md
    └── CONTRIBUTING.md
```

## Skills

| Skill | Description |
|-------|-------------|
| [payments](skills/payments/) | Accept payments in SOL or USDC, build transactions, verify invoices onchain. Extended from pump-fun-skills. |
| [identity](skills/identity/) | Define the agent's personality, voice, knowledge, and context. Templates included. |
| [services](skills/services/) | Configure what the agent sells. Service definitions, pricing tiers, delivery logic. |
| [trading](skills/trading/) | Autonomous trading with signal evaluation, position sizing, risk management, and execution. |
| [social](skills/social/) | Post to X, announce buybacks, share revenue stats, engage with the community. |
| [analytics](skills/analytics/) | Track revenue, monitor buyback history, measure token performance, report agent health. |
| [onchain](skills/onchain/) | Read wallets, monitor transactions, log activity to Solana, query token data. |

## How It Works

Every skill follows the [Agent Skills](https://agentskills.io) format. YAML frontmatter at the top, markdown instructions below. Point your AI agent at a skill directory and it loads the instructions into its context.

The skills are designed to work together. An agent loads the identity skill to know who it is, the services skill to know what it sells, the payments skill to collect revenue, and the analytics skill to track performance. Or it loads just the skills it needs. Each skill is independent.

## Quick Start

Clone the repo.

```bash
git clone https://github.com/tokenexplor4r/Agent-md.git
cd Agent-md
```

Install the payments SDK (required for revenue collection).

```bash
npm install @pump-fun/agent-payments-sdk@3.0.0 @solana/web3.js@^1.98.0
```

Copy the identity templates and fill them in with your agent's details.

```bash
cp skills/identity/templates/AGENT.template.md my-agent/AGENT.md
cp skills/identity/templates/VOICE.template.md my-agent/VOICE.md
cp skills/identity/templates/CONTEXT.template.md my-agent/CONTEXT.md
```

Point your agent at the skills it needs.

```
Read the files in skills/identity/, skills/payments/, and skills/services/ and follow their instructions.
```

## Compatibility

Agent.md skills work with any AI agent framework that supports skill files. Claude Code, OpenClaw, Eliza, custom frameworks. If the agent can read a markdown file and follow instructions, it can use these skills.

The payments skill requires a Tokenized Agent token on pump.fun. Other skills work with any Solana based agent.

## Links

| | |
|---|---|
| X | [@Agent_md_](https://x.com/Agent_md_) |
| Dev | [@tokenexplor4r](https://x.com/tokenexplor4r) |
| GitHub | [tokenexplor4r/Agent-md](https://github.com/tokenexplor4r/Agent-md) |
| Upstream | [pump-fun/pump-fun-skills](https://github.com/pump-fun/pump-fun-skills) |
| Domain | [agent-md.fun](https://agent-md.fun) |

## Contributing

See [CONTRIBUTING.md](docs/CONTRIBUTING.md). Add a skill, improve an existing one, submit an example agent.

## License

MIT
