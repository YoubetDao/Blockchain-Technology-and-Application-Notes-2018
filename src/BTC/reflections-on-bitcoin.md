# P13 Reflections on Bitcoin

## Hash Pointer

- In Bitcoin, a hash pointer is not a pointer to a memory address like in C. It doesn't point to a storage location directly.
- A hash pointer is the hash value of a block of data, which is the unique identifier for that data. **Bitcoin full nodes maintain the pointing relationship using a key-value database like LevelDB**, where the key is the hash value and the value is the block data.
- The header of each block records the hash value of the previous block, linking multiple blocks together in a chain.

## Blockchain romance

- A couple buys BTC and splits the private key into two parts, with each person keeping one part. However, this method of storing the private key is not a good practice.
- If one person loses their private key, not only will the BTC be unrecoverable, but **the Bitcoin system will also have to maintain this transaction in the UTXO set forever**.
- **Splitting a private key into multiple parts actually weakens its security**, as each part is much shorter than the full key, reducing its complexity. This significantly lowers the difficulty of cracking each part, thereby increasing the overall risk of compromise.
- The best method for managing an account by multiple people is using **multisignature**.

## Distributed Consensus

- distributed consensus impossibility
  - The FLP impossibility
    - In a fully asynchronous network, there is no reliable algorithm that can guarantee the system will always reach consensus in all cases.
- Bitcoin does not achieve true theoretical consensus and doesn't solve the distributed impossibility theorem.
- Bitcoin is a practical implementation that closely aligns with real-world models.
- Bitcoin achieves **practical consensus** by leveraging economic incentives.
- Don't let theoretical limitations constrain your thinking. **So buy BTC**.

## BTC Scarcity

- The total supply of Bitcoin is fixed, making it scarce and thus unsuitable as a currency.
- If you want to issue tokens, you will face cold-start challenges.
- Many altcoins have the ability to adjust their supply.

## Quantum Computing

- Quantum computing will primarily threaten traditional financial systems, and today’s cryptographic systems will also face significant challenges.
- Cryptographic algorithms are also evolving, with quantum encryption algorithms being an example.
- Challenges for quantum computers in breaking encryption:
  - Bitcoin does not expose the account’s public key directly. Instead, the public key is hashed using the SHA256 algorithm, and this hash becomes the Bitcoin address. Therefore, even with a quantum computer, it is very difficult to derive the public key from the address, and subsequently the private key.
  - Hash algorithms cause data loss, making it impossible to reconstruct the original data.
  - Encryption algorithms do not cause data loss, as the encrypted data can be decrypted back to its original form.
- Bitcoin can resist double-spending attacks. The quantum computing attacker would still need to participate in mining competition and publish the block at the correct moment.
