# P20 ETH Difficulty Adjustment 

## Why adjust difficulty

The Ethereum network targets a consistent block time of around 15 seconds. Without difficulty adjustment, variations in the number of miners or improvements in mining hardware could either make block production too fast (lower block times) or too slow (higher block times). Difficulty adjustment ensures that block production remains steady, regardless of the fluctuating total mining power.

## How to adjust

$$
D(H)=
\begin{cases} D_0, & H_i =0\\
max(D_0, P(H)_{H_d} + x * \sigma_2) + \epsilon, & otherwise
\end{cases}
$$

$$
D_0 = 131072
$$

The current block's difficulty consisit of two parts: basic part and difficulty bomb

- $P(H)_{H_d}$ is the parent block's difficulty.
- $x * \sigma_{2}$ is used for adpatively adjusting the block difficulty to ensure a stable block production rate.
- $\epsilon$ is the difficulty bomb.

$$
x = \left \lfloor \frac{P(H)_{H_d}}{2048} \right \rfloor
$$

$$
\sigma = max(y - \left \lfloor \frac{H_s - P(H)_{H_s}}{9} \right \rfloor, -99)
$$

- $H_s$ is the timestamp of the current block, and $P(H)_{H_s}$ is the timestamp of the parent block.
- The value $y$ is related to the number of uncles in the parent block. If the parent block includes an uncle, $y$ is 2; otherwise, $y$ is 1. When the parent block includes an uncle, the difficulty increases by one unit because including uncles increases the amount of newly issued currency.
- The upper limit for difficulty reduction is set to 99, primarily to address potential hacker attacks or other unforeseen black swan events.

$$
\epsilon = \left \lfloor 2 ^ {\left \lfloor H^{'}_i / 100000  \right \rfloor - 2}  \right \rfloor, H^{'}_i = max(H_i - 3000000, 0)
$$

- $\epsilon$ doubles every 100000 blocks. In the later stages, this leads to very rapid growth, that's why it is called the difficulty "bomb".
- The reason for implementing the difficulty bomb is to reduce the risk of forks when transitioning to the PoS protocol: by that time, mining difficulty will be very high, thus incentivizing miners to willingly migrate to the PoS protocol.
- $H^{'}_i$ is referred to as the fake block number, obtained by subtracting three million from the actual block number $H_i$. This adjustment is made because the development difficulty of the PoS protocol was underestimated.
