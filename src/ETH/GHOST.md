## P18 GHOST Protocol
### 1 Background 
#### 1.1 Impact of shortening Ethereum's block generation time 
- Advantage
    * Increase system throughput and reduce response time.
- Disadvantage
    * The fast block generation speed, combined with the long transmission time of the underlying overlay network, makes it easy for Ethereum to generate many **temporary forks**.

#### 1.2 BTC's block reward mechanism is no longer applicable to ETH
- BTC's block reward mechanism
```plain
BTC
           □  （orphan block、 still block）   no reward
         ↙  
  ← □ ← □ ← □ ← □  (longest valid chain)      block reward 以及 tx fee  
         ↖          
           □  （orphan block、 still block）   no reward                      
```
Only the block that becomes the longest legal chain can obtain the block reward and transaction fee.
- Reasons why BTC's block reward mechanism is not applicable to ETH
	* **thereum forks are too easy to occur**. Processing in the same way as BTC would result in miners' blocks being mined with a high probability being in vain, which is unfair to miners who mine blocks, especially individual miners.
    * **The trend of large mining pools leads to mining centralization**. and this centralization will bring disproportionate advantages and centralization bias to mining pools, creating a vicious cycle.
### 2 Evolution of Ethereum's GHOST protocol
#### 2.1 Core idea
Miners who mine and publish blocks can still obtain block rewards even if the blocks are invalidated.
#### 2.2 Initial version protocol
-  Content
	* Definition of uncle block
        * Ethereum calls orphan blocks and still blocks in BTC as uncle blocks. When the current block is published, it can contain transactions in uncle blocks.
	* Uncle block reward
        * If included by the current block, uncle blocks will receive 7/8 of the block reward.
	* Current block reward
        * In addition to obtaining the block reward, for each included uncle block, an additional 1/32 of the block reward will be obtained.
        * A block can include at most two uncle blocks.
- Defects
	* **Only two blocks can be considered as uncle blocks**. If there are other blocks, they cannot obtain rewards.
	* **Missed uncle block cannot obtain reward**. If a block that can be used as an uncle is found after a block is published, this block cannot obtain the uncle block reward.
	* **Mining pools deliberately do not include a certain block**. Because of the commercial interests, they are in a competitive relationship.

#### 2.3 Protocol improvement
```plain
ETH
          |←------at most 7 generation------→|
  ←  □ ←  □ ←  □ ←  □ ←  □ ←  □ ←  □ ←  □ ←  □  
      ↖    ↖    ↖    ↖    ↖    ↖    ↖    ↖ 
         □    □    □    □    □    □    □    □   
         0   2/8  3/8  4/8  5/8  6/8  7/8   0
```
- Improvement points
    * Expanded the definition of uncle blocks, they don't have to be contemporary uncles and can be uncles from several generations ago.
-  Purpose
    * Solve the defects existing in the initial version protocol, solve the temporary forks in the system, and encourage fork merging.
- Protocol points
	* Definition of uncle block
        * A block that has a common ancestor within 7 generations from the current block.
	* Uncle block reward
        * Only 6th-generation uncle blocks can obtain block rewards, which decrease from 7/8 to 2/8 as the generation increases.
	*  Current block reward
        * Regardless of which generation of uncle is included, the additional reward obtained for including one uncle block is always 1/32.
	* The design reason for having only 6 generations of legal uncles:
		* Reduce the state that full nodes need to maintain.
		* Setting 7 generations and gradually decreasing the block reward is beneficial to encourage early merging after a fork occurs. Merging as soon as a fork occurs will result in the highest block reward. The more generations, the less the block reward will be.
    * The main purpose of the protocol is to solve temporary forks rather than hard forks. If a fork is caused by other reasons and there are different suggestions for the running blockchain protocol, this method cannot solve it:
         * In Bitcoin's script checkmultisig, when checking the legality of multiple signatures, an extra element will be popped from the stack, and the normal operation check cannot pass. To deal with a bug, an extra useless element needs to be pushed into it first. Why not fix this bug? After modification, the versions will be different, and a hard fork will occur once it is modified.
         * If they mutually consider each other's chains to contain illegal transactions, this method cannot merge them.

* Comparison of block rewards between Bitcoin and Ethereum
    * BTC
        * It can obtain block reward (also known as static reward) and transaction fee (transaction fee can be obtained only by executing transactions, known as dynamic reward).
        * The block reward is halved regularly to artificially create scarcity.
        * The transaction fee accounts for about 1/100 of the block reward.
        * It is compared to digital gold and is used for value storage.
    * ETH
        * It can obtain block reward (also known as static reward) and gas fee (if a block contains a smart contract, gas fee can be obtained by executing the smart contract, known as dynamic reward).
        * Uncle blocks receive a 7/8 block reward reward, and uncle blocks do not receive gas fee.
        * The gas fee accounts for a very small proportion. Most of it is static block reward, similar to the situation in Bitcoin.
        * Ethereum does not stipulate regular halving of block reward. Ethereum's block reward adjustment is not to artificially create scarcity but to adjust mining difficulty to maintain fairness and prevent drastic changes in the total supply of Ether. This adjustment is only a one-time adjustment and will not be continuously lowered in the future.
        * It is compared to oil and is used for consuming and executing smart contracts. The metaphor is not completely appropriate because oil is gone after it is spent. In Ethereum, when executing a contract, only the gas is transferred from one account to another.
### 3 Q & A


**Q** If one more uncle block appears while mining the next block, how can it be included?

**A** By modifying the fields in the block header to include two uncle blocks. This modification has no loss for mining because the mining process is unmemorable. Modifying and continuing to mine is the same as setting the parameters from the beginning, and there is no disadvantage.

**Q** Why can only two blocks be included?

**A** If it is considered an uncle block, it can obtain 7/8 of the block reward, which is very high. If there is no limit and 100 uncles can be recognized, then Ether will be too worthless.


**Q** When an uncle block is included, should the transactions in the uncle block be executed?

**A** They should not be executed. Ethereum and Bitcoin are transaction-driven state machines. Every time a new block is published on the chain, the current state will be transferred to the next state. There may be conflicts between transactions on the main chain and uncle blocks. Ethereum prevents double-spending attacks through account balances. Two different transactions may conflict. If the transactions of uncle blocks are executed, some transactions may become illegal transactions. The uncle block itself is not necessarily illegal. After executing the parent block and then executing the uncle block, it may become illegal.

**Q** Is it possible to check which transactions in the uncle block are legal and only execute legal transactions?

**A** To ensure transactions along the main chain, the transactions of uncle blocks are not executed by the current block, and the legality of uncle block transactions is not checked. The current block will check whether the uncle block is legally published and whether it meets the mining difficulty by checking the block header.

**Q** How can we detect how many uncle blocks there are?

**A**  Check when one is monitored. Each published block must indicate the parent block, so it is possible to detect whether there is a common ancestor between the uncle block and the current block.

**Q** If there is a series of blocks following after a fork, should the entire chain be regarded as uncle blocks and rewarded to encourage merging?

**A** Only the first block has a reward, and the rest have no rewards. The cost of forking attack is too low. If the entire chain is rewarded and the forking attack is successful and the transaction is rolled back, otherwise, block rewards can still be obtained. Then the cost of forking attack will be greatly reduced.

```plain
ETH
          |←------at most 7 generation------→|
  ←  □ ←  □ ←  □ ←  □ ←  □ ←  □ ←  □ ←  □ ←  □  
            ↖     
               □ ←  □ ←  □ ←  □ ←  □ ←  □ ←  □
            reward  0    0    0    0    0    0
```
