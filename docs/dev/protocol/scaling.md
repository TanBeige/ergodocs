---
tags:
  - NIPoPoWs
  - Plasma
  - Sigma Protocols
---

# Scaling


There are three generations of blockchain, each capable of more complex behaviours than the last. 

- The first generation refers to the original use BTC was intended for, that is, to provide a reliable and accurate method of maintaining a public transaction ledger. 
- Second-generation blockchains were created with the implementation of smart contracts as a priority, with the most popular one being ETH (Ethereum). One of the big differences in ETH is the language used to smart code contracts. ETH utilizes a [turing complete](https://www.cs.odu.edu/~zeil/cs390/latest/Public/turing-complete/index.html) language (known as Solidity), meaning it is computationally universal. 
- The third generation of blockchain technology now focuses on solving issues with congestion and scalability. As this technology becomes more decentralized, there will inevitably be an exponential increase in the number of users interacting with the blockchain. DOT, ADA and ERG are third-generation blockchains, meaning they have smart-contract capabilities while proposing solutions to scalability for a global audience. 

Third-generation blockchains generally can process off-chain transactions, helping speed up transactions significantly. The account model performs both storage changes and validity checks on-chain during code execution. In contrast, Ergo transactions are created off-chain, and only validation checks are performed on-chain, reducing the number of operations performed by every node on the network. In addition, due to the immutability of the transaction graph, various optimization strategies can improve the throughput of transactions per second in the network. 

Light-verifying nodes are also possible, thus further facilitating the scalability and accessibility of the network.

Based on a decade of research, extensive testing pre-launch and ongoing development, the Ergo community has launched a blockchain with all the tools to scale to a global scale over time. 


## Transaction Speed

Transaction speed is commonly measured in Transactions per Block/Second (TPS). Transaction speed, specifically TPS, measures the transactions per block/second a blockchain can perform, quantifying how quickly a blockchain can complete transactions.  

Below are approximated TPS values for other blockchains:

* `BTC - ~7 TPS (Gobbel, 2017).`
* `ETH - ~15 TPS (Clincy et al (table1), 2019)`
* `XRP - ~ 1500 TPS (Clincy et al (table1), 2019)`
* `ADA - ~ ~7 TPS (~250 in controlled tests) (Stamoulis, 2021).`
* `DOT - ~1500 TPS (Hiemstra et al., 2021)`


It is important to remember that TPS (Transactions Per Second) is a vanity metric, and it is not about how many transactions you can do but rather the weight of those transactions and the computational cost limit per block. This cost limit depends on the hardware miners have, the size of the network, and other dynamic factors, but focusing on how many simple transactions you can do at once is meaningless, especially on Ergo. 


With the release of v5, the raw TPS numbers should bring us to around **47.5tx/s** - improvements on top of this are still possible. The focus is on raising the TPS without compromising classic blockchain assumptions and guarantees. 

## Settlement Layer

Thanks to the high flexibility of the ErgoScript programming model, large chunks of transactions can happen on layer two and be settled in Ergo using a single transaction. 

Below you will find a developer harnessing the power of eUTXO to airdrop native tokens to [10,000 addresses at once](https://explorer.ergoplatform.com/en/transactions/e2c4954665ccf87791f42983ae4f7031205c2e719709907cbf2ff09e5489d4b8)

ErgoScript adds several improvements such as time-weighted data, Turing completeness, read-only data inputs, multi-stage contracts, sigma protocols, NIPoPoWs and more that make many different protocols possible on Layer 2, each one solving scalability problems in a specific domain (like simple payment transactions, sped up with sub-block confirmation protocols).
**Ergo can be considered a common *settlement layer* for many Level-2 protocols and applications.**


## Layer 0 *(Network Layer)*

The network or *peer to peer* layer. The Ergo Node Client has greatly improved since v4.0.8 and still has room to grow. Quick bootstrapping using [NIPoPoWs](nipopows.md) proofs and UTXO set snapshots in development

**Stateless Clients:** Light clients: You can have full-node guarantees in Ergo without storing the full *UTXO set*. Bringing improved bootstrapping and block validation times.  

**State Bloat:** One of Ergo's major strengths when scaling is to avoid bloat without compromising functionality. For E.g. persistent updateable storage is possible, with updates to be checked by a blockchain contract. However, only the digest of the authenticated data structure (and some additional bytes, less than 40) is stored in the UTXO set regardless of the data set size. Ergo utilizes a [Storage Rent Fee](rent.md) to prevent spam and recirculate unused data bytes, known as dust. Storage Rent Fee helps clean the network pollution and encourages users to be more active.

**Block size:** Parameters like block size, etc., are not set in stone; miners can adjust them. So if a miner is experiencing low full block validation time (as hardware is getting better with time and software), he may propose or vote to increase the block size. Currently set to `8MB`.

**Transaction size:** As of node `4.0.23`, there is a transaction size limit of `96kb` for the mempool. Larger transactions can only be included manually by miners. 

**Logarithmic space mining:**  allows for *light miners.* Similar to light clients, light miners can bootstrap with block headers without downloading the entire blockchain. Integrating logarithmic space mining in Ergo is possible via a velvet (soft) fork; see this video from Dionysis Zindros from The University of Athens for a [introduction and their progress so far](https://www.youtube.com/watch?v=s05ypkSC7gk).

## Layer 1 (Blockchain)

Ergo supports multiple on-chain scalability solutions, such as Sharding.

**Sharding** as per [*' On the Security and Performance of Blockchain Sharding'*](https://eprint.iacr.org/2021/1276)

**Sub-block confirmation protocols:** as seen in ([Bitcoin-NG](https://www.usenix.org/system/files/conference/nsdi16/nsdi16-paper-eyal.pdf) or [Flux](https://www.usenix.org/system/files/atc20-li-chenxing.pdf) are an active topic for research in 2023. Ergo blocks have *extension sections* with **mandatory and arbitrary key-value data**; by putting certain anchors there, it is possible to do BitcoinNG-style micro blocks, Aspen-like service chains or generic sidechains with just velvet or soft forks. Also see *[Flux: Revisiting Near Blocks for Proof-of-Work Blockchains](https://eprint.iacr.org/2018/415.pdf)*

## Layer 2 (Off-Chain)

Ergo can utilize multiple off-chain solutions, such as [Hydra](https://iohk.io/en/research/library/papers/hydrafast-isomorphic-state-channels/) and sidechains to compress blockchain bloat and provide similar benefits as zk-rollups. Ergo can also be compatible with other UTXO Layer 2 solutions, such as Bitcoin's Lightning Network. The implementation here will depend on the applications being built on Ergo. 

### Plasma

Ergo has native support for [AVL trees](avl.md), an efficient authenticated data structure that allows for proving the data's various properties without needing to know the entire set.

The ledger is stored as an AVL tree using **[Plasma](plasma.md)**. Users perform off-chain transactions with the bank, and the ledger keeps changing. Occasionally, the bank publishes a compact snapshot of the ledger on the blockchain.

You can use Plasma to create Plasma chains and make a full L2 solution. Right now, it is mostly used for data compression and simplifying contracts, though Plasma chains will likely come in the future.


> Plasma tutorials for Ergo have now been released. Please see [Bank](https://github.com/ergoplatform/ergo-jde/blob/main/kiosk/src/test/scala/kiosk/avltree/bank/Bank.md) & [AVLTrees](https://github.com/ergoplatform/ergo-jde/blob/main/kiosk/src/test/scala/kiosk/avltree/AvlTrees.md)



### NIPoPoWs
**NIPoPoWs:** [Non-interactive proofs of proof of work](http://docs.ergoplatform.org/dev/protocol/nipopow/) are essential for two reasons: Light Clients and Side Chains. Light clients, which consist of light nodes and light wallets, are efficient clients that do not need to hold the whole blockchain to verify transactions and enable efficient mobile wallets and faster miner bootstrapping. Clients can interact using only the block headers, thus reducing computational resources. Ergo has enabled NIPoPoW support since the genesis block. They can be applied to Ergo's blockchain with an easy-to-implement [velvet fork](https://www.coindesk.com/markets/2018/03/15/velvet-forks-crypto-updates-without-the-controversy/). NIPoPoWs can also be deployed to support PoW and PoS cross-chain communication. NIPoPoW implementations via *Velvet soft forks* enable **infinite scalability** via sidechains on top of Ergo. 

**State Channels (Hydra):** is a *peer-to-peer signing model*, and the design can work well for payment channels for simple purposes. The problem, however, is that the state channels are pre-set contracts for which the participants are defined at the launch. New contract creation is needed each time a new participant wants to use the channel. In return, there is higher privacy and security but little flexibility for an open system. IOHK has published a new model called [Hydra: Isomorphic State Channels](https://iohk.io/en/research/library/papers/hydrafast-isomorphic-state-channels/) that introduces multi-party state channels by utilizing both on-chain and off-chain-computations-powered-by-the-eUTXO-design. Other novel state channel constructions should be possible as well. It would be good to apply off-chain techniques to applications like ErgoMixer. Ergo is mentioned in the [Hydra whitepaper](https://eprint.iacr.org/2020/299.pdf). Research and discussions are underway. 


### Other Possibilities

#### **Lightning Network:** 

Due to the shared UTXO architecture, utilizing a Lightning network is also possible. In a lightning channel, two participants send their funds to a specific type of joint multi-sig wallet that allows them to create and enforce off-chain agreements. The network itself is just a bunch of these channels connected. You can then structure an off-chain payment across many channels, where none of the funds leaves any individual channel but shuffles around like an abacus.

#### **Rainbow Network:** 



The Rainbow Network is an off-chain non-custodial exchange and payment network supporting any assets for which two parties can agree on a price oracle. The Rainbow Network allows users to trade, borrow, lend, and make payments in synthetic assets entirely off-chain while having only one on-chain payment channel collateralized by a single asset.

As described in [this paper](http://research.paradigm.xyz/RainbowNetwork.pdf)

#### **Rollups**: 

Rollups are also possible via AVL trees. A roll-up involves rolling up collections of transactions, and the only concern is posting the data on-chain, not verification. 

There are two types of Rollups. 

- **Optimistic Rollups:** compute the transactions on a parallel compatible chain that communicates with the main chain. The model is optimistic because it relies on the *Fraud-Proof principle*, where the aggregators are not actively verifying layer two. Still, they interfere in the event of a fraud dispute. Disputes in optimistic rollups when computations are done only on data whose validity is disputed
- **ZK-Rollups** utilize [zkSNARKs](https://blog.ethereum.org/2016/12/05/zksnarks-in-a-nutshell/) (zero-knowledge succinct non-interactive arguments of knowledge), they can decrease network load by taking hundreds of transfers off-chain and combining or "rolling" them up into a single transaction. The security of the transactions relies directly on the main chain secured by adding mathematical proofs to validate transactions. However, it is relatively harder than hybrid approaches to implement all the functionalities of the mainnet with full security. Various projects are attempting to implement zkSNARKs.

Zk rollups have many issues in practice, and pairing compatible curves support in the core protocol is likely required.


#### **Zero-Knowledge Contingent Payments:** 

It is possible to make payments that are released if and only if the payee discloses some knowledge (in a trustless manner where neither the payer nor payee can cheat). Achieved using a combination of a `hash-locked transaction` and an external protocol to ensure the correct data is revealed in the hash lock release.

#### **FairSwap/FastSwap protocols:** 

As described in [this paper](https://eprint.iacr.org/2019/1296)

#### **Coinpools:** 

Another L2 solution for the UTXO model to consider is described in [this paper](https://discrete-blog.github.io/coinpool/)


## Roadmap (Dec 2021)

[Ergo protocol research and client development roadmap (Dec, 2021)](https://www.reddit.com/r/ergonauts/comments/qfjhw4/ergo_protocol_research_and_client_development/)

> We are researching different scalability proposals for Bitcoin, Cardano, and Ethereum, such as sidechains (which are also nice for testing new features), commit chains, rollups, isomorphic state channels, FairSwap etc. New opcodes are needed for some solutions for Bitcoin (so a little chance to see things in the real world), while Ergo allows for such constructions with no forks. Ergo will not just be a chain but a king of chains (which will improve the cryptoeconomic security of the protocol as miners will get additional rewards from sidechains).

> for improving the performance of the network, the reference protocol client (Ergo node) is getting different performance improvements in the p2p layer and not only right now

> bootstrapping via UTXO set snapshot and NIPoPoWs are in progress now. This should allow a client to have much faster bootstrapping without compromising security.

> 5.0 soft-fork is going to be proposed to miners soon; the main change is about switching to just-in-time-costing in ErgoTree evaluation which is giving a 5-6x boost in scripts processing (on real blockchain data)

> time to consider long-term cryptoeconomic security of the protocol; discussions have already started: https://www.ergoforum.org/t/ergo-emission-details-retargeting-via-a-soft-fork/2778/7

> for application development, more frameworks and ready apps are needed; there are some results to be announced already

> Plans for supporting the different application is out of the scope of this text and would be a topic of another piece.

## Resources

- [A Scalability Plan for Ergo](https://www.ergoforum.org/t/a-scalability-plan-for-ergo/226)
- [Network congestion on Jul 10th, 2021](https://www.ergoforum.org/t/network-congestion-on-jul-10th-2021/1945)
- [Long-term vision for Ergo](https://www.ergoforum.org/t/long-term-vision-for-ergo/2629)
