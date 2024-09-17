# P11 BTC - Q&A

## What happens when transferring a transaction if the recipient is not online?
The transfer is only a record and does not require the recipient to be online

## If a full node receives a transaction, is it possible that the recipient's receiving address is one that the full node has never heard of before?
Possible. There is no need to notify anyone else when a Bitcoin account is created, and the node will only know about its existence when it receives money for the first time.

## What should be done if the private key of an account is lost?
There is no way to retrieve the private key after it is lost.

## What happens if the account's private key is leaked?
Transfer the funds from that account to a secure account as soon as possible.

## What should I do if I wrote the wrong address when making a transfer?
It's not possible to cancel a transaction once it has been broadcast.

## Transactions that are verified by the full node will be written to the blockchain, but the execution of `OP_RETURN` will unconditionally returns an error, how does the blockchain accept the block?
OP_RETURN is written in the output script, and the output script of the current transaction will not be executed at the time of validation, and It will only be validated when the output is spent again.


## A miner noticed that another miner had broadcast a block, after verifying the`nonce` is satisfied, is it possible that using that `nonce` as their own discovery and subsequently broadcasting the transaction.
Impossible! The Coinbase Transaction is the first transaction of each block as a reward for the miner who find the `nonce`. It should be noted that `nonce` and `merkle root hash` are both in `block header`, and the verification of `nonce` is through `H(block header) <= target`, if other miners want to steal the answer after verification, they would need to alter the recipient address in the transaction, which would change the `merkle root hash` and invalidate the original `nonce`.

## Transaction fees can be seen as the tips given to miners when broadcasting a transaction. So how do you know which miner this transaction fee should be given to?
There is no need to know which miner will receive the transaction fee in advance. The transaction fee is calculated as `total inputs - total output`. The miner who successfully mines a block collects the transaction fee when packaging the transactions.


