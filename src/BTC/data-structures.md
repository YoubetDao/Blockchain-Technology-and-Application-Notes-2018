# P3 Block Chain Data Structures

## Hash Pointers
- A normal pointer in programming saves the target memory location.
- Hash pointer is a pointer saving the target memory location and the target hash，Typically, `H` is used to represent a hash pointer. It can locate the position of the structure and also detect whether the content has been modified, as it stores its hash value.

## Block Chain
```
+---------------------+         +---------------------+         +---------------------+
|   genesis block     |         |        Block 2      |         |the most recent block| <---- H()
|---------------------|         |---------------------|         |---------------------|
|                     | <-------|--H(previous).       | <-------|--H(previous).       |
+---------------------+         +---------------------+         +---------------------+
```

- Block Chain is a linked list using hash pointers.
- The first block of the chain is called *genesis block*
- The last block of the chain is the most recent block.
- Each Block has a hash pointing to the previous block, the hash of the most recent block is saved in the block chain system.
- The hash of a block is the result of applying a hash function to the entire content of the block.
- The hash of a block is the content of the current block and the hash of the previous block. This creates a tamper-evident log. In other words, it is impossible to modify a node within the blockchain. If a node is modified, then all subsequent blocks must also be changed.

## Merkle tree
```
          +------------------+
          | H(12)      H(34) | <------H(root)
          +--------+---------+
                   |
          +--------+---------+
          |                  |
  +-------+-------+  +-------+-------+
  | H(1)      H(2)|  | H(3)      H(4)|
  +-------+-------+  +-------+-------+
          |                  |
     +----+----+      +------+------+
     |         |      |             |
+----+---+ +--+---+ +----+---+ +---+---+
| Data 1 | |Data 2| | Data 3 | | Data 4|
+--------+ +------+ +--------+ +-------+

Leaf Nodes: Represent the hash values of the original data (e.g., `Data 1`, `Data 2`, etc.). 
Intermediate Nodes: Are the combinations of the hash values of their child nodes.
Root Node: Is the hash value of the entire Merkle Tree, representing the hashes of all leaf nodes.
```
- Merkle tree is a binary tree using hash pointers
- The advantage of this data structure is that you only need to know the root hash value to detect any modifications to the structure of the tree.
- The leaf nodes represent the transaction data.
- A single block consists of a block header and a block body.
- The root hash of the Merkle tree, composed of all the transactions contained in each block, is stored in the block header of that block.

## Merkle Proof

- In Bitcoin, nodes are categorized into full nodes and light nodes (or lightweight nodes).
- Full nodes contain the entire block information, including the block header, block body, and transaction data.
- Bitcoin wallets function as light nodes, storing only the block headers.
- The Merkle tree root of all transactions in a block is stored in the block header.
- The purpose of a Merkle proof is to verify that a transaction indeed exists in a Merkle tree.

### Example
```
----------+         +---------------------+         +---------------------+
 block n  |         |        Block n+1    |         |the most recent block| <---- H()
----------|         |---------------------|         |---------------------|
          | <-------|--H(previous).       | <-------|--H(previous).       |
          |.        |      Merkle Root    |         |       Merkle Root   |
----------+         +---------------------+         +---------------------+
                                   |
                                   | 
                                   |
          +------------------+     |
          | H(12)      H(34) | <---
          +--------+---------+
                   |
          +--------+---------+
          |                  |
  +-------+-------+  +-------+-------+
  | H(1)      H(2)|  | H(3)      H(4)|
  +-------+-------+  +-------+-------+
          |                  |
     +----+----+      +------+------+
     |         |      |             |
+----+---+ +--+---+ +----+---+ +---+---+
|   tx1  | | tx2  | |   tx3  | |  tx4  |
+--------+ +------+ +--------+ +-------+
```

How can we prove that Transaction 2 (tx2) exists? We only have the Merkle Root (R) and the tx2 data.

1. Compute the hash \( H'(2) \) for tx2.
2. Query the full node for the hash value \( H(1) \).
3. Compute \( H'(12) \) from \( H'(2) \) and \( H(1) \).
4. Query the full node for the hash value \( H(34) \).
5. Compute the Merkle Root value \( R' \) from \( H'(12) \) and \( H(34) \).
6. Check the equation \( R' \) and \( R \). If \( R' \) equals \( R \), then tx2 exists; otherwise, it does not.


### The Time Complexity of Merkle Tree
- Proof of membership(inclusion) O(log(n))
  - In the context of Merkle trees, the time complexity for verifying a Merkle proof is **O(log n)**, where **n** is the number of leaf nodes (transactions) in the tree. This is because you only need to traverse up the tree using the hash values provided in the proof, which involves a logarithmic number of steps relative to the total number of transactions.
- Proof of non-membership O(n)
  - We need to query all the transactions from the full nodes and check it's non-existence.
  - Sorted Merkle tree which sorted by transactio hash is similar to the binary search tree. We can use a Merkle tree to prove the non-existence of a transaction for O(log(n)) time complexity, although this structure is not used in Bitcoin.

