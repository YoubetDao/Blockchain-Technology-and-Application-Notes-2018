# Block Chain Data Structures

## Hash Pointers
- 一个保存目标结构体位置和哈希的指针。a pointer saving the target memory location and the target hash
- 既能找到结构体位置，又能检测内容是否被修改

## Block Chain
- Block Chain is a linked list using hash pointers.

- The first block of the chain is called *genesis block*
- The last block of the chain is the most recent block.
- The hash of a block is the content of the block and the hash of the previous hash. There will result in a tamper-evident log

## Merkle tree
