# P16 State Tree

Ethereum is account-based, maintaining the state of each account in the system. This section explains how to map from an account address to its state.

Accounts are 40-digit hexadecimal numbers. Account states include balance, transaction count, and for contract accounts, code and storage.

### Hash Table (Not Feasible)

A full node maintains a hash table with constant time complexity for queries and updates.

#### Problems
- Hash collisions
- Can't provide Merkle proof to verify account state. Can't organize a hash table into a Merkle proof (changing even a small part of a block's hash content requires re-forming a Merkle tree for all account states, which is too costly).

### Direct Use of Merkle Tree (Not Feasible)

Forming all account states into a Merkle Tree requires only partial modifications.

#### Problems
- Lacks efficient query and update methods
- Different nodes can't provide the same Merkle Tree
  - When a block is published, other nodes need the root hash for consistency verification, requiring knowledge of all account states.
  - In Bitcoin, the node that publishes the block decides.

#### Sorted Merkle Tree Issues
- Adding an account may change the entire structure, requiring regeneration of the Sorted Merkle Tree.

### Trie
> "retrieval" means information retrieval

A tree formed by concatenating character prefixes.

#### Example
general, genesis, go, god, good.

```
(root)
 └── g
     ├── e
     │   ├── n
     │   │   ├── e
     │   │   │   ├── r
     │   │   │   │   └── a
     │   │   │   │       └── l (end)
     │   │   │   └── s
     │   │   │       └── i
     │   │   │           └── s (end)
     └── o
         ├── (end)
         ├── d (end)
         └── o
             └── d (end)

- (root) is the root node.
- g has e, representing words starting with ge.
- ge branches into n, forming general and genesis.
- go has words ending directly as go and branches d, od, forming god and good.
```

```rust
struct TrieNode {
    children: HashMap<char, TrieNode>,
    is_end_of_word: bool,
}
```

#### Features
- The maximum number of branches is the size of the legal character set.
- Search efficiency depends on string length; the longer the string, the more memory accesses.
- No hash collisions
- Insertion order doesn't affect the tree structure
- Locality of update operations; doesn't affect other branches
- Long chains with a single child node can waste storage

### Patricia Trie/Tree

A path-compressed Trie. Adding new strings may cause decompression. Very effective for sparse Tries.

#### Example
```
(root)
 └── gen
     ├── eral (end)
     └── esis (end)
 └── go
     ├── (end)
     ├── d (end)
     └── od (end)

- (root) is the root node.
- gen is a common prefix, leading to branches eral and esis for general and genesis.
- go is another common prefix, with ending go and branches d, od for god and good.
```

### MPT (Merkle Patricia Tree)

Like blockchain to a regular linked list, MPT uses "hash" pointers in a Patricia Tree.

#### Features
- Tamper-resistant
  - If the root hash of the MPT for account states doesn't change, the entire tree is unchanged.
- Merkle Proof
  - Prove account balance by sending the necessary Merkle Proof variables to a light node.
  - Prove an account is not in the full node by sending the necessary Merkle Proof variables for where it would be inserted.

### Modified MPT (Used in Ethereum)

- Extension Node
  - Contains path compression information
  - Fields
    - prefix: 1 byte, high bit indicates extension or leaf node, low bit indicates shared prefix nibbles' parity.
    - shared nibbles: compressed shared string sequence.
    - next node: address of the next branch node.
- Branch Node
  - Divides different subsequent nodes
- Leaf Node
  - References actual account state information
  - Fields
    - prefix: 1 byte, high bit indicates extension or leaf node, low bit indicates shared prefix nibbles' parity.
    - key-end: end of string sequence
    - value: account state, sub-tree Sub MPT.
      - If a contract account, includes contract code, storage root.
      - Balance

#### Advantages

- Modifications aren't in-place; unchanged parts use references, changed parts create new ones, resulting in a final MPT. Similar to COW, flux/Redux architecture.
- Allows time reversal. Efficiently saves history for easy rollback of account balances, contract codes, contract state storage, etc.

### Data Structure

#### Block
- header: block header
- uncles: array of uncle block headers
- transactions: list of transactions

#### Block Header
- ParentHash: parent block header hash
- UncleHash: uncle hash
- Coinbase: miner address
- Root: state tree root hash
- TxHash: transaction tree root hash
- ReceiptHash: receipt tree root hash
- Bloom: field for efficient queries related to the receipt tree
- Difficulty: mining difficulty
- GasLimit: gas-related
- GasUsed: gas-related
- Time: approximate block creation time
- MixDigest: mining-dependent parameter
- None: mining-dependent parameter

#### Published Block (External Block)

- Header: block header
- Txs: transaction list
- Uncles: uncle block headers.

### Serialization

Uses RLP (Recursive Length Prefix), a simplified version of protocol buffers
- Supports only nested arrays of bytes.
- More specific types are implemented at the application layer.