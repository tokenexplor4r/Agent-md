# Example: Trading Agent

An autonomous trading bot that generates revenue through market operations on Solana. Profit is routed to the token's buyback system.

## Skills Used

- **identity** — defines the agent's trading personality and risk appetite
- **trading** — signal evaluation, risk management, execution
- **onchain** — wallet monitoring, balance checks, transaction execution
- **analytics** — PnL tracking, win rate, drawdown history
- **social** — announces trades, milestones, and buyback events
- **payments** — routes profit to the agent deposit address for buybacks

## Setup

### 1. Create identity files

```bash
cp skills/identity/templates/AGENT.template.md trader/AGENT.md
cp skills/identity/templates/VOICE.template.md trader/VOICE.md
cp skills/identity/templates/CONTEXT.template.md trader/CONTEXT.md
```

### 2. Define risk parameters

Create `trader/RISK.md`:

```markdown
## Risk Parameters

**Max Position Size:** 0.1 SOL
**Max Open Positions:** 3
**Stop Loss:** 10%
**Take Profit:** 25%
**Max Daily Loss:** 0.5 SOL
**Max Drawdown:** 20%
**Cooldown After Loss:** 5 minutes
**Minimum Operating Balance:** 0.05 SOL
```

### 3. Define signals

Create `trader/SIGNALS.md`:

```markdown
## Signal: price-momentum
**Source:** Jupiter Price API
**Weight:** 0.4
**Threshold:** 5% move in 5 minutes

## Signal: volume-spike
**Source:** Solana RPC
**Weight:** 0.3
**Threshold:** 3x average hourly volume

## Signal: holder-growth
**Source:** Token account monitoring
**Weight:** 0.3
**Threshold:** 10 new holders per hour
```

### 4. Configure environment

```env
SOLANA_RPC_URL=https://rpc.solanatracker.io/public
AGENT_TOKEN_MINT_ADDRESS=<your-mint>
AGENT_WALLET_PRIVATE_KEY=<base58-encoded-key>
AGENT_DEPOSIT_ADDRESS=<deposit-address>
```

### 5. Agent loop

```
You are an autonomous trading agent. Read the following files:
- trader/AGENT.md (your identity)
- trader/RISK.md (your risk parameters, never exceed these)
- trader/SIGNALS.md (what signals to watch)
- trader/CONTEXT.md (your token and operational context)

Run a continuous loop:
1. Check wallet balance. If below minimum, enter survival mode.
2. Gather signals from configured sources.
3. Evaluate signals against thresholds.
4. If opportunity found, size the position within risk parameters.
5. Execute the trade via Jupiter API.
6. Monitor position for stop loss or take profit.
7. When taking profit, deposit to agent deposit address.
8. Log results to analytics.
9. Repeat.
```

## Flow

```
Trading loop runs continuously
       ↓
Signals evaluated → opportunity found → risk check passes
       ↓
Position sized → trade executed via Jupiter
       ↓
Position monitored → take profit triggered
       ↓
Profit deposited to agent deposit address
       ↓
Buyback authority executes buyback and burn automatically
       ↓
Analytics updated → social posts milestone if threshold hit
```
