# P5 Implementation of the Bitcoin System

## Ledger Design Model

- Blockchain: A decentralized ledger technology used in various systems.
- Bitcoin’s Ledger Model: Uses a **transaction-based** approach where account balances are not directly recorded, requiring the replay of transaction history to calculate balances. Each transaction must specify the source of the coins.
- Ethereum’s Ledger Model: Adopts an **account-based** ledger system that explicitly records each account’s balance, similar to traditional banking experiences.

## UTXO

```plain
B ----------> A (5 BTC)
C ----------> A (3 BTC)

A ----------> D (8 BTC)
```

- UTXO (Unspent Transaction Outputs): A data structure that prevents double spending in Bitcoin.
- Transaction Example:
  - B sends 5 BTC to A; C sends 3 BTC to A, giving A two UTXOs.
  - A sends 8 BTC to D, consuming these two UTXOs.
	- Miners check against previous blocks to ensure no double spending.
- UTXO Set:
	- Maintained in memory by full nodes for quick double spending detection.
	- Contains elements like:
	  - Transaction Identifier (Txid): Unique hash of the transaction.
	  - Output Index: Identifies the specific output within a transaction.
- Transaction Process:
	- Transactions consume old UTXOs and create new ones.
	- Example: After A’s transaction, a new UTXO A ----------> D (8 BTC) is created.
- UTXO Set Storage:
	- Despite gradual growth, the UTXO set fits within standard server memory.

## Total inputs `=` total outputs

- Transaction Inputs and Outputs:
  - A transaction can have multiple inputs and outputs.
	- Typically, the sum of inputs exceeds the sum of outputs; the difference is the transaction fee for miners.
	- If inputs equal outputs, there is no transaction fee.
- Incentives in the Bitcoin System:
	- Miners receive block rewards and transaction fees as incentives.
	- As block rewards decrease, transaction fees will become the primary incentive for miners.
	- Block Reward Halving:
	- The block reward is halved every 210,000 blocks.
	- This halving occurs approximately every 4 years, calculated as $$\frac{210000 \times 10  \text{ minutes}}{60  \text{ minutes} \times 24 \times 365} \approx 4 \text{ years}.$$

## Bitcoin block information (Example: Block Height 548,402)

| Name     | Description                            | Value                                                         |
|----------|----------------------------------------|---------------------------------------------------------------|
| Hash     | Unique identifier used to identify a particular block | 0000000000000000001b55037c21205c145b69c7b80ccac455c7322463040f14 |
| Capacity | Percentage of the block's maximum allowed size that has been utilized by transactions | 16.04% |
| Distance | Time since block was mined             | 5y 10m 3d 0h 15m 10s                                           |
| BTC      | Accumulative amount of crypto sent on all transactions of the block | 2,843.2813 |
| Value    | Value of all transactions when block was mined | $18,170,159 |
| Value Today | Present value of all transactions | $160,622,277 |
| Average Value | Average value of transaction | 5.8503730250 BTC |
| Median Value | Median transaction value | 0.01458962 BTC |
| Input Value | Sum of all transactional input amounts | 2,843.33 BTC |
| Output Value | Sum of all transactional output amounts | 2,855.83 BTC |
| Transactions | Number of transactions included in this block | 486 |
| Witness Tx’s | Number of SegWit transactions on the block | 1 |
| Inputs | Total amount of inputs | 727 |
| Outputs | Total amount of outputs | 1,367 |
| Fees | Amount of transaction fees rewarded to the miner for calculating the hash for this block | 0.04586859 BTC |
| Fees Kb | Transaction fee paid per 1000 bytes of data in a block, reflecting the cost of space a transaction occupies in the block | 0.0002727 BTC |
| Fees kWU | Transaction fee paid per 1000 weight units (WU), which measures the transaction's impact on block capacity, especially for SegWit transactions | 0.0000682 BTC |
| Depth | Total number of confirmations | 311,400 |
| Size | Total size of the block | 168,183 |
| Version | Block version related to protocol proposals underway | 0x20000000 |
| Merkle Root | The root node of a merkle tree, a descendant of all the hashed pairs in the tree | 124e29e9eb2230f965c158eba33b4feff38c719c957749eebed4b9cdfc7db14f |
| Difficulty | Mathematical value of how hard it is to find a valid hash for this block | 7,184,404,942,701.79 |
| Nonce | Random value that can be adjusted to satisfy the proof of work| 3,485,367,370 |
| Bits | A sub-unit of BTC, equal to 0.000001 BTC | 388,443,538 |
| Weight | A measurement to compare the size of different transactions to each other in proportion to the block size limit | 672,624 WU |
| Minted | Static reward for the miner who calculated the hash for this block | 12.50 BTC |
| Reward | Reward paid as fees | 12.54586859 BTC |
| Mined on |  the exact date and time when the block was successfully mined and added to the blockchain | Nov 02, 2018, 1:11:23 PM |
| Height | Number of blocks connected on the blockchain | 548,402 |
| Confirmations | number of blocks that have been added to the blockchain after the specific block was mined. It shows how many times the transactions in that block have been confirmed by the network. More confirmations indicate increased security for the transactions | 311,400 |
| Fee Range | Range of transaction fees paid by the transactions included in a specific block. It indicates the minimum and maximum fees paid by transactions within the block | 5-446 sat/vByte |
| Average Fee | Average amount paid in fees per transaction on the block | 0.00009438 |
| Median Fee | Median amount paid in fees per transaction on the block | 0.00002244 |
| Miner | Who confirmed the transactions in the block | AntPool |

## Block headers

| Bytes | Name                    | Data Type  | Description                                                                                                                                                                                                                          |
|-------|-------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 4     | version                  | int32_t    | The block version number indicates which set of block validation rules to follow. See the list of block versions below.                                                                                                               |
| 32    | previous block header hash | char[32]   | A SHA256(SHA256()) hash in internal byte order of the previous block’s header. This ensures no previous block can be changed without also changing this block’s header.                                                               |
| 32    | merkle root hash         | char[32]   | A SHA256(SHA256()) hash in internal byte order. The merkle root is derived from the hashes of all transactions included in this block, ensuring that none of those transactions can be modified without modifying the header. See the merkle trees section below. |
| 4     | time                     | uint32_t   | The block time is a Unix epoch time when the miner started hashing the header (according to the miner). Must be strictly greater than the median time of the previous 11 blocks. Full nodes will not accept blocks with headers more than two hours in the future according to their clock. |
| 4     | nBits                    | uint32_t   | An encoded version of the target threshold this block’s header hash must be less than or equal to. See the nBits format described below.                                                                                               |
| 4     | nonce                    | uint32_t   | An arbitrary number miners change to modify the header hash in order to produce a hash less than or equal to the target threshold. If all 32-bit values are tested, the time can be updated or the coinbase transaction can be changed and the merkle root updated.               |

## Bitcoin transaction

### Transaction example

Bitcoin Transaction JSON (from blockchain.com)

```JSON
{
  "txid": "882f4d5ff8618b5b8802a2193dd853405d6ddb27068a44c5444db75f068dfaf3",
  "size": 373,
  "version": 1,
  "locktime": 548401,
  "fee": 163840,
  "inputs": [
    {
      "coinbase": false,
      "txid": "c5a1e76f233e1ec38b315700951ff17ac764429cc926e8592697060a35f83de1",
      "output": 0,
      "sigscript": "483045022100e619f11be093febe4ab9f68e4bbb5bf882e8fb472404434c82d984a667e3f152022008093e9495629f39aa42b8d6e13364acd473c6acd6dc755f1326572315b8d75c0121034c4b6449bbcdf467412a82f5d75c1d92c5908e3352b46c22ce940635bee989aa",
      "sequence": 4294967294,
      "pkscript": "76a914d576835832f8884f9ea58191d527cd18a2575dc488ac",
      "value": 1000000,
      "address": "1LTguHwAm5D9jZJQnauSQfQaygoVhtJdZC",
      "witness": []
    },
    {
      "coinbase": false,
      "txid": "e2fd6414a2478b6401a105a55f04a0002a9a7eeea685d2ff3e2ec4f888061b5e",
      "output": 1,
      "sigscript": "47304402207849b7760c760bcbf48bb9caa4362daa7faf00ef42f828f9b2b012150185a5df022014032423be78bf9fc64748d7fbd5409f65795e8cf07752ac33e0f9a25c002384012103cdfa005cba5a55351a0897d56b92b272d34f5e47695c9d3a56cf768a2003b7c1",
      "sequence": 4294967294,
      "pkscript": "76a9144322ebd00bf4a76b5e855099e473528b663c419c88ac",
      "value": 328331,
      "address": "177z7DYLMYUPkANt6aJkwGP6Dv4DZNL77x",
      "witness": []
    }
  ],
  "outputs": [
    {
      "address": "1MWdsyQDGWQMFW8WcNeJ4wcsnjwntSmTsn",
      "pkscript": "76a914e0fd5d0c43d4521e8c51441c53707784bf6eb78188ac",
      "value": 10000,
      "spent": true,
      "spender": {
        "txid": "fb9572cbab4e232a3b1a0e216a1f61dd4c4ba6d729c323f9b2622c842de71eb9",
        "input": 1
      }
    },
    {
      "address": "1HbarFGTVZjFgeAEEfxu2ZLYgFnqjHBw6j",
      "pkscript": "76a914b60c6938a77c956938517cb0b54801f5179b299f88ac",
      "value": 1154491,
      "spent": true,
      "spender": {
        "txid": "bd4419ec74e241d3869a8c8e25b3b1dc92573da70df6125c15a75b764f09988a",
        "input": 9
      }
    }
  ],
  "block": {
    "height": 548402,
    "position": 2
  },
  "deleted": false,
  "time": 1541135483,
  "rbf": false,
  "weight": 1492
}
```

### Transaction scripts

In the Bitcoin system, verifying the legitimacy of a transaction involves concatenating the transaction's input scripts with the output scripts from the previous transactions that provided the coin source and executing them. If the execution is successful, the transaction is considered valid.

## Probability analysis of the mining process 

- Mining as a Bernoulli Process:
	- Each attempt to find a valid nonce during mining is a Bernoulli trial with a binary outcome (success or failure).
	- The probability of success in each trial is low, making mining a series of independent Bernoulli trials.
- Approximation to Poisson Process:
	- Due to the high number of trials and the low probability of success, mining can be approximated by a Poisson process.
- Block Times and Exponential Distribution:
	- Block times follow an exponential distribution, indicating the time intervals between blocks are memoryless.
	- Average block time is set at 10 minutes.
	- Miners’ success is proportional to their hash power relative to the total network hash rate (e.g., 1% hash power equates to mining approximately 1 out of every 100 blocks).
- Memoryless Property:
	- The exponential distribution is memoryless, meaning the probability of finding the next block does not change with time already spent mining.
	- This progress-free aspect ensures fairness in mining, as previous unsuccessful attempts do not influence the likelihood of succeeding in subsequent tries.

## Total supply of Bitcoin

- The block reward is the only way new Bitcoin is created in the system. 
- The block reward halves every 4 years, which forms a geometric series in terms of the number of new bitcoins generated.
- The series is calculated $$210000 \times 50 + 210000 \times 25 + 210000 \times 12.5 + \cdots = 210000 \times 50 \times \left( 1 + \frac{1}{2} + \frac{1}{4} + \cdots \right) = 210000 \times 50 \times \left( \frac{1}{1 - \frac{1}{2}} \right) = 21000000$$

## Bitcoin is secured by mining

- Security Assumption:
  - Majority hash power controlled by honest nodes increases security but doesn’t guarantee all transactions are legitimate.
- Probabilistic Guarantees by Mining:
	- High probability that honest nodes publish the next block, but there’s still a chance for malicious nodes to validate a block.
- Malicious Node Capabilities:
	- Cannot Steal Coins: Malicious nodes can’t forge signatures to steal coins.
	- Rejecting Forged Transactions: Honest nodes will reject blocks containing forged, illegitimate transactions.
	- Potential for Double Spending: Malicious nodes can attempt double spending, especially if they manage to validate a block with such a transaction.
- Preventing Double Spending:
  - Transactions are considered secure after 6 confirmations; each additional confirmation significantly reduces the chance of tampering.
  - Zero Confirmation: Transactions broadcast but not yet recorded are vulnerable; Bitcoin defaults to first-seen acceptance.
- Handling Excluded Transactions:
	- If a malicious node omits legitimate transactions, these can be included in subsequent blocks by honest nodes.
	- Bitcoin blocks have a size limit (1MB), meaning some transactions may naturally wait for the next block.
- Selfish mining: 
  - a mining strategy where the miner, upon discovering a block, does not immediately publish it. Instead, they keep it hidden and continue mining the next block, waiting for the right moment to release an entire chain.