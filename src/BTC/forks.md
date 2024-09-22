# P10 Forks

**Fork**: Originally there was one chain, but now it is divided into two chains. This is called a fork.

The possible reasons for the fork are:

- **State Fork**: A fork is caused by a disagreement about the current state of the Bitcoin blockchain. For example, if two nodes mine at about the same time, both nodes can publish blocks, and a temporary fork will occur.
  - **Forking Attack**/**Deliberate Fork**: A forking attack is also a state fork, except that the disagreement in the fork attack is intentionally caused by humans.
- **Protocol Fork**: A fork is caused by a change in the Bitcoin protocol. To modify the Bitcoin protocol, a software upgrade is required. In a decentralized system, there is no way to ensure that all nodes upgrade the software at the same time. We assume that most nodes have upgraded their software, but a few nodes have not upgraded their software for various reasons, and a fork will occur at this time. According to the different contents of the protocol change, it can be divided into:
  - **Hard Fork**: Hard fork is usually extensions of consensus rules. Hard fork is usually permanent and may create two parallel chains. For example, [BCH](https://bch.info) was created as a result of a hard fork.
  - **Soft Fork**: Soft fork is usually tighten of consensus rules. Soft fork is usually temporary.
