---
name: payments
description: Accept payments in SOL or USDC for Pump Tokenized Agents, build accept-payment transactions, verify invoices onchain. Extended from pump-fun/pump-fun-skills with additional patterns for service delivery, retry logic, and multi-currency support.
metadata:
  author: Agent.md
  version: "1.0"
  upstream: pump-fun/pump-fun-skills/tokenized-agents
---

# Payments

This skill handles revenue collection for Tokenized Agents on Pump.fun. It is an extended version of the [official pump-fun payments skill](https://github.com/pump-fun/pump-fun-skills/blob/main/tokenized-agents/SKILL.md) with additional patterns for integration with other Agent.md skills.

## Core SDK

The payment system is built on `@pump-fun/agent-payments-sdk`. The SDK handles two operations: building payment transactions and verifying that invoices have been paid.

```bash
npm install @pump-fun/agent-payments-sdk@3.0.0 @solana/web3.js@^1.98.0
```

## Required Configuration

Before writing any code, the following must be provided:

1. **Agent token mint address** from pump.fun
2. **Payment currency** (USDC or SOL)
3. **Price per service** in the currency's smallest unit (6 decimals for USDC, 9 for SOL)
4. **Solana RPC URL** that supports sendTransaction

```env
SOLANA_RPC_URL=https://rpc.solanatracker.io/public
NEXT_PUBLIC_SOLANA_RPC_URL=https://rpc.solanatracker.io/public
AGENT_TOKEN_MINT_ADDRESS=<your-agent-mint-address>
CURRENCY_MINT=EPjFWdd5AufqSSqeM2qN1xzybapC8G4wEGGkZwyTDt1v
```

## SDK Setup

```typescript
import { PumpAgent } from "@pump-fun/agent-payments-sdk";
import { Connection, PublicKey } from "@solana/web3.js";

const connection = new Connection(process.env.SOLANA_RPC_URL!);
const agentMint = new PublicKey(process.env.AGENT_TOKEN_MINT_ADDRESS!);
const agent = new PumpAgent(agentMint, "mainnet", connection);
```

## Building a Payment Transaction

Generate unique invoice parameters, build the transaction server side, serialize it, and send it to the client for signing.

```typescript
function generateInvoice(priceAmount: string) {
  const memo = String(Math.floor(Math.random() * 900000000000) + 100000);
  const now = Math.floor(Date.now() / 1000);
  return {
    amount: priceAmount,
    memo,
    startTime: String(now),
    endTime: String(now + 86400),
  };
}

async function buildPaymentTx(userWallet: string, invoice: ReturnType<typeof generateInvoice>) {
  const currencyMint = new PublicKey(process.env.CURRENCY_MINT!);
  const userPublicKey = new PublicKey(userWallet);

  const instructions = await agent.buildAcceptPaymentInstructions({
    user: userPublicKey,
    currencyMint,
    amount: invoice.amount,
    memo: invoice.memo,
    startTime: invoice.startTime,
    endTime: invoice.endTime,
  });

  const { blockhash } = await connection.getLatestBlockhash("confirmed");

  const tx = new Transaction();
  tx.recentBlockhash = blockhash;
  tx.feePayer = userPublicKey;
  tx.add(
    ComputeBudgetProgram.setComputeUnitPrice({ microLamports: 100_000 }),
    ...instructions,
  );

  return tx.serialize({ requireAllSignatures: false }).toString("base64");
}
```

## Verifying Payment

Always verify server side. Never trust the client.

```typescript
async function verifyPayment(params: {
  user: string;
  currencyMint: string;
  amount: number;
  memo: number;
  startTime: number;
  endTime: number;
}): Promise<boolean> {
  const invoiceParams = {
    user: new PublicKey(params.user),
    currencyMint: new PublicKey(params.currencyMint),
    amount: params.amount,
    memo: params.memo,
    startTime: params.startTime,
    endTime: params.endTime,
  };

  for (let attempt = 0; attempt < 10; attempt++) {
    const verified = await agent.validateInvoicePayment(invoiceParams);
    if (verified) return true;
    await new Promise((r) => setTimeout(r, 2000));
  }

  return false;
}
```

## Integration with Other Skills

The payments skill is the revenue engine. Other Agent.md skills plug into it:

**Services skill** defines what the agent sells and at what price. When a user requests a service, the services skill tells the payments skill what to charge. After payment verification, the services skill delivers.

**Analytics skill** reads payment events to track revenue over time. Every verified payment gets logged for reporting.

**Social skill** announces revenue milestones and buyback events based on payment activity.

**Onchain skill** monitors the agent's deposit address for incoming payments and token burns.

## Safety Rules

Never log or return private keys. Never sign transactions on behalf of a user. Always validate amount is greater than zero. Always ensure endTime is after startTime. Always verify payments server side before delivering any service.

## Buyback Mechanics

A portion of every payment is automatically used for token buybacks and burns by pump.fun's buyback authority. The buyback percentage is set by the token creator and can be adjusted at any time. Buybacks execute hourly when at least $10 worth of revenue has accumulated. The cadence is probabilistic to prevent frontrunning. All bought back tokens are permanently burned.

## Full Reference

For the complete SDK documentation including wallet integration, transaction serialization, and troubleshooting, see the [upstream skill](https://github.com/pump-fun/pump-fun-skills/blob/main/tokenized-agents/SKILL.md).
