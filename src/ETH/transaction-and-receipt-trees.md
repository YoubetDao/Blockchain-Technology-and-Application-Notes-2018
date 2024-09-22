# P17 Transaction and Receipt Trees

## Transaction Tree and Receipt Tree

**Transaction tree**: Every time a new block is released, the transactions in the block are organized into a transaction tree.

**Receipt tree**: After each transaction is executed, a receipt will be generated to record the relevant information of the transaction. All receipts will be organized into a receipt tree.

The nodes on the transaction tree and receipt tree are one-to-one corresponding. Their data structures are both **MPT**.

## Bloom filter

The **Bloom filter** data structure can support a relatively efficient search for whether an element is in a relatively large set.

Bloom filters may produce **false positive**. Simple bloom filters do not support delete operations.

Ethereum uses the bloom filter data structure to quickly filter out irrelevant blocks.

## Transaction-driven state machine

The operation process of Ethereum can be regarded as a **transaction-driven state machine**, where the state of the state machine refers to the state of all accounts, that is, the content contained in the state tree, and transactions refer to the transactions contained in each published block. **By executing these transactions, the system will be driven to move from the current state to the next state.**
