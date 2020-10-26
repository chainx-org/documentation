---
title: "资产转接桥/网关（2.0中称为资产网关Gateway）"
linkTitle: "Bridges"
weight: 9
description: >
  资产转接桥/网关指代其他链的资产转移到ChainX或从ChainX转移回去的控制模块。
---

{{% pageinfo %}}
资产转接桥/网关（2.0中称为资产网关Gateway）
{{% /pageinfo %}}

## 介绍

资产转接桥/网关指代其他链的资产转移到ChainX或从ChainX转移回去的控制模块。

例如当Bitcoin链上的BTC资产需要转移到ChainX上成为X-BTC，或将ChainX链上的X-BTC转移回到Bitcoin链上的过程，由Bitcoin转接桥/网关控制这个过程的验证，及资产发放/销毁过程。

## 术语：

> 本链：指代ChainX链自身，如“本链上处理某事物”含义为“在ChainX链上包含并执行某事物的过程”。
>
> 原链：指代资产移动到ChainX后，资产来源的那条链，如X-BTC来源于Bitcoin链存在于ChainX上，则Bitcoin链称为X-BTC的原链。
>
> 异构链：指代原链的模型结构和ChainX/Substrate链模型结构差距较大，因此很难有统一的形态处理所有的异构链，需要针对不同的链有对应的处理。
>
> 同构链：指原链的模型结构和ChainX/Substrate链结构一致，基本指代用Substrate开发的区块链。

## 异构链跨链的核心原理

相对于Polkadot平行链的跨链原理而言，异构链跨链本质上是：

1. 在ChainX链上知道/证明在另一条链上确实发生过某件事情；
2. 若这件事情是锁定资产，那么在ChainX链上发放对应的资产给某个账户；
3. 若这件事情是释放资产，那么在ChainX链上将某个账户上对应的资产进行销毁。

因此异构跨链从原理上而言是一种Oracle模式的变种。

当前解决Oracle数据发送到某条链上且保证数据正确性的方式原理上只有两种

1. 数据自身携带证明（自身可证明），如轻节点/零知识证明等
2. 数据靠投票证明/靠授权者证明，如Substrate offchain worker/某link等

因此异构跨链的模式也不会跳出以上两种原理。

## 网关分类

对于资产在ChainX链与原链的移动过程需要分为以下两种情况：

* 一个资产跨到ChainX上（即充值）；
* 跨到ChainX上的资产返回到原链上（即提现）；

由于异构链上功能差异很大，因此一个资产相对于ChainX上的充值/提现很多情况下解决方案是不相同的。

当前对于ChainX的资产网关模型，总共设计了4种类型以应对不同的异构链模式：

以 A公链为例子， A 可以通过4种方式处理资产移动到ChainX及移动回原链的范式：

1. 轻节点验证

   在ChainX上集成原链的轻节点，将轻节点的区块头及对应的交易提交到ChainX链上，通过轻节点数据自带证明的方式在ChainX链上执行交易的验证，确定交易在对应异构链（原链）上真实发生。需要该异构链具备轻节点的功能。若没有轻节点功能无法支持。

   轻节点验证大多用于充值过程，若需提现过程也使用轻节点，则要求原链上能编写ChainX轻节点验证逻辑，该功能几乎只能在ChainX同构链上使用，除非该原链支持复杂的功能性逻辑。

   **该模式是纯去中心化方式跨链，通过轻节点自带证明的方式保证数据正确性。**

   例子：ChainX上的X-BTC充值过程。

2. 多签控制

   在ChainX上跨链资产的控制方式及将资产返回原链的操作方式。多用于解决轻节点及POA充值跨链资产后，资产的管理及提现过程。含义为原链资产跨链到ChainX上，由原链上支持的多链模式由多人持有公钥，锁定被跨链的资产。当资产需要从ChainX上返回到原链上上，多人参与操作多签，释放原先锁定的资产。

   多签控制一般会在原链上分配一个热地址和一个冷地址，其中资产的充值提现过程只在热地址上发生，长时间不移动的资产转移到冷地址上保存，以保护资产安全性。

   当多签控制与轻节点结合时，提现过程一般会由轻节点验证方案对提现的交易进行验证。

   **该模式是多中心化方式跨链，通过多签私钥分散的形式保证资产安全性。**

   例子：ChainX上的X-BTC提现过程。

3. POA（Proof of authority）

   即某个经过授权的个人或主体在原链锁定/释放资产，在ChainX链上发放/销毁资产的过程.

   **该模式是中心化方式跨链，通过信任某实体的形式保证资产安全。**

4. 抵押发行

   通过抵押PCX或者X-BTC， 在Kusama/Polkadot的ChainX平行链上跨链自己的主网代币

   **该模式是新型去中心化跨链方式。**

   