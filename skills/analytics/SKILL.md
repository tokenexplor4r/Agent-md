---
name: analytics
description: Track revenue, monitor buyback and burn history, measure token performance, and report agent health. Use when the agent needs to know its own performance metrics or share stats publicly.
metadata:
  author: Agent.md
  version: "1.0"
---

# Analytics

This skill tracks everything the agent does and everything that happens to its token. Revenue earned, services delivered, tokens burned, buyback history, agent uptime, and operational health. The agent uses this data internally to make decisions and externally to share stats with the community.

## Metrics

### Revenue

```typescript
interface RevenueMetrics {
  totalRevenueLamports: number;      // Total SOL earned
  totalRevenueUsdc: number;          // Total USDC earned
  revenueToday: number;              // Revenue in last 24 hours
  revenueThisWeek: number;           // Revenue in last 7 days
  revenueByService: Record<string, number>; // Breakdown by service name
  invoicesVerified: number;          // Total successful payments
  invoicesFailed: number;            // Total failed/expired payments
}
```

### Buyback and Burn

```typescript
interface BuybackMetrics {
  totalTokensBurned: number;         // Cumulative tokens removed from supply
  totalBuybackValue: number;         // Total SOL/USDC spent on buybacks
  buybackCount: number;              // Number of buyback events
  lastBuybackTimestamp: number;      // Unix timestamp of most recent buyback
  averageBuybackSize: number;        // Average tokens per buyback
  burnRate: number;                  // Tokens burned per day (rolling average)
}
```

### Token Performance

```typescript
interface TokenMetrics {
  currentPrice: number;
  marketCap: number;
  holderCount: number;
  circulatingSupply: number;         // Total supply minus burned
  totalBurned: number;
  priceChange24h: number;
  volumeToday: number;
}
```

### Agent Health

```typescript
interface HealthMetrics {
  uptime: number;                    // Seconds since last restart
  requestsServed: number;           // Total requests processed
  averageResponseTime: number;      // Milliseconds
  errorRate: number;                // Percentage of failed requests
  walletBalance: number;            // Current operating balance
  lastActivity: number;             // Unix timestamp
}
```

## Data Collection

The agent collects analytics data from multiple sources.

**Payment events** come from the payments skill. Every verified invoice gets logged with amount, currency, service name, wallet address, and timestamp.

**Buyback events** come from monitoring the token's onchain activity. Watch for burn transactions associated with the token mint.

**Token data** comes from Solana RPC calls and price APIs. Query token accounts for holder count, supply data, and balances.

**Health data** comes from the agent's own runtime. Track uptime, request count, errors, and response times internally.

```typescript
async function collectBuybackEvents(mintAddress: string): Promise<BuybackEvent[]> {
  // Monitor the token mint for burn events
  // Query transaction history for the agent deposit address
  // Parse buyback and burn transactions
  // Return structured events with timestamps and amounts
}

async function collectTokenMetrics(mintAddress: string): Promise<TokenMetrics> {
  // Query Jupiter or DexScreener for price data
  // Query Solana RPC for supply and holder data
  // Calculate derived metrics
}
```

## Reporting

The agent should be able to generate reports on demand and on schedule.

### Summary Report

```markdown
## Agent Report — [date]

**Revenue:** [total] [currency] ([change]% vs previous period)
**Services Delivered:** [count] requests
**Buybacks:** [count] events, [tokens] tokens burned
**Token Price:** [price] ([change]%)
**Holders:** [count] ([change] new)
**Uptime:** [duration]
**Health:** [status]
```

### Revenue Breakdown

```markdown
## Revenue by Service

| Service | Requests | Revenue | % of Total |
|---------|----------|---------|------------|
| chat    | [count]  | [amount]| [percent]  |
| content | [count]  | [amount]| [percent]  |
| query   | [count]  | [amount]| [percent]  |
```

## Alerting

Define conditions that trigger alerts. Alerts can be sent to the social skill for public posting or kept internal.

```typescript
interface AlertRule {
  metric: string;
  condition: "above" | "below" | "change";
  threshold: number;
  action: "post" | "log" | "pause";
  message: string;
}

const alerts: AlertRule[] = [
  {
    metric: "walletBalance",
    condition: "below",
    threshold: 50_000_000, // 0.05 SOL
    action: "pause",
    message: "Balance critically low. Pausing operations.",
  },
  {
    metric: "totalTokensBurned",
    condition: "above",
    threshold: 1_000_000,
    action: "post",
    message: "1M tokens burned milestone reached.",
  },
  {
    metric: "errorRate",
    condition: "above",
    threshold: 10,
    action: "log",
    message: "Error rate exceeds 10%. Investigating.",
  },
];
```

## Integration with Other Skills

**Payments** feeds revenue data to analytics with every verified invoice.

**Social** pulls metrics from analytics for buyback announcements, milestone posts, and status updates.

**Trading** reports PnL data, win rates, and drawdowns to analytics for performance tracking.

**Onchain** provides raw transaction data that analytics processes into structured metrics.

**Identity** context file gets updated with current metrics so the agent always knows its own status.
