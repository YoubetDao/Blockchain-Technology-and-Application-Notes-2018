# P21 Proof Of Stake (POS)

## Electrical Energy of Pow

1. [Bitcoin Electrical Energy Consumption:](https://digiconomist.net/bitcoin-energy-consumption): 172 terawatt-hours (TWh) per year, equivalent to Poland’s annual electricity consumption.
2. [Bitcoin Electrical Energy Consumption:](https://digiconomist.net/ethereum-energy-consumption): 93 TWh per year when using **proof-of-work**(POW) before switching to **proof-of-stake**(POS).

> 1Twh = 10^9 Kwh

## Description of Proof of stake

In POW, mining equipment is purchased with external currency, and the miner's computational power is essentially determined by how much money they spend. In contrast, **proof-of-stake** skips mining equipment and directly stakes tokens for voting to reach consensus, which is sometimes referred to as **virtual mining**.

Coin distribution with proof-of-stake involves:
1. Reserving some coins for developers.
2. Selling some coins to fund development.

Once the system reaches a stable state, individuals can stake and vote according to the amount of coins they hold.

Advantages of POS over POW:
1. Energy efficiency leading to reduced greenhouse gas emissions.
2. Small computational power in POW is susceptible to malicious attacks (AltCoin Infanticide), where POW attacks rely on external computational resources; however, POS attacks can only be sourced from within the cryptographic system.

### Hybrid of Pos and Pow

POW and POS can be used in combination. The more coins you hold, the easier it is to mine a block with POW. However, its disadvantage is that it is easiest for people who hold the most coins to mine.

To avoid this, **Proof of Deposit** needs to be used for optimization: the pledged currency has a certain lock-up period and cannot be reused. For example, when mining the current block, a certain amount of coins is invested to reduce the difficulty, but when mining the next block, the coins invested in the previous block cannot be used and need to wait for a certain period of time before they can be used again.

### Nothing At Stake
A major challenge in the design of proof-of-stake systems: Nothing At Stake.

In proof-of-work(PoW) system, miners must select only one chain for mining during a fork due to the computational demands of mining. However, in proof-of-stake system, validators can stake on multiple forked chains without incurring computational costs. Consequently, validators can earn rewards regardless of which chain ultimately becomes the longest and valid one, but it will be harmful to the system and not conducive to reaching consensus.

```txt

                     ┌──┐ ┌──┐
 ┌──┐◄──┬──┐◄──┬──┐◄─┴──┘◄┴──┘
 └──┘   └──┘   └──┘◄─┬──┐   ▲ 
                     └──┘   │ 
                      ▲     │ 
                      └─stake 
```

## Casper
The Proof of Stake protocol that Ethereum is planning to adopt is `Casper the Friendly Finality Gadget (FFG)`, which will be initially hybrid with POW to provide Finality to POW.


### Finality
`Finality` is the property that there are blocks in the chain that are guaranteed never to be reverted: they will be part of the chain forever.

In Ethereum’s system, Casper FFG achieves the `Finality`.

### Validator
Casper introduces `Validator`, users who must deposit a certain amount of Ether as bonds to become `Validator`; this bond is locked by the system. The amount of the bond determines the weight of their vote.

A Validator’s responsibility is to facilitate consensus within the system by voting for the longest valid chain.

### Epochs and checkpoints

1. Epochs: While still using POW, mining will occur, and every 100 blocks will form an `epoch`.
2. Checkpoint: To ensure `Validator` vote on the same block, a checkpoint is used as a reference point; it occurs at the first slot of every epoch.

### Two-phase commit

The voting process utilizes the two-phase commit concept:
1. Prepare message
2. Commit message

In the original Casper version, both rounds of voting were on the same `checkpoint`, requiring over two-thirds of Validators to agree in each round for consensus to be reached.

```txt
          │◄───  epoch ──────►│               
          │                   │               
          │                   │               
 ┌──┐◄┬──◄┼┬──◄── ◄─┬──◄──┬──◄┼┬──◄──┬──┐◄┬──┐
 └──┘ └──┘│└──┘ ... └──┘  └──┘│└──┘  └──┘ └──┘
          │                   │               
          │                   │prepare 2/3    
          │                   │commit  2/3    
```

In the actual system, the distinction between these two messages is not made, and the epoch has been reduced from one hundred blocks to fifty blocks.

Each epoch requires only one round of voting; this round serves as the `commit message` for the previous epoch and the `prepare message` for the next epoch.

After two consecutive epochs of voting, with both receiving more than 2/3 majority, the transactions of the first epoch are considered valid, achieving `Finality`.

```txt
 epoch │◄───epoch────►│◄────epoch────►│    
       │              │               │    
       │              │               │    
       │              │               │    
┌───┐◄─┼┬──◄── ◄─┬──◄─┼─┬──◄── ◄─┬──┐◄┼┬──┐
└───┘  │└──┘ ... └──┘ │ └──┘ ... └──┘ │└──┘
       │              │               │    
       │              │               │    
       │              │               │    
      2/3             2/3            2/3   
```

### Validator Rewards and Penalties
What are the benefits for `Validator` participating in this voting process?

Validators are rewarded for fulfilling their responsibilities.

However, if a Validator:

1. Fails to vote when required, a portion of the bond will be deducted.
2. Votes incorrectly for conflicting chains, all the bonded funds will be seized, and the seized funds will be destroyed.

Each Validator has a specific term limit; upon expiry, they enter a waiting period. During this period, other nodes can report and reveal any misconduct. If no misconduct is found during this waiting period, the Validator can retrieve their bond and rewards.


### Security
The Casper protocol can set a `checkpoint` for a certain state of a blockchain that is mined. Is this `checkpoint` absolutely secure? In other words, can the `Finality` achieved through validator votes be overturned?

If the malicious attacker is just a miner, they cannot overturn it; however, if there are a large number of `Validators` conducting a `Nothing At Stake attack`, it could lead to the system being tampered with.

Ethereum envisions that, over time, mining rewards will gradually decrease while proof of stake rewards will gradually increase, thus achieving the transition from POW to PoS, ultimately eliminating the need for mining of pow.

## Delegated Proof of Stake

Another consensus mechanism is `DPOS`: **Delegated Proof of Stake**.

For instance, in EOS, 21 super nodes are selected first by a voting mechanism, and then these super nodes generate blocks.

## Is Mining Energy Consumption Bad?

Is the high electricity consumption associated with mining necessarily a negative aspect? Some argue otherwise.

1. Electricity is challenging to store and transport; mining provides a means to convert electricity into currency.
2. Unused clean energy is difficult to return to the main grid: mining with clean energy effectively helps utilize surplus electricity.

Mining’s environmental impact is limited and can drive local economic development.
