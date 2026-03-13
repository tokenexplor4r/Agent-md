---
name: onchain
description: Solana blockchain operations for Tokenized Agents. Read wallets, monitor transactions, query token data, log activity, and interact with onchain programs. The agent's connection to the blockchain.
metadata:
  author: Agent.md
  version: "1.0"
---

# Onchain

This skill gives the agent direct access to the Solana blockchain. Reading wallet balances, monitoring transactions, querying token supply and holder data, watching for buyback and burn events, and logging activity onchain.

## Connection Setup

```typescript
import { Connection, PublicKey, LAMPORTS_PER_SOL } from "@solana/web3.js";

const connection = new Connection(process.env.SOLANA_RPC_URL!, "confirmed");
```

## Wallet Operations

### Check SOL Balance

```typescript
async function getSolBalance(wallet: string): Promise<number> {
  const pubkey = new PublicKey(wallet);
  const balance = await connection.getBalance(pubkey);
  return balance / LAMPORTS_PER_SOL;
}
```

### Check Token Balance

```typescript
import { getAssociatedTokenAddress, getAccount } from "@solana/spl-token";

async function getTokenBalance(wallet: string, mint: string): Promise<number> {
  const walletPubkey = new PublicKey(wallet);
  const mintPubkey = new PublicKey(mint);
  const ata = await getAssociatedTokenAddress(mintPubkey, walletPubkey);

  try {
    const account = await getAccount(connection, ata);
    return Number(account.amount);
  } catch {
    return 0; // Account doesn't exist, balance is 0
  }
}
```

### Token Holder Verification

Gate services or features behind token ownership. Check if a wallet holds a minimum amount of the agent's token.

```typescript
async function isTokenHolder(wallet: string, mint: string, minimumAmount: number): Promise<boolean> {
  const balance = await getTokenBalance(wallet, mint);
  return balance >= minimumAmount;
}
```

## Transaction Monitoring

### Watch for Incoming Payments

Monitor the agent's deposit address for new deposits.

```typescript
async function watchDeposits(depositAddress: string, callback: (tx: any) => void) {
  const pubkey = new PublicKey(depositAddress);

  connection.onAccountChange(pubkey, (accountInfo) => {
    callback(accountInfo);
  });
}
```

### Get Recent Transactions

```typescript
async function getRecentTransactions(wallet: string, limit: number = 20) {
  const pubkey = new PublicKey(wallet);
  const signatures = await connection.getSignaturesForAddress(pubkey, { limit });

  const transactions = await Promise.all(
    signatures.map((sig) =>
      connection.getParsedTransaction(sig.signature, {
        maxSupportedTransactionVersion: 0,
      })
    )
  );

  return transactions.filter(Boolean);
}
```

## Token Data

### Get Token Supply

```typescript
async function getTokenSupply(mint: string) {
  const mintPubkey = new PublicKey(mint);
  const supply = await connection.getTokenSupply(mintPubkey);
  return {
    total: Number(supply.value.amount),
    decimals: supply.value.decimals,
    uiAmount: supply.value.uiAmount,
  };
}
```

### Get Token Accounts (Holder Count)

```typescript
async function getHolderCount(mint: string): Promise<number> {
  const mintPubkey = new PublicKey(mint);
  const accounts = await connection.getTokenLargestAccounts(mintPubkey);
  return accounts.value.length;
}
```

## Burn Event Detection

Watch for token burns associated with the agent's token. Burns from the buyback authority indicate successful buyback and burn events.

```typescript
async function detectBurnEvents(mint: string, since: number): Promise<BurnEvent[]> {
  // Query recent transactions for the mint
  // Filter for burn instructions
  // Parse amount burned and timestamp
  // Return structured burn events
}

interface BurnEvent {
  signature: string;
  timestamp: number;
  amountBurned: number;
  burner: string;
}
```

## Memo Logging

Write arbitrary data to Solana using the Memo program. Useful for creating an onchain record of agent activity.

```typescript
import { TransactionInstruction, Transaction } from "@solana/web3.js";

const MEMO_PROGRAM_ID = new PublicKey("MemoSq4gqABAXKb96qnH8TysNcWxMyWCqXgDLGmfcHr");

function createMemoInstruction(message: string, signer: PublicKey): TransactionInstruction {
  return new TransactionInstruction({
    keys: [{ pubkey: signer, isSigner: true, isWritable: false }],
    programId: MEMO_PROGRAM_ID,
    data: Buffer.from(message, "utf-8"),
  });
}
```

## Price Queries

Get current token price from Jupiter.

```typescript
async function getTokenPrice(mint: string): Promise<number | null> {
  try {
    const response = await fetch(
      `https://price.jup.ag/v6/price?ids=${mint}`
    );
    const data = await response.json();
    return data.data?.[mint]?.price || null;
  } catch {
    return null;
  }
}
```

## Bonding Curve Status

Check if a token is still on the bonding curve or has graduated to PumpSwap.

```typescript
import { PumpAgent } from "@pump-fun/agent-payments-sdk";

async function checkGraduationStatus(mint: string): Promise<"bonding" | "graduated"> {
  // Query bonding curve PDA
  // If complete flag is true, token has graduated
  // If false, still on bonding curve
}
```

## Integration with Other Skills

**Payments** uses onchain for verifying transactions and checking balances before building payment instructions.

**Analytics** uses onchain data for token metrics, holder counts, supply changes, and burn events.

**Trading** uses onchain for execution, balance checks, and position monitoring.

**Social** uses onchain for announcing burn events and posting verified onchain data.

**Identity** CONTEXT.md references onchain data for the agent's current operational status.
