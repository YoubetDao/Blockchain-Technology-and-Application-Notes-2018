# P11 - Accounts

## Account-Based Ledger

Comparing Bitcoin and Ethereum:
- Bitcoin use a **transaction-based ledger**, where account balances are inferred through `UTXO`.
- Ethereum employs an account-based ledger, similar to a banking system, where accounts directly store balance states.

Differences in transfer mechanisms:
- Bitcoin: When transferring from account B to C, the source of coins must be specified, and the entire amount must be spent at once. Excess funds are transferred back to another self-owned account.
- Ethereum: Transfers are directly deducted from the account's balance state.

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
**Advantages** of the account-based model: No need to consider the origin of coins, inherently preventing double-spending attacks.

## Replay Attack

**Replay Attack**: After account A transfers money to account B, B maliciously resubmits A's transaction later, resulting in A being debited twice.

How to prevent Replay Attack?

1. Add a `nonce` state to the account as a transaction counter, recording the number of transactions posted by the account, the `nonce` becomes part of the transaction and is signed during transfers. 
2. Transactions with a `nonce` less than or equal to the account's current nonce are rejected.

```txt
┌─────────────────┐   
│ A -> B(10ETH)   │   
│                 │   
│   nonce = 21    │   
│                 │   
│    signed by A  │   
└─────────────────┘
```

## Smart Contract Accounts

Two types of accounts exist in the Ethereum system:
1. Externally Owned Accounts: Controlled by user's public-private key pairs, these accounts have a `balance` and a `nonce`.
2. **Smart Contract Accounts**: In addition to balance and nonce, there are code and storage
    - An address is returned when a contract is created, allowing calls to be made to it. During these calls, the code remains unchanged, but the state is altered.
    - A contract can call another contract.
    - Contract Accounts also have a `nonce`, which records the number of times the contract has been invoked.
    - Contract Accounts can't initiate a transaction on their own, all transactions must be initiated by external accounts.
