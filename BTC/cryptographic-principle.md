# P2 Cryptographic Principle

# Hashing

## Cryptographic Hash Functions

1. **Collision Resistance** (also known as Collision-Free):

   - Given an input x , it is computationally infeasible to find another input y such that $$x \neq y, \quad H(x) = H(y)$$
     This ensures that each message has a unique hash digest, making it practically impossible to alter the content without being detected.
   - While no hash function can mathematically guarantee collision resistance, some functions, such as MD5, were once thought to be resistant but were later found to be vulnerable to collision attacks.

2. **Hiding**:

   - A cryptographic hash function is a one-way function, meaning that it is irreversible. Given x , you can easily compute H(x) , but given H(x) , it's infeasible to find x .
   - This property enables **digital commitment**, similar to the digital equivalent of a sealed envelope. In practice, this is often combined with a random nonce, producing H(x || \text{nonce}) , ensuring that the original value remains hidden.

3. **Puzzle-Friendliness**:
   - Bitcoin mining leverages this property by requiring the computation of a hash that begins with a certain number of leading zeros. This is typically achieved by finding a nonce such that:
   - $$H(\text{block header} + \text{nonce}) \leq \text{target}$$
   - This mechanism ensures that solving the puzzle is computationally difficult but verifying the solution is straightforward.

```
|       whole space       |
+-------------------------+
|//////|                  |
+-------------------------+
|target|
|space |
```

Bitcoin utilizes the SHA-256 hash function, which satisfies all three of the properties mentioned above.

## Digital Signatures

- Bitcoin Account Management

  - Bitcoin, being decentralized, allows account creation independently. An account is essentially created by generating a cryptographic key pair (public and private keys).

- **Asymmetric Encryption Algorithm**:
  - This form of encryption resolves the issue of key distribution in symmetric encryption by using a pair of keys: a public key for encryption and a private key for decryption.
  - When broadcasting a transaction in Bitcoin, the sender signs it with their private key, and other nodes verify it using the sender's public key.
- **Key Security**:
  - The probability of generating the same key pair as someone else is infinitesimally small.
  - Bitcoin requires a good source of randomness not only during key generation but also during every signing process to prevent potential private key leakage.
