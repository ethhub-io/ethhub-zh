---
title: Optimistic Rollups - EthHub

description: Optimistic Rollups（OR）是一种第二层解决方案，不直接在以太坊基础层中构建，而是基于以太坊进行构建
---

# Optimistic Rollups

## 简介

Optimistic Rollups（OR）是一种第二层解决方案，也就是说不直接在以太坊基础层中构建，而是基于以太坊进行构建。好处在于可以规模化运行智能合约，同时还能受到共享以太坊的安全性。其构造类似于Plasma，虽然无法达到Plasma几近无限的扩容能力，OR选择使用与EVM兼容的OVM（Optimistic虚拟机），使其能够像以太坊一样运作。

其名称“Opmistic Rollup”源自于这个解决方案本身的特征。之所以采用“Optimistic”（乐观），是因为聚合者仅发布最少量的所需信息，而无需提供任何证明，前提是聚合者没有实施欺诈行为，且仅在发生欺诈时提供证明。之所以使用“Rollups”，是因为交易以捆绑形式提交到主链（也即，交易被rolled-up）。

## 概述

与大多数第二层解决方案类似，在Optimistic Rollups上进行交易的资金存储在以太坊智能合约中，在该合约中，用户存入资金，聚合器签名并提交欺诈证明。此类解决方案的交互流程如下：

1. 用户将智能合约的部署交易从链下发送给聚合者（此架构中的区块生产者）

2. 聚合者本地部署该笔用以创建新智能合约的交易

3. 聚合者计算新的状态根（即默克尔根）

4. 聚合者创建一笔以太坊交易，包含步骤3中所计算的状态根

5. 如果有任何用户发现聚合者部署了无效状态根（包含无效交易的状态根），都可以“挑战”该聚合者，并且重新发布有效状态根，其中要包含所需的默克尔证明。继而，欺诈者会受到惩罚（扣除一定押金），包括提交该无效状态根的聚合者，以及基于欺诈区块继续生产区块的聚合者

6. 如果无效区块已经被提交，且欺诈证明是正确的，那么第二层链可以回退到发生欺诈之前的区块重新开始工作。

提示：只要在智能合约中锁定押金，任何人都能成为聚合者

提示2：步骤5可能会出现的抢先交易问题有多种解决方式，例如可以借助[submarine sends](https://libsubmarine.org/)（潜艇交易）。

## 用户体验

构建在第二层并使用Optimistic Rollup架构的Dapp为用户带来了经济抽象的体验（例如免交易费、使用ERC-20支付等等），以及几近即时的交易速度（交易时间约为200毫秒，一眨眼的功夫！）

## 优缺点

优点：

* 通用计算灵活性（图灵完备/兼容EVM）
* 提升扩容性（目前以太坊第一层吞吐量为10tps，可提升至200-2000tps）
* 所有数据在链上都可用（无需信任链下数据提供者）
* 用户体验更佳（如上文所述）

缺点：

* 吞吐量不如其他第二层解决方案（Plasma、ZK Rollups等）
* 伴随着一些安全性问题（参见下文）

## Demo

由Uniswap和Optimism（原Plasma Group）共同开发的Unipig交易所，类似于Uniswap，作为一个概念证明（PoC）应用在Devcon 5上进行了演示，访问[unipig.exchange](https://unipig.exchange/)即可免费试用。

## 安全性

Optimistic Rollups能够运行的前提是，我们必须假定以太坊网络中大多数验证者（Eth1中的矿工，Eth2中的质押者）都是诚实的，并且至少有一个聚合者不审查交易。

## Resources

* [Unipig](https://unipig.exchange/)
* [Plasma group](https://plasma.group/)
* [ZK Rollups vs Optimistic Rollups](https://blog.idex.io/all-posts/rollup-rundown)
* [深入探析 Optimistic vs. ZK Rollup](https://medium.com/matter-labs/optimistic-vs-zk-rollup-deep-dive-ea141e71e075)
