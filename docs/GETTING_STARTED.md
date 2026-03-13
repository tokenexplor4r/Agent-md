# Getting Started

## Prerequisites

A token launched on pump.fun with the Tokenized Agent setting enabled. Node.js 18 or higher. An AI agent framework that can read markdown files (Claude Code, OpenClaw, Eliza, or any custom setup).

## Step 1: Launch Your Token

Go to [pump.fun](https://pump.fun) and create your token. During or after creation, enable the "Tokenized Agent" toggle on your token's page. Set your buyback percentage (the portion of revenue that goes to automatic buyback and burn).

Save your token's mint address. You will need it for everything.

## Step 2: Clone Agent.md

```bash
git clone https://github.com/tokenexplor4r/Agent-md.git
cd Agent-md
```

## Step 3: Install Dependencies

```bash
npm install @pump-fun/agent-payments-sdk@3.0.0 @solana/web3.js@^1.98.0
```

## Step 4: Build Your Agent's Identity

Copy the identity templates and fill them in.

```bash
mkdir my-agent
cp skills/identity/templates/AGENT.template.md my-agent/AGENT.md
cp skills/identity/templates/VOICE.template.md my-agent/VOICE.md
cp skills/identity/templates/CONTEXT.template.md my-agent/CONTEXT.md
```

Edit each file with your agent's specific details. The CONTEXT.md needs your token mint address, payment currency, pricing, and RPC endpoints.

## Step 5: Define Your Services

Decide what your agent sells. Create a SERVICES.md file following the services skill format. See `skills/services/SKILL.md` for templates and examples.

## Step 6: Configure Environment

Create a `.env` file:

```env
SOLANA_RPC_URL=https://rpc.solanatracker.io/public
NEXT_PUBLIC_SOLANA_RPC_URL=https://rpc.solanatracker.io/public
AGENT_TOKEN_MINT_ADDRESS=<your-token-mint>
CURRENCY_MINT=EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
```

## Step 7: Point Your Agent at the Skills

Load the relevant skill files into your agent's context. At minimum, load identity and payments. Add services, trading, social, analytics, and onchain as needed.

```
Read the following skill files and follow their instructions:
- my-agent/AGENT.md
- my-agent/VOICE.md
- my-agent/CONTEXT.md
- my-agent/SERVICES.md
- skills/payments/SKILL.md
- skills/services/SKILL.md
```

## Step 8: Test

Run an end to end test. Have a user (or yourself) request a service, pay the invoice, and verify that the agent delivers the service and the revenue shows up in your agent's deposit address.

Buybacks execute hourly when at least $10 worth of revenue has accumulated.

## Next Steps

Add more skills as your agent grows. Use the analytics skill to track performance. Use the social skill to build a public presence. Use the trading skill if your agent generates revenue through market operations.
