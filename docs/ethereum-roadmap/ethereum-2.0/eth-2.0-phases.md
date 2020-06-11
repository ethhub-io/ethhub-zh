---
title: Ethereum 2.0 Phases - EthHub

description: A guide to the phases and implementation plans of Ethereum 2.0.
---

# 以太坊2.0各阶段

_随着以太坊2.0不断进行大量的研发工作，该页面可能自然而然地就会过时了。我们只能尽最大的努力保持其更新。最后一次更新时间为2019年11月16日。_

This page is also available in [German](eth-2.0-phases-german.md) and [Portuguese](eth-2.0-phases-portuguese.md).

## 介绍

以太坊下一步的重要网络升级称为 Ethereum 2.0，eth2 或 Serenity，将带来分片（Sahrding）、权益证明共识机制（Proof of Stake）、新虚拟机（eWASM）等重大改变。 首先我们需要明确的是，eth2升级并非一蹴而就，而是划分成多个阶段逐步进行。以下内容旨在提供eth2的阶段概况参考，让关注以太坊的爱好者们对升级路线心中有数。

## 设计目标

以太坊研发者Danny Ryan就Ethereum 2.0给出了五个不同的[设计目标](https://github.com/ethereum/eth2.0-specs#design-goals)：

* **去中心化：** 允许处理能力达O\(C\) 的消费级笔记本电脑处理/验证 O\(1\) 个分片（可能是系统上任何一层的验证，如信标链）；
* **强韧性：** 在主网分区之后，即使大部分节点离线，整个系统依旧能够运行；
* **安全性：** 通过密码学技术和设计技术提高验证者的总人数和单位时间内的参与者数量；
* **简洁性：** 最大程度地降低复杂性，哪怕会导致效率有所下降；
* **持久性：** 选用的组件要能够抵抗量子计算，或是选用可用的替换型组件。

## 阶段0 - 信标链

### **本阶段包含什么内容？**

阶段0指的是信标链的发布。信标链将为其自身和将来所有的分片链管理Casper权益证明协议。正如Ben Edgington所[提到](https://media.consensys.net/state-of-ethereum-protocol-2-the-beacon-chain-c6b6a9a69129)的，该阶段的工作涉及许多方面：管理验证者及其押金；在每一步骤中为每个分片选择区块提议者；组织验证者进入委员会并对提议区块进行投票；应用共识规则、验证者的奖惩机制；将信标链作为一个锚定点，分片从中记录其状态以促进跨分片交易等。

信标链上的主要负载来源将是“证明”（attestations）。证明是针对分片区块可用性的投票，同时也是信标区块的PoS投票。当一个分片区块取得了足够数量的证明，将创建一个“交联”（crosslink），该“交联”可以确认将分片片段（到该分片区块为止）整合到信标链中。

阶段0将使用 Caspe FFG（the Friendly Finality Gadget/友好的最终确定性小工具）来保障最终确定性（finality）。简单来说，最终确定性意味着某项特定操作一旦完成，就将永远铭刻在历史中，并且无法还原。

#### **将ETH移至信标链**

阶段0中，信标链上的所有ETH都会以单向交易形式发送至存款合约中，其中会附带交易数据，以识别该存款对应的验证者身份。存款合约受网络中所有验证者的监控，之后会将存款提交到信标链。

当一名验证者的公钥的余额达到了32 ETH，那么他就会以活跃验证者的身份进入队列等待激活。

**请注意：** 在阶段0中，仅支持向存款合约进行单向传输，无法将已存入的ETH转回Eth 1.x。在阶段1中预计会有所改动。

### **该网络会是怎样的？**

阶段0一旦完成，以太坊网络上就会有两条链存在。为了避免混淆，我们分别称其为Eth1链（当前的PoW主链）和Eth2链（未来的信标链）。在阶段0，用户可以将其ETH从Eth1链转移至Eth2链，并成为验证者。然而，用户目前暂时不能将ETH转移Eth1链。

用户需要通过信标链客户端来运行信标链节点。目前，多个[团队](/ethereum-roadmap/ethereum-2.0/eth2.0-teams/teams-building-eth2.0/)以一贯的标准以太坊客户端（如Geth、Parity、Pantheon等）为参照，开发着新的信标链客户端。其中大部分团队都会定期更新其客户端开发进展，有的团队还会提供赏金，以激励更多开发者参与到以太坊2.0的工作中。读者也可以进入[Gitcoin](https://gitcoin.co/grants/)进行捐款。

就其本身而言，信标链的用处可能不是特别大。然而，作为过渡到Eth2.0的第一阶段，信标链是整个系统的基础。

**注意事项**

* 想要激活信标链，需要达到最低ETH质押值。该值在[未来上线于Eth1.0的存款合约](https://github.com/ethereum/deposit_contract/blob/master/deposit_contract/contracts/validator_registration.v.py#L3)（deposit contract）中被定义为`CHAIN_START_FULL_DEPOSIT_THRESHOLD`
* 在阶段0中，所有用户交易和智能合约计算仍在Eth1链上进行

## 阶段1 - 分片链

### 本阶段包含什么内容？

分片链是将来可扩展性的关键，因为其允许并行处理交易而大大提升了吞吐量，目前最新方案是在阶段1中部署64个分片（将来或许会更多）。

阶段1主要涉及在分片链写入数据，并实现其有效性和共识性。分片链尚未拥有账户、资产或智能合约。由于缺乏支持并行处理交易的执行环境，阶段1更像是试运行分片结构，而不是直接借助分片进行扩展的尝试。信标链把分片链区块看作是没有结构和意义的简单位集合。

**交联** <br/>
每个分片的当前状态（“组合数据根”）会作为交联周期性地记录在信标链区块中。当信标链区块最终确定后，相应的分片区块也将被视为最终确定，如此其他分片就可以信任该分片并进行跨分片交易。 <br/>

交联是委员会的一个签名集合，作为分片链中某个区块的证明（attestation），证明其可以被添加进信标链中；是信标链获取分片链状态更新的主要方式；是异步跨分片通信的基础设施。

信标链在各个slot中为每个分片随机分配的验证者，只是就分片区块的内容达成共识，而要证明分片的内容和状态，则需要通过交联。分片区块里面包含什么信息并不重要，重要的是分片里的所有委员会能够定期地同步信标链的内容，并就其达成共识。

### 该网络会是怎样的？

阶段1之后，Eth1链和Eth2链仍然同时运行。

### 注意事项

* 在阶段0至阶段2中，在向Eth2链过渡和进行测试时，PoW主链（Eth1）依然运行。这意味着Eth2.0各阶段的验证者和PoW矿工都能获得奖励。因此，两条链的总通胀率在最初可能会飙升，但随着PoW链的地位逐渐下降，通胀率开始趋向0-1％。

## 阶段2 - 状态执行

### 本阶段包含什么内容？

到了阶段2，整个系统的功能开始融合。这时，分片链从作为简单的数据容器过渡到结构化链状态，并将重新引入智能合约。每个分片将管理基于[eWASM](https://github.com/ewasm/design)的虚拟机。该虚拟机将支持帐户、合约、状态的执行，以及和我们所熟悉的solidity语言的其他机制。 我们可以期望在阶段2或之前将熟悉的工具（例如truffle，solc，ganache）移植过来，支持eWASM。

阶段2还引入了“执行环境（EEs）”的概念。 可以按照开发人员认为合适的任何方式来构建分片中的执行环境——可用于UTXO-style链、Libra类系统、费用市场中继器等。每个分片都可以访问任何执行环境，并能够在其中进行交易以及运行智能合约并与之交互。请注意，执行环境仍出于紧密研发工作中。

### 注意事项
* dApp必须选择自己将在哪个分片进行部署。此决定非常重要，因为跨分片通信在Eth2上有所不同。而造成这种差异的原因是，跨分片通信不同步，也就意味着分片间失去了一些可组合性。 <br/>
* dApp将必须拥有能消耗一个特定分片所有资源的大量数据，从而证明自身有必要拓展到多个分片。<br/>
* 此阶段将eWASM作为以太坊虚拟机2.0（EVM2.0）提供给分片使用。
* 我们该何时以及如何将以太坊1.0的账户和合约迁移到以太坊2.0中去，这是一个待解决的问题。点击查看[升级计划](https://ethresear.ch/t/the-eth1-eth2-transition/6265)

## 相关资源

* [以太坊路线图](https://ethos.dev/ethereum-2020-roadmap/)
* [以太坊2.0信息](https://hackmd.io/e4cNiocFTiS67j6yJ_XHPw?view)
* [以太坊2.0规范](https://github.com/ethereum/eth2.0-specs)
* [易读版阶段0](https://notes.ethereum.org/jDcuUp3-T8CeFTv0YpAsHw?view)
* [分片路线图](https://github.com/ethereum/wiki/wiki/Sharding-roadmap#strongphase-3strong-light-client-state-protocol)
* [以太坊协议的状态](https://media.consensys.net/state-of-ethereum-protocol-2-the-beacon-chain-c6b6a9a69129)
* [以太坊2.0设计目标](https://media.consensys.net/exploring-the-ethereum-2-0-design-goals-fd2d901b4c01)
* [ETHMagicians 问答环节](https://medium.com/ethereum-magicians/demystifying-the-road-to-ethereum-2-0-8130ade8d00f)
* [虚拟机eWASM](https://www.coindesk.com/open-heart-surgery-inside-ethereums-crucial-replacement-of-the-evm)
