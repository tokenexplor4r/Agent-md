# Example: Chatbot Agent

A paid chatbot that answers questions within a specific domain. Users pay per message. Revenue feeds token buybacks.

## Skills Used

- **identity** — defines who the chatbot is and how it talks
- **services** — defines the chat service and pricing
- **payments** — handles payment collection and verification
- **social** — posts status updates and revenue milestones
- **analytics** — tracks usage and revenue

## Setup

### 1. Create identity files

```bash
cp skills/identity/templates/AGENT.template.md chatbot/AGENT.md
cp skills/identity/templates/VOICE.template.md chatbot/VOICE.md
cp skills/identity/templates/CONTEXT.template.md chatbot/CONTEXT.md
```

Fill in the templates with your chatbot's personality, voice, and token details.

### 2. Define the service

Create `chatbot/SERVICES.md`:

```markdown
## Service: chat

**Description:** Ask any question about [your domain].
**Price:** 0.10 USDC
**Price Raw:** 100000
**Currency Mint:** EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
**Delivery:** Inline text response
**Cooldown:** None
**Limit:** 100 per wallet per day
```

### 3. Configure environment

```env
SOLANA_RPC_URL=https://rpc.solanatracker.io/public
NEXT_PUBLIC_SOLANA_RPC_URL=https://rpc.solanatracker.io/public
AGENT_TOKEN_MINT_ADDRESS=<your-mint>
CURRENCY_MINT=EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
PRICE_AMOUNT=100000
```

### 4. Agent prompt

```
You are a paid chatbot agent. Read the following files:
- chatbot/AGENT.md (your identity)
- chatbot/VOICE.md (how you communicate)
- chatbot/CONTEXT.md (your token and operational context)
- chatbot/SERVICES.md (what you sell)

When a user sends a message:
1. Check if they have a valid payment for the chat service
2. If not, generate an invoice and return a payment transaction
3. If yes, answer their question in your voice
4. Log the interaction to analytics
```

## Flow

```
User sends message
       ↓
Agent checks for valid payment
       ↓
No payment → generate invoice → return payment transaction → user signs
       ↓
Payment verified → agent responds in character → log to analytics
       ↓
Revenue accumulates → buyback and burn executes automatically
```
