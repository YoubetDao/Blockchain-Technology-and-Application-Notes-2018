# Building a decentralized monetary system

We assume the central bank plans to issue digital currency, which, intuitively, would draw on the model of cash issuance.

The characteristics of cash are:

1. Anti-counterfeiting marks: Difficult to forge, unalterable.
2. Uniqueness: Non-reproducible.



To implement anti-counterfeiting measures for digital currency, leveraging a public-private key system would be straightforward. Issuing a 100 dollars would require a signature from the central bank's private key, with the public key being publicly available for anyone to verify its authenticity. The public-private key system ensures immutability but does not address the uniqueness of the currency, as the code itself, being a file, can be infinitely replicated.



If digital currencies could be infinitely duplicated, it would be easy to generate double-spending attacks, where a currency issued by the central bank is spent multiple times.



A centralized solution to prevent double-spending attacks involves assigning each issued currency a unique identifier and keeping track of who holds it. Each transaction requires confirmation from the central bank. This method can be referred to as the accounting approach.



The issues with centralized ledger systems lie in the fact that all transactions must be validated for legality by the central bank, yet centralized systems come with their own set of problems. If a decentralized monetary system is to be constructed, two urgent issues need to be addressed:
1. Who has the authority to issue currency?
2. How is the validity of transactions verified, and how is double spending prevented?



Every Bitcoin transaction includes inputs, outputs, and a signature.

1. The input section contains the source of the coins and the sender's public key hash.
2. The output section contains the recipient's public key hash.
3. The signature: a transaction signature made with the sender's private key.


An example of a transfer:

```
               ┌───────────┐    ┌──────────────┐     ┌─────────────┐              
               │           │    │              │     │             │              
 ┌─────────────┼────┐     ┌┼────▼──────┐     ┌─┼─────▼──────┐    ┌─┼────────────┐ 
 │ create coin │    │     │A┌──►B(5)   │     │ B┌───►C(2)   │    │ C────►E(7)   │ 
 │             ▼    │     │ │          │     │  │           │    │ │            │ 
 │       ─────►A(10)│◄────┤ │          │◄────┤  │           │◄───┤ │            │ 
 │                  │     │ └──►C(5)   │     │  └───►D(3)   │    │ │            │ 
 │                  │     │ signed by A│     │  signed by B │    │ │signed by C │ 
 └──────────────────┘     └─────▲──────┘     └──────────────┘    └─┼────────────┘ 
                                │                                  │              
                                │                                  │              
                                └──────────────────────────────────┘              
                                                                                            
                                                                                  
```

The input section specifying the origin of the coins is crucial as it addresses the double-spending issue: 
if B attempts to transfer 5 coins to F's account (a public key conversion) at the end, but B has already spent them in the third transaction, the subsequent transfer will fail.

In the transaction where A transfers to B, the input includes A's public key hash, which is used to verify the signature. 
However, both the public key hash and the transaction signature can be forged. Therefore, A's public key hash must match the public key hash of the recipient in the source of the coins.


> This raises a question: if someone maliciously matches A's public key hash and signature, wouldn't that allow them to forge transactions?
> My guess: Signatures cannot be forged, as A encrypts the entire transaction with their private key. Therefore, if you attempt to alter the transaction information, the signature would not match; if you were to copy the exact same transaction, the blockchain design mentioned earlier already prevents double-spending attacks, rendering the complete copy meaningless.



# Expansion in real-world systems

In real-world systems, each block contains numerous transactions, which are divided into block header and block body. Signatures are applied only to the block header, with the header's merkle root hash pointing to the block body. The transactions within the block body form a merkle tree, ensuring the immutability of the transactions.

The block consists of:
1. Block header:
   a. Revision
   b. Hash of the previous block header
   c. Merkle root hash: Ensures that transactions in the body cannot be tampered with
   d. Target: H(block header) <= target
   e. nonce
2. blocker body: stores a list of transactions organized into a Merkle tree, with the Merkle root hash held in the header ensuring the immutable nature of the transactions


# How are transactions recorded on the blockchain

> starts at 39 minutes in the video

The main issue in this section is: who gets to decide the order in which transactions are written into the blockchain?



##  Distributed Consensus

Blockchain is a distributed ledger, and the content of the ledger must achieve distributed consensus.

A simple example of distributed consensus is a distributed hash table.



There are many impossibility results in distributed systems:
1. FLP impossibility result: In an asynchronous system, consensus cannot be achieved even if only one member is faulty.
2. CAP theorem: Consistency, Availability, Partition tolerance. You can only have two out of the three. 
   a. but According to "Designing Data-Intensive Applications," P is inevitable. When P occurs, you must choose between CP or AP.
3. Paxos: It guarantees Consistency, but there's a small chance it might never reach consensus in certain situations.


## Bitcoin Consensus

Designing a Bitcoin consensus protocol to prevent malicious node attacks involves several key considerations.

A straightforward approach: Direct voting, but Issues with direct voting include:

1. The node has been consistently voting for candidate blocks that are deemed illegal.
2. Nodes abstaining from voting: administrative inaction.
3. Efficiency concerns: network latency, how long to wait for each round of voting.

However, a more significant issue is determining who has the right to vote (membership) in any voting-based scheme.

> The scheme based on voting in consortium chains is feasible as there are no malicious nodes.

The drawback of direct voting in public chains lies in the fact that, since the generation of accounts in Bitcoin is unlimited, malicious nodes can create an infinite number of accounts. When the number of created accounts exceeds half of the total, the voting itself becomes a form of malicious attack, known as the **Sybil attack**.

Bitcoin employs a clever mechanism to address this issue, using voting based on computational power (proof of work) rather than the number of accounts to prevent Sybil attacks. Every node has the capability to construct a candidate block locally, incorporating transactions it deems valid into the block. It then attempts various `nonce` values until the condition `H(block header) <= target` is met, thereby earning the right to mine, which grants the authority to write the next block into the Bitcoin blockchain. Once other nodes receive this block, they verify the legitimacy of its contents.



## Longest Valid Chain

Another question arises: Does a block that has passed the legitimacy check necessarily get accepted?

Consider the following example:

```plain
 ┌──────┐◄──┬──────┐◄───┬──────┐◄───┬──────┐ 
 │C->A  │   │      │◄-┐ │ A->B │    │      │ 
 └──────┘   └──────┘  │ └──────┘    └──────┘ 
                      │                       
                      └──┬──────┐             
                         │A->A' │             
                         └──────┘             
```



After A transfers to B, if A then transfers back to itself, it effectively reverses the A->B transaction.



A valid block may not necessarily be on the longest valid chain (Longest Valid Chain), and this is known as a forking attack.



The Bitcoin protocol stipulates that only blocks on the longest valid chain should be accepted.



Temporary forks that are not part of the longest chain are called orphan blocks and are discarded.

```plain
                                                  ┌───────┐◄──┬───────┐ 
                                              ┌───┤       │   │       │ 
 ┌──────┐◄──┬──────┐ ◄───┬──────┐◄───┬──────┐ │   └───────┘   └───────┘ 
 │C->A  │   │      │ ◄┐  │ A->B │    │      │◄┘                         
 └──────┘   └──────┘  │  └──────┘    └──────┘◄┐   ┌───────┐             
                      │                       └───┼       │             
                      │                           └───────┘             
                      └──┬──────┐                 orphan block          
                         │A->A' │                                       
                         └──────┘                                       
```

Why do nodes vie for the right to mine a block?


Nodes that secure the mining right are rewarded with newly minted coins (block reward). In Bitcoin, the coinbase transaction is the only method for issuing new bitcoins, and it does not require specifying the source of the coins.

Initially, each mined block generated 50 BTC. However, every 210,000 blocks (approximately four years), the reward for mining a block halves.
