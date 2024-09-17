# P9 The BitCoin Scripting Language


## Scripting Language Based Stack
The only memory that Bitcoin Script can access is a stack, unlike other languages that can freely allocate heap memory. This makes Bitcoin Script somewhat akin to a simplified version of assembly language operations.

## Bitcoin Transaction Structure

- **txid**: Transaction ID
- **hash**: Transaction hash
- **version**: Protocol version
- **size**: Transaction size
- **locktime**: Transaction effective time
- **blockhash**: Hash of the block containing the transaction
- **confirmations**: Number of confirmations
- **time**: Transaction creation time
- **blocktime**: Block creation time
- **vin**: Input array, referred to as `vin`. Each item includes:
  - **txid**: Previous transaction ID
  - **vout**: Previous transaction output index
  - **scriptSig**: Input script, requires only a signature, referred to as `input script`.
    - **asm**: Script content
    - **hex**: Hexadecimal representation
- **vout**: Output array, referred to as `vout`. Each item includes:
  - **value**: Amount transferred
  - **n**: Output index, indicating which output it is
  - **scriptPubKey**: Output script
    - **asm**: Script content
    - **hex**: Hexadecimal representation
    - **reqSigs**: Required number of signatures
    - **type**: Output type
    - **addresses**: Output addresses

```
+---------------------+            +---------------------+
|     Block m         |            |     Block n         |
|                     |            |                     |
|  TX: A → B          |            |  TX: B → C          |
|                     |            |                     |
|  vin:               |            |  vin:               |
|  txid:              |            |  txid:              |
|  vout:              |       -----|--vout:              |
|  input script       |       |    |  input script       |
|                     |       |    |                     |
|  vout: <------------|--------    |  vout:              |
|  value:             |            |  value:             |
|  n:                 |            |  n:                 |
|  output script      |            |  output script      |
+---------------------+            +---------------------+
         |                                |
         |                                |
         +-------------+------------------+
                       |
                +---------------+
                |  input script |
                +---------------+
                | output script |
                +---------------+
```
The execution order of scripts is as follows:
1. The input script of the current transaction is executed.
2. The output script of the previous transaction is executed.

This sequential execution ensures that the necessary conditions for validating the transaction are met.

## Input and Output Script Formats

The following script instructions do not include the `OP` prefix. For example, `PUSHDATA` should be represented as `OP_PUSHDATA`. 

### Common Instructions

- **PUSHDATA**: Push data onto the stack
- **DUP**: Duplicate the top item on the stack
- **HASH160**: Apply SHA-256 followed by RIPEMD-160
- **EQUALVERIFY**: Check if the top two items are equal and remove them from the stack
- **CHECKSIG**: Verify the signature

These instructions are essential for creating and validating scripts in Bitcoin transactions.
### P2PK(Pay to Public Key)

### Input Script
```asm
PUSHDATA(Sig) // Sig is the signature
```

### Output Script
```asm
PUSHDATA(PubKey) // PubKey is the recipient's public key
CHECKSIG // Used to check the signature; returns true if correct
```

### Execution Steps
This process ensures that only valid transactions are accepted in the Bitcoin network.
```
instruction         |->  PUSHDATA(Sig)   |   PUSHDATA(Sig)       |   PUSHDATA(Sig)
pointer             |    PUSHDATA(PubKey)|-> PUSHDATA(PubKey)    |   PUSHDATA(PubKey)
                    |    CHECKSIG        |   CHECKSIG            |-> CHECKSIG
--------------------|--------------------|-----------------------|--------------------
stack               |                    |                       |
                    |                    |   PubKey              |
                    |        Sig         |   Sig                 |   TRUE
--------------------|--------------------|-----------------------|--------------------
```


### P2PKH (Pay to Public Key Hash)

- The main difference from the previous method is that the output does not provide the recipient's public key directly; instead, it provides the hash of the public key.
- This helps prevent impersonation, ensuring that one cannot use their own public key to masquerade as the recipient's public key.

### Input Script
```asm
PUSHDATA(Sig)      // Push the signature onto the stack
PUSHDATA(PubKey)   // Push the public key onto the stack
```

### Output Script
```asm
DUP                // Duplicate the top element of the stack
HASH160            // Hash the top element and push the hash onto the stack
PUSHDATA(PubKeyHash) // Push the provided public key hash onto the stack
EQUALVERIFY        // Verify that the two hashes are equal; if not, fail
CHECKSIG          // Verify the signature with the public key
```

### P2SH (Pay to Script Hash)

P2SH utilizes the BIP16 proposal, allowing transactions to be sent to a script hash instead of a public key hash.

### Input Script
```asm
...
PUSHDATA(Sig)               // Push the signature onto the stack
...
PUSHDATA(serialized redeemScript) // Push the serialized redeem script onto the stack
```

### Output Script
```asm
HASH160                       // Hash the redeem script
PUSHDATA(redeemScriptHash)    // Push the hash of the redeem script onto the stack
EQUAL                         // Check if the hashes match
```

#### Further Explanation

- The **input script** must provide a variable number of signatures along with a serialized `redeemScript`. The verification process involves two main steps:
  1. **Hash Verification**: Check if the serialized `redeemScript` matches the hash value in the output script.
  2. **Signature Verification**: Deserialize the `redeemScript` and execute it to verify that the signatures provided in the input script are correct.
- Forms of `redeemScript`
  1. **P2PK (Pay to Public Key)**
  2. **P2PKH (Pay to Public Key Hash)**
  3. **Multisig (Multisignature)**

#### Implementing P2PK Using P2SH

- redeemScript
```asm
PUSHDATA(PubKey)
CHECKSIG
```
- input script
```asm
PUSHDATA(Sig)
PUSHDATA(serialized redeemScript)
```
- output script
```asm
HASH160
PUSHDATA(redeemScriptHash)
EQUAL
```

#### Multisignature
In scenarios like a company transaction requiring multiple partners' signatures, a multisignature setup is used. Below is an example of an early multisignature script, which is now considered outdated and not recommended due to its complexity being exposed to the user.
- input script
```asm
hack element
PUSHDATA(Sig_1)
PUSHDATA(Sig_2)
...
PUSHDATA(Sig_M)
```
- output script
```asm
M
PUSHDATA(pubkey_1)
PUSHDATA(pubkey_2)
...
PUSHDATA(pubkey_N)
N
CHECKMULTISIG
```

#### Implementing Multisignature Using P2SH

- redeemScript
```asm
M
PUSHDATA(pubkey_1)
PUSHDATA(pubkey_2)
...
PUSHDATA(pubkey_N)
N
CHECKMULT
```
- input script
```asm
hack element
PUSHDATA(Sig_1)
PUSHDATA(Sig_2)
...
PUSHDATA(Sig_M)
PUSHDATA(serialized RedeemScript)
```
- output script
```asm
HASH160
PUSHDATA(RedeemScriptHash)
EQUAL
```


### Proof of Burn

- output script, Provably Unspendable/Prunable Outputs
```asm
RETURN
... zero or more ops or text
```
When all inputs to a transaction point to a specific output, the execution reaching a `RETURN` instruction will result in returning `false`. This halts any further execution, and the corresponding output can no longer be spent. As a result, the associated UTXO (Unspent Transaction Output) can be pruned and does not need to be stored.

#### Applications

1. **Altcoins**:
   - In alternative cryptocurrencies (AltCoins), users may incur a cost to destroy tokens, effectively using the `RETURN` instruction to indicate that these tokens are no longer valid or usable.

2. **Information Storage on Chain**:
   - Information can be permanently stored on the blockchain by writing it after the `RETURN` instruction. This technique can be used for:
     - **Digital Commitment**: Ensuring that certain data (like contracts or agreements) is verifiably recorded on the blockchain. For example, registering and timestamping intellectual property claims to provide proof of ownership.