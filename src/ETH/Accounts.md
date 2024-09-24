# P15 - Accounts

## Account-Based Ledger
Comparing Bitcoin and Ethereum:
- Bitcoin uses a **transaction-based ledger**, where account balances are inferred through `UTXO`.
- Ethereum uses an **account-based ledger**, similar to a banking system, where accounts directly store `balance` states.

Differences in transfer mechanisms:
- Bitcoin: When transferring from account B to C, the source of coins must be specified, and every `UTXO` must be entirely spent at once, so any remaining coins should be transferred back to another self-owned account.
- Ethereum: The source of coins during a transfer is the `balance` state of the account, as the account includes the `balance` state, so it does not need to be entirely spent at once.

```txt
         Transaction-based Ledger                    Account-Based Ledger             
 ┌────────────────────────────────────────┐     ┌───────────────────────────────────┐ 
 │      ┌──────────────────┐              │     │                                   │ 
 │      │                  │              │     │                                   │ 
 │┌─────▼───────┐         ┌┼─────────────┐│     │┌─────────────┐    ┌─────────────┐ │ 
 ││A───►B(10BTC)│         │B─┬──►C(3BTC) ││     ││A───►B(10ETH)│    │B───►C(3ETH) │ │ 
 ││             │         │  │           ││     ││             │    │             │ │ 
 ││             │         │  └──►B'(7BTC)││     ││             │    │             │ │ 
 │└─────────────┘         └──────────────┘│     │└─────────────┘    └─────────────┘ │ 
 └────────────────────────────────────────┘     └───────────────────────────────────┘               
```
**Advantages** of the account-based model: No need to consider the source of coins, inherently preventing double-spending attacks.

## Replay Attack
Although the **account-based ledger** avoids double-spending attack, there is still a risk of **replay attack**.

**Replay Attack**: After transferring from account B to C, C maliciously broadcasts B's transaction later, resulting in B being debited twice.

**How to prevent Replay Attack?**

1. Add a `nonce` state to the account as a transaction counter, recording the number of transactions posted by the account, the `nonce` becomes part of the transaction and is signed during transfers.
2. Transactions with a `nonce` less than or equal to the account's current `nonce` are rejected.

```txt
┌─────────────────┐   
│ B -> C(10ETH)   │   
│                 │   
│   nonce = 21    │   
│                 │   
│    signed by A  │   
└─────────────────┘
```

## Smart Contract Accounts

Two types of accounts exist in the Ethereum system:
1. Externally Owned Accounts: Controlled by user's public-private key pairs, these accounts have a `balance` and a `nonce`.
2. **Smart Contract Accounts**: In addition to `balance` and `nonce`, these accounts include `code` and `storage`.
    - When a contract is created, it returns an address that can be used to make calls to the contract. During these calls, the `code`remains unchanged.
    - Contract Accounts can't initiate a transaction on their own, all transactions must be initiated by external accounts.
    - Contract Accounts also have a `nonce`, which records the number of times the contract has been invoked.
    - A contract can call another contract.

## Why Ethereum use Account-Based Ledger?
Ethereum is designed to support smart contracts, which require stable account addresses for execution.

Therefore, the account-based ledger is more suitable than the transaction-based ledger.
