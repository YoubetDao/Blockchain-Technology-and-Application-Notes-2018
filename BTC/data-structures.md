# Block Chain Data Structures

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
- - Each Block has a hash pointing to the previous block, the hash of the most recent block is saved in the block chain system.
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

- **Leaf Nodes**: Represent the hash values of the original data (e.g., `Data 1`, `Data 2`, etc.). 
- **Intermediate Nodes**: Are the combinations of the hash values of their child nodes.
- **Root Node**: Is the hash value of the entire Merkle Tree, representing the hashes of all leaf nodes.
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
- The purpose of a Merkle proof is to verify that a transaction indeed exists in a Merkle tree.
The purpose of a Merkle proof is to verify that a transaction indeed exists in a Merkle tree.
### Example



