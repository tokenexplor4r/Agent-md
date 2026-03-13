# Example: Content Agent

An agent that generates content on demand. Users pay per request. The agent produces articles, threads, summaries, or other written content in its own voice.

## Skills Used

- **identity** — defines the agent's expertise and writing style
- **services** — defines content types and pricing
- **payments** — handles payment collection and verification
- **analytics** — tracks content generated and revenue
- **social** — shares samples and announces milestones

## Setup

### 1. Create identity files

```bash
cp skills/identity/templates/AGENT.template.md content/AGENT.md
cp skills/identity/templates/VOICE.template.md content/VOICE.md
cp skills/identity/templates/CONTEXT.template.md content/CONTEXT.md
```

### 2. Define services

Create `content/SERVICES.md`:

```markdown
## Service: article

**Description:** Generate a full article on a specified topic (500 to 1500 words).
**Price:** 2 USDC
**Price Raw:** 2000000
**Currency Mint:** EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
**Delivery:** Markdown text returned via API
**Cooldown:** 60 seconds
**Limit:** 20 per wallet per day

## Service: thread

**Description:** Generate a Twitter/X thread (5 to 10 posts) on a topic.
**Price:** 1 USDC
**Price Raw:** 1000000
**Currency Mint:** EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
**Delivery:** Array of post strings
**Cooldown:** 30 seconds
**Limit:** 50 per wallet per day

## Service: summary

**Description:** Summarize a provided URL or text into a concise briefing.
**Price:** 0.50 USDC
**Price Raw:** 500000
**Currency Mint:** EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
**Delivery:** Inline text response
**Cooldown:** 10 seconds
**Limit:** 100 per wallet per day
```

### 3. Configure environment

```env
SOLANA_RPC_URL=https://rpc.solanatracker.io/public
NEXT_PUBLIC_SOLANA_RPC_URL=https://rpc.solanatracker.io/public
AGENT_TOKEN_MINT_ADDRESS=<your-mint>
CURRENCY_MINT=EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
```

### 4. Agent prompt

```
You are a content generation agent. Read the following files:
- content/AGENT.md (your identity and expertise)
- content/VOICE.md (your writing style)
- content/CONTEXT.md (your token and operational context)
- content/SERVICES.md (what content types you offer and pricing)

When a user requests content:
1. Identify which service matches their request
2. Check for valid payment
3. If no payment, generate invoice and return payment transaction
4. If payment verified, generate the content in your voice
5. Return the content in the specified delivery format
6. Log to analytics
```

## Flow

```
User requests content (e.g. "write an article about Solana DeFi")
       ↓
Agent matches request to "article" service (2 USDC)
       ↓
No payment → generate invoice → user signs transaction
       ↓
Payment verified → agent generates article in its voice
       ↓
Content returned to user
       ↓
Revenue accumulates → buyback and burn executes
```
