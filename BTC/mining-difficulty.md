# P7 Mining Difficulty

## Mining and mining difficulty

**Mining**: Mining is to continuously try the `nonce` value in the block header so that the hash value of the entire block header is less than or equal to the given target threshold. That is, $H(block \; header) \leq target$. The smaller the `target`, the greater the mining difficulty.

**Mining Difficulty**: The mining difficulty is inversely proportional to the `target`, that is, $difficulty=\frac{difficulty1target}{target}$, where `difficulty_1_target` refers to the `target` corresponding to the mining difficulty of 1. The minimum mining difficulty is 1.

## Adjust mining difficulty

The mining difficulty is adjusted to **keep the average block time of BTC stable**. That is, a new block is generated every **10** minutes.

If the block time cannot remain stable, then as the computing power increases, the block time may become shorter and shorter, which will cause a large number of forks. The more forks there are, the less beneficial it is for the system to reach consensus. This is because a large number of forks disperse the system's computing power.

## Specific implementation of BTC

The mining difficulty is adjusted every **2016** blocks. The specific adjustment method is: $target = target \times \frac{actual \; time}{expected \; time}$.

The *expected time* represents the time required to generate 2016 blocks under ideal circumstances, that is, $2016 \times 10$ minutes (about two weeks). The *actual time* is the time spent mining the last 2016 blocks.

That is to say, if the time required to generate 2016 blocks is more than two weeks, it means that the mining difficulty is too high. According to the above formula, the `target` will become larger and the mining difficulty will be reduced. Vice versa.

The above formula is defined in the code of the Bitcoin node, prompting each Bitcoin node to comply with this rule.

If a Bitcoin node maliciously modifies the code to reduce the difficulty of mining, the blocks it issues will not be recognized by the vast majority of other honest nodes.

## Resource

- From the statistical chart in this [URL](https://bitinfocharts.com/comparison/bitcoin-confirmationtime.html#3y), we can see that the average block time of Bitcoin has remained at around 10 minutes.
