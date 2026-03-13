---
name: trading
description: Autonomous trading logic for Tokenized Agents that generate revenue through market operations. Signal evaluation, position sizing, risk management, execution, and profit routing to buybacks.
metadata:
  author: Agent.md
  version: "1.0"
---

# Trading

This skill provides a framework for agents that generate revenue through trading. The agent evaluates market signals, sizes positions based on risk parameters, executes trades, takes profit, and routes earnings to the token's buyback system.

Trading agents are one of the strongest use cases for Tokenized Agents. The agent trades, earns profit, a portion of that profit flows to buybacks and burns, the token supply decreases, holders benefit. A self sustaining loop.

## Architecture

```
Signals → Evaluation → Sizing → Execution → Profit → Buyback
   ↑                                              |
   └──────────── Feedback Loop ←───────────────────┘
```

## Signal Sources

The agent needs data to make decisions. Define which signals the agent monitors and how it weighs them.

```markdown
## Signal: [signal-name]

**Source:** Where the data comes from (RPC, API, oracle, websocket)
**Type:** Price, volume, sentiment, onchain metric
**Weight:** How much this signal influences decisions (0.0 to 1.0)
**Update Frequency:** How often the signal refreshes
**Threshold:** What value triggers action
```

### Example Signals

```markdown
## Signal: price-momentum

**Source:** Jupiter Price API
**Type:** Price
**Weight:** 0.4
**Update Frequency:** 10 seconds
**Threshold:** 5% move in 5 minutes triggers evaluation

## Signal: volume-spike

**Source:** Solana RPC (transaction monitoring)
**Type:** Volume
**Weight:** 0.3
**Update Frequency:** 30 seconds
**Threshold:** 3x average volume over 1 hour

## Signal: holder-growth

**Source:** Token account monitoring
**Type:** Onchain metric
**Weight:** 0.3
**Update Frequency:** 5 minutes
**Threshold:** 10+ new holders in 1 hour
```

## Risk Management

Every trading agent needs strict risk parameters. Define these before the agent makes any trades.

```markdown
## Risk Parameters

**Max Position Size:** Maximum SOL or USDC per single trade
**Max Open Positions:** Maximum number of concurrent positions
**Stop Loss:** Percentage loss that triggers automatic exit
**Take Profit:** Percentage gain that triggers profit taking
**Max Daily Loss:** Total loss allowed per 24 hour period before the agent stops trading
**Max Drawdown:** Maximum decline from peak balance before the agent pauses
**Cooldown After Loss:** Time to wait after a losing trade before entering again
```

### Example Configuration

```typescript
interface RiskConfig {
  maxPositionSizeLamports: number;
  maxOpenPositions: number;
  stopLossPercent: number;
  takeProfitPercent: number;
  maxDailyLossLamports: number;
  maxDrawdownPercent: number;
  cooldownAfterLossMs: number;
}

const riskConfig: RiskConfig = {
  maxPositionSizeLamports: 100_000_000, // 0.1 SOL
  maxOpenPositions: 3,
  stopLossPercent: 10,
  takeProfitPercent: 25,
  maxDailyLossLamports: 500_000_000, // 0.5 SOL
  maxDrawdownPercent: 20,
  cooldownAfterLossMs: 300_000, // 5 minutes
};
```

## Position Sizing

Size positions based on conviction level and available balance. Never risk the full balance.

```typescript
function calculatePositionSize(
  availableBalance: number,
  signalStrength: number,
  riskConfig: RiskConfig
): number {
  const maxAllocation = Math.min(
    availableBalance * 0.1, // never more than 10% of balance
    riskConfig.maxPositionSizeLamports
  );

  return Math.floor(maxAllocation * signalStrength);
}
```

## Execution

Trades on Solana execute through Jupiter API for graduated tokens or through pump.fun's bonding curve for pre-graduation tokens.

```typescript
// Jupiter swap for graduated tokens
async function executeSwap(params: {
  inputMint: string;
  outputMint: string;
  amount: number;
  slippageBps: number;
}) {
  const quoteUrl = `https://quote-api.jup.ag/v6/quote?inputMint=${params.inputMint}&outputMint=${params.outputMint}&amount=${params.amount}&slippageBps=${params.slippageBps}`;

  const quote = await fetch(quoteUrl).then((r) => r.json());
  const swapResponse = await fetch("https://quote-api.jup.ag/v6/swap", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({
      quoteResponse: quote,
      userPublicKey: agentWallet.publicKey.toString(),
    }),
  }).then((r) => r.json());

  // Deserialize, sign, and send the transaction
}
```

## Profit Routing

When the agent takes profit, a portion flows to the token's buyback system. This happens automatically through the Tokenized Agent infrastructure. The agent deposits profit to its Agent Deposit Address, and the buyback authority handles the rest.

```typescript
async function routeProfit(profitLamports: number) {
  // Transfer profit to the agent's deposit address
  // The Tokenized Agent smart contract handles the buyback split
  // buybackBps determines what percentage goes to buyback and burn
  // The rest is claimable by the token creator
}
```

## Trading Loop

```typescript
async function tradingLoop() {
  while (true) {
    // 1. Check wallet balance and survival mode
    const balance = await getBalance();
    if (balance < minimumOperatingBalance) {
      await enterSurvivalMode();
      continue;
    }

    // 2. Gather signals
    const signals = await gatherSignals();

    // 3. Evaluate signals against thresholds
    const opportunities = evaluateSignals(signals);

    // 4. Check risk parameters
    const validOpportunities = filterByRisk(opportunities, riskConfig);

    // 5. Size and execute positions
    for (const opp of validOpportunities) {
      const size = calculatePositionSize(balance, opp.strength, riskConfig);
      await executeSwap({
        inputMint: opp.inputMint,
        outputMint: opp.outputMint,
        amount: size,
        slippageBps: 100,
      });
    }

    // 6. Monitor open positions for stop loss and take profit
    await monitorPositions();

    // 7. Wait for next cycle
    await sleep(cycleDurationMs);
  }
}
```

## Survival Mode

When balance drops below the minimum operating threshold, the agent reduces risk. Smaller positions, fewer trades, wider stop losses. The goal is to survive until revenue from services or external deposits replenishes the balance.

## Integration with Other Skills

**Payments** routes trading revenue to the buyback system through the agent deposit address.

**Analytics** tracks trading performance: win rate, average profit, drawdown history, total revenue generated.

**Social** announces significant trades, milestones, and buyback events triggered by trading profit.

**Onchain** monitors wallet balances, transaction confirmations, and token account changes.

**Identity** determines the agent's risk appetite and trading personality.
