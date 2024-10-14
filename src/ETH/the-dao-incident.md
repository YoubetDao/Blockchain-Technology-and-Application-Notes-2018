# P23 The DAO Incident

## DAO
In the previous section, we discussed the principles of **reentrancy attacks**. In this section, we will explore a real-world case that is famous in Ethereum's history and led to a split in the Ethereum network.

Bitcoin introduced the concept of decentralized currency, while Ethereum brought forth decentralized contracts. This gave rise to the idea of **Decentralized Everything**, The concept of `DAO`  emerged in this context.

`DAO` stands for **Decentralized Autonomous Organization**. In the blockchain world, the rules and regulations of an organization are written in code and maintained through the consensus protocol of the blockchain.

## The DAO

In May 2016, a crowdfunding investment project known as `The DAO` was launched.

`The DAO` is a decentralized crowdfunding investment fund, which should not be confused with the general concept of `DAO`.

Unlike traditional venture capital funds, `The DAO` operated as a democratic investment fund. Within a month, it raised $150 million worth of Ether, achieving a rare speed and scale in crowdfunding.

## How The DAO Works

Here’s how `The DAO` works:

1. **Mechanism**: It is a crowdfunding investment fund where the invested money is raised through crowdfunding on the blockchain. Essentially, It runs as a smart contract on Ethereum.
2. **Investment Decisions**: To participate in investments, sending `ETH` to the smart contract in exchange for `The DAO` tokens, then investment decisions are made through voting using these tokens.
3. **Profit Distribution**: Profits are distributed according to the rules set in the smart contract.

## SplitDAO

The purpose of `SplitDAO`:

1. **Establishing `ChildDAO`**: Some investors believed certain projects were worth funding but lacked majority support. In such cases, they could split off from `The DAO` to form their own `Child DAO`. After this split, their tokens would be converted into `ETH` and remain in the `Child DAO`. There was a 28-day lock-up period after the split, which provided a buffer time to address potential future attacks.

2. **Retrieving Investment Returns**: `The DAO` did not have a direct function to convert tokens back to Ether. However, investors could retrieve their funds through the `ChildDAO` created via `SplitDAO` .

## The Hacker Attack

Unfortunately, `The DAO` only survived for three months due to the hacker attack.

The hacker exploited a vulnerability in the implementation of `SplitDAO`.

Here’s a snippet of the relevant code:

```solidity
function splitDAO(
  uint _proposalID,
  address _newCurator
) noEther onlyTokenholders returns (bool _success) {
  ...
  // Burn DAO Tokens
  Transfer(msg.sender, 0, balances[msg.sender]);
  withdrawRewardFor(msg.sender);
  totalSupply -= balances[msg.sender];
  balances[msg.sender] = 0; 
  paidOut[msg.sender] = 0;
  return true;
}
```

In the `SplitDAO` code, the transfer occurs before the account balance is reset to zero. This is the **reentrancy attack vulnerability** discussed in the previous section, and hackers exploited this vulnerability to carry out the reentrancy attack.

> The correct approach would have been to zero out the account balance first and then perform the transfer.

## Remedial Measures

The Ethereum community was divided into two camps:

1. **Rollback Transactions**: From the perspective of protecting investors' interests, the Ethereum team supported remedial measures.

2. **Maintain the status**: Some argued that blockchains are immutable; the issue lay with the smart contract, not Ethereum itself.

Ultimately, the Ethereum community chose to rollback transactions. The remedy involved first prohibiting related transactions through a soft fork, then executing a hard fork to rollback the transactions.

1. **Soft Fork**: A rule has been added through software upgrade, disallowing any transactions involving accounts related to the `The DAO` fund. However, this upgrade contained a bug: it did not charge `gas fees` for illegal transactions, leading to low-cost attacks. Consequently, the version was rolled back, and the soft fork was deemed a failure.

2. **Hard Fork**: The tokens of `The DAO` were transferred to a new smart contract, which had the sole function of converting tokens to `ETH`. This transfer was automatically executed upon mining the 1,920,000th block.

A vote was held between supporters and opponents of the hard fork, ultimately favoring the hard fork. However, those against it chose to continue mining on the old chain.

After the hard fork, the new chain retained the Ether (ETH) symbol, while the old chain's Ether became known as Ethereum Classic (ETC).

Because both chains shared the same historical ledger and private keys, replay attacks were possible. To prevent this, a unique `chainID` was introduced to differentiate between the new and old chains.

