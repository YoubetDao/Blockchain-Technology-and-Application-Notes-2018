**Light Nodes vs. Full Nodes**: In the Bitcoin network, most nodes are lightweight nodes. If you only want to make transactions and are not interested in mining, there is no need to run a full node. During mining, if a miner detects that someone else has already mined a block and extended the longest chain, they should immediately abandon their current work and restart the mining process.

**Mining Has No Memory**: Mining itself does not have memory. Previous work does not affect future mining efforts, so even if earlier work becomes invalid, it is not wasted.

**Bitcoin System Security**:

- **Cryptographic Assurance**: Without someone else's private key, no one can forge a legitimate signature to transfer Bitcoin from another account.
- **Consensus Mechanism**: Ensures that malicious transactions are not accepted by the system.

**Evolution of Mining Equipment**:

- Mining equipment has evolved from general-purpose CPUs to GPUs, and now to specialized ASIC chips. As mining difficulty increased, using general-purpose devices became inefficient, leading to the emergence of specialized mining hardware.
- ASIC chips are designed specifically for mining certain cryptocurrencies. While they increase mining efficiency, they also raise the entry barrier, which goes against Bitcoin's original decentralized vision.

**Emergence of Large Mining Pools**:

- Individual miners often face unstable incomes, leading to the development of mining pools. Mining pools can be centralized (data center-like) or distributed (miners from different organizations). The pool allocates tasks, and miners are rewarded based on proof of work.
- Mining pools also face challenges, such as intentionally disrupting the pool by discarding mined blocks.

**51% Attack and Transaction Blocking**:

- A mining pool that controls over 51% of the network's  can perform a fork attack, rolling back previously confirmed transactions, which poses a major threat to the system's security. Additionally, an attacker can block transactions from a specific account by preventing its transactions from being included in the longest valid chain.
- However, stealing Bitcoin from others is impossible without having the private key, as the network will not recognize unsigned transactions.

**Pros and Cons of Mining Pools**:

- **Pros**: Mining pools provide more stable income for miners and reduce their workload.
- **Cons**: They threaten the security of the blockchain system, making 51% attacks more feasible.