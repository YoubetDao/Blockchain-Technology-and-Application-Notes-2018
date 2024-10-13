# P25 Beauty Chain Incident

## Background

**Beauty Chain** is a smart contract deployed on Ethereum and has its own token **BEC**.

  - Without its own blockchain, the issuance and transfer of tokens are all completed by calling functions in smart contracts.
  - Beauty Chain can define its own issuance rules, and the number of tokens each account has is also stored in the state variables of the smart contract.
  - **ERC 20** is a standard for issuing tokens on Ethereum, which specifies the functions and interfaces that all contracts for issuing tokens should implement.
  - There is a function called `batchTransfer` in Beauty Chain, which sends tokens to multiple recipients and then deducts these tokens from the caller's account.

Below are the [implementation details](https://etherscan.io/address/0xc5d105e63711398af9bbff092d4b6769c82f793d#code) of the `batchTransfer` function:

```solidity
function batchTransfer(address[] _receivers, uint256 _value) public whenNotPaused returns (bool) {
    uint cnt = _receivers.length;
    uint256 amount = uint256(cnt) * _value;
    require(cnt > 0 && cnt <= 20);
    require(_value > 0 && balances[msg.sender] >= amount);

    balances[msg.sender] = balances[msg.sender].sub(amount);
    for (uint i = 0; i < cnt; i++) {
        balances[_receivers[i]] = balances[_receivers[i]].add(_value);
        Transfer(msg.sender, _receivers[i], _value);
    }
    return true;
  }
```

Please note that this line of code `uint256 amount = uint256(cnt) * _value;` may overflow, that is, if the `_value` sent to each person is large, the calculated `amount` may be small. This will cause the sender to spend only a small amount of money to increase the number of tokens for each receiver.

## Attack

The following are the [function parameters](https://etherscan.io/tx/0xad89ff16fd1ebe3a0a7cf4ed282302c06626c1af33221ebe0d3a470aba4a660f) during the attack:

```
Function: batchTransfer(address[] _receivers, uint256 _value)

MethodID: 0x83f12fec
[0]:  0000000000000000000000000000000000000000000000000000000000000040
[1]:  8000000000000000000000000000000000000000000000000000000000000000
[2]:  0000000000000000000000000000000000000000000000000000000000000002
[3]:  000000000000000000000000b4d30cac5124b46c2df0cf3e3e1be05f42119033
[4]:  0000000000000000000000000e823ffe018727585eaf5bc769fa80472f76c3d7
```

- Parameter 0 is the position of the `_receivers` array in the parameter list, starting from the 64th byte, which is parameter 2.
- Parameter 1 is the amount to transfer to each receivers.
- Parameter 2 first indicates that the array length is 2, and then parameters 3 and 4 indicate the addresses of the two receivers.
- The amount calculated with such parameters overflows to 0!

The attack occurred on April 22, 2018, and the currency value plummeted after the attack. The losses were then reduced by rolling back the transaction.

## Reflection

In earlier versions of Solidity, overflow can be easily detected by simply computing `amount` via multiplication provided by SafeMath.

Since Solidity 0.8, the overflow/underflow check is implemented on the language level - it adds the validation to the bytecode during compilation.
