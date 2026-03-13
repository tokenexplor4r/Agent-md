---
name: services
description: Define and manage the services a Tokenized Agent sells. Use when configuring what the agent offers, pricing tiers, service delivery logic, and access control after payment verification.
metadata:
  author: Agent.md
  version: "1.0"
---

# Services

This skill defines what the agent sells. Every Tokenized Agent needs a revenue model. The services skill provides a framework for defining offerings, setting prices, gating access behind payment verification, and delivering results.

## Service Definition

Each service the agent offers should be defined with a clear structure. The agent reads this at startup and knows exactly what it can sell, what it charges, and how to deliver.

```markdown
## Service: [service-name]

**Description:** What this service does in one sentence.
**Price:** [amount] [currency] (e.g. 1 USDC, 0.01 SOL)
**Price Raw:** [amount in smallest unit] (e.g. 1000000 for 1 USDC)
**Currency Mint:** [mint address]
**Delivery:** How the result is delivered (inline response, file, API call, etc.)
**Cooldown:** Minimum time between requests from the same wallet (optional)
**Limit:** Maximum requests per wallet per day (optional)
```

## Example Services

### Chatbot Access

```markdown
## Service: chat

**Description:** Ask the agent any question within its knowledge domain.
**Price:** 0.10 USDC
**Price Raw:** 100000
**Currency Mint:** EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
**Delivery:** Inline text response
**Cooldown:** None
**Limit:** 100 per wallet per day
```

### Content Generation

```markdown
## Service: generate-content

**Description:** Generate a blog post, thread, or article on a specified topic.
**Price:** 1 USDC
**Price Raw:** 1000000
**Currency Mint:** EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
**Delivery:** Markdown file returned via API
**Cooldown:** 60 seconds
**Limit:** 20 per wallet per day
```

### Data Query

```markdown
## Service: query-data

**Description:** Query onchain data, token analytics, or market information.
**Price:** 0.001 SOL
**Price Raw:** 1000000
**Currency Mint:** So11111111111111111111111111111111111111112
**Delivery:** JSON response
**Cooldown:** 5 seconds
**Limit:** 500 per wallet per day
```

### Image Generation

```markdown
## Service: generate-image

**Description:** Generate an AI image based on a text prompt.
**Price:** 0.50 USDC
**Price Raw:** 500000
**Currency Mint:** EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
**Delivery:** Image URL returned via API
**Cooldown:** 30 seconds
**Limit:** 50 per wallet per day
```

## Service Delivery Flow

The flow connects the services skill to the payments skill:

```
1. User requests a service
       ↓
2. Agent looks up the service definition (price, currency, limits)
       ↓
3. Agent checks rate limits and cooldowns for the wallet
       ↓
4. Agent calls payments skill to generate invoice and build transaction
       ↓
5. User signs and submits transaction
       ↓
6. Agent calls payments skill to verify payment
       ↓
7. Payment verified → agent delivers the service
   Payment failed → agent asks user to retry
```

## Implementation Pattern

```typescript
interface ServiceConfig {
  name: string;
  description: string;
  priceRaw: string;
  currencyMint: string;
  deliveryType: "inline" | "file" | "api" | "url";
  cooldownSeconds: number;
  dailyLimit: number;
}

const services: Record<string, ServiceConfig> = {
  chat: {
    name: "chat",
    description: "Ask the agent any question within its knowledge domain.",
    priceRaw: "100000",
    currencyMint: "EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v",
    deliveryType: "inline",
    cooldownSeconds: 0,
    dailyLimit: 100,
  },
};

function getServiceConfig(serviceName: string): ServiceConfig | null {
  return services[serviceName] || null;
}

async function handleServiceRequest(serviceName: string, userWallet: string) {
  const config = getServiceConfig(serviceName);
  if (!config) throw new Error("Unknown service");

  // Check rate limits
  // Generate invoice using payments skill
  // Build and return transaction
  // After payment verification, deliver the service
}
```

## Pricing Strategy

Set prices based on the cost to deliver the service plus margin. Consider:

**Compute costs** for AI inference, data queries, or image generation. The agent should at minimum cover its own operating costs.

**Buyback allocation** means a portion of every payment goes to token buybacks. Factor this into pricing so the agent remains profitable after the buyback split.

**Market rates** for similar services. Price too high and nobody pays. Price too low and the agent cannot sustain itself.

## Access Tiers

For agents that want to offer different levels of access:

```markdown
## Tier: Free
**Services:** Basic chat (limited to 5 per day)
**Price:** 0

## Tier: Standard
**Services:** Chat, content generation
**Price:** 1 USDC per request

## Tier: Premium
**Services:** All services, priority queue, higher limits
**Price:** 5 USDC per request
```

The agent checks the tier based on payment amount or token holdings in the user's wallet.

## Integration with Other Skills

**Payments** handles the transaction building and verification for every service request.

**Identity** ensures service delivery matches the agent's voice and personality.

**Analytics** tracks which services generate the most revenue and usage patterns.

**Onchain** can gate premium services behind token holder verification (must hold X amount of the agent's token).
