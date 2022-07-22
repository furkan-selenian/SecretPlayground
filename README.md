# SecretPlayground
In this repo, we will initialize a Secret environment in local from zero to hero :). This documentation is mainly based on the [Secret Network Documentation](https://docs.scrt.network).

## What is Docker?
Docker is an open source containerization platform. It enables developers to package applications into containers—standardized executable components combining application source code with the operating system (OS) libraries and dependencies required to run that code in any environment. Containers simplify delivery of distributed applications, and have become increasingly popular as organizations shift to cloud-native development and hybrid multicloud environments.

### What is a Container?
A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.

## Blockchain Technology

### Cosmos Basics

#### Horizontal Scaling - Application Specific Blockchains
Cosmos is an ecosystem of independent, interconnected and application specific blockchains commonly referred to as the internet of blockchains. By having all these, application specific interconnected blockchains, the ecosystem can easily scale horizontally which avoids most of the bottlenecks and scalability issues we know today on other heteregenous blockchains.

#### Tendermint and Cosmos SDK
To reach adoption and grow the internet of blockchain, developers should focus on their application specific development. They should not spend time on building from scratch the consensus and networking layers. That's what Tendermint Core is taking care of by providing networking and consensus layers saving a lot of time and headaches for the developers.

### Tendermint
In distributed systems, replication of information on all machines is fundamental. In our case, Tendermint is responsible for replicating securely and consistently the state-machine among the nodes of the system.

It is composed of a Byzantine-Fault-Tolerance (BFT) Proof-of-Stake (PoS) consensus algorithm and a peer-to-peer networking protocol. It communicates to the application layer through the ABCI protocol.

#### Byzantine-Fault-Tolerance (BFT)
In short, BFT represents the ability of a system to continue operating even if some of its nodes fail or act maliciously. In the Tendermint case, it can only tolerate up to a 1/3 of failures, meaning that the blockchain will halt momentarily until a 2/3 of the validator set comes to consensus.

Unlike Nakamoto consensus where it’s subject to 51% attack (meaning that 51% of the actors acting maliciously could attack and alter the blockchain), Tendermint is more resistant as it is subject to a 66% attack.

#### Consensus
Tendermint consensus module relies on Proof-of-stake and BFT.

Let’s take a look at the consensus process from a high-level standpoint:

1. Transactions are received by the node and goes into a local cache mempool
2. Before going into the node mempool, Tendermint ask the application if the transaction is valid through “CheckTx” ABCI message
3. If it’s valid, transaction is added to the mempool and broadcasted to the peer nodes
4. A new consensus round is initiated with a Proposer Node
5. The Proposer selects transactions in the mempool to be included in a new block
6. This proposed block is broadcasted to all nodes (Pre-vote phase) and nodes verify that the block is valid, simultaneously verifying that the Proposer is also valid and sign the pre-vote message
7. If >2/3 of nodes pre-vote for this block, block is valid (but not committed) and we enter the pre-commit phase
8. Same as pre-vote, Tendermint will wait for >2/3 of nodes to sign the pre-commit message 
    - There are two stages of voting to tolerate Byzantine faults, meaning that the pre-commit ensures that enough validators witnessed the result of pre-vote stage
9. The block is then committed, broadcasted to all nodes and transactions in this block executed by the application to update its state (for example account balance update etc)
    - Once a block is committed, there is no way to revert it and that gives an instant finality 
    - All nodes are processing the transactions of every block even if they are not the block proposer
10. A new round is proposed and a new proposer is designated

#### ABCI
