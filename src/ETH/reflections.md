# P24 Reflections

## 1. Is a smart contract really smart?

A smart contract is anything but smart. It has no AI-related capabilities, and once deployed, it is irrevocable.

## 2. Irrevocability is a double-edged sword.
1. Irrevocability enhances the contract's credibility.
2. Fixing vulnerabilities is difficult, as they can't be patched quickly or accounts frozen; issues are only resolvable through soft or hard forks. Once deployed, a smart contract cannot be stopped from being called.
Vulnerabilities can be exploited to transfer funds, and if a private key is leaked, funds must be immediately moved to a secure wallet.

## 3. Nothing is irrevocable.

For example, fork attacks can roll back transactions already written to blocks, and Ethereum software upgrades can forcibly change account states. Don't blindly trust irrevocability. Code is static; people are dynamic.

## 4. Is Solidity the right programming language?

- Solidity has many areas for improvement in its design, but whether to use functional programming or formal proof methods to ensure program correctness is still under discussion.
- Some suggest using a language with moderate expressive power, weaker Turing completeness than Solidity but stronger than Bitcoin scripts, to write smart contracts. This could minimize vulnerabilities. The challenge is finding such a language, predicting future needs, and anticipating attack methods.
- A possible future direction is to model contracts after real-world contracts, with professionals writing contract templates.

## 5. What does decentralization mean?

- Some argue against hard forks as centralized, but hard forks are decided by a vote of miners. Decentralization doesn't mean complete automation without human intervention or that rules can't be changed; it means changes must be made in a decentralized manner.
- Forks are generally seen as negative, yet they exemplify decentralization, allowing dissatisfied parties to fork off.

## 6. Decentralization is not equivalent to distribution

- Decentralized systems are necessarily distributed, but distributed systems aren't necessarily decentralized. Distributed platforms can run both centralized and decentralized applications.
- Most centralized distributed systems aim to have different machines perform different tasks to increase speed, then aggregate the results. Bitcoin and Ethereum, as transaction-driven state machines, have different machines performing the same tasks to reach consensus. State machines are primarily used in mission-critical applications to avoid downtime, requiring synchronized states across machines, which slows as the number of machines increases. Bitcoin and Ethereum networks are atypical; smart contracts are suited for writing control logic.