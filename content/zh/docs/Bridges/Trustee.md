---
title: "信托"
linkTitle: "Trustee"
weight: 9
description: >
  信托介绍
---

## 介绍

信托存在的意义为对于跨链而言，若ChainX可以支持被跨链（如Bitcoin）的轻节点验证，而被跨链无法支持ChainX的轻节点验证，则只能进行单向跨链（单向Relay）。因此能够双向跨链的链不需要信托，只有单向跨链的链需要信托。**信托是对于无法双向跨链的一种妥协方案。**

对于这种妥协方案需要具备很高的灵活性，很多细节无法在链上处理（如信托资质确认等），因此信托在某种程度上可以看做是需要一定信任的。因而ChainX的给予信托**极大的灵活性及权利**

因此对于单向跨链（如Bitcoin）而言，在被跨链端需要有一个地址/账户锁定（保存）需要跨链的资产，而**生成并操控**这个地址/账户的相关人员，在ChainX中称为“信托”。

**信托**：是ChainX上托管对应链的代币的角色，因此信托是跨链代币的**真币托管者**。因此信托的参与者必须经过严格的KYC进行验证并公布自己的节点信息，且在验证节点中排名靠前，以保证充足的利益绑定关系防止作恶。

**信托的分类**：信托以链为单位作为区分，如Bitcoin对应的信托只处理BTC，将来Ethereum若使用信托，则ETH，Ethereum上的ERC20代币都属于信托托管。

**被跨链（原链）**: 在ChainX上对跨链的称呼，比如Bitcoin。

**被跨链（原链）代币**在ChainX上对跨链过来在ChainX上的凭证的称呼，比如Bitcoin跨链到ChainX上，原链上的BTC被锁定到信托的多签地址中，在ChainX上会1:1兑换一个ChainX上的代币。

**候选信托**：在已经是节点的情况下将自己的信托信息注册于ChainX上，即是候选信托。

**注册成为候选信托**：由于确保跨链地址的安全性与公开性，一个账户（操作节点的实体）欲成为信托节点时，首先需要将自己注册为一个节点（候选节点、验证节点），然后才能将自己想要成为某条链（如Bitcoin）的信托的相关信息（热公钥/地址，冷公钥/地址）注册到ChainX上，称为**候选信托节点**。

**候选信托注册的信息**：注册信托信息最核心部分需要具备**热公钥/地址，冷公钥/地址**，其中热公钥/地址在链上参与生成热地址，冷公钥/地址在链上参与生成冷地址，热地址用于接受用户的跨链充值与执行跨链提现，当热地址保存数量交大后转移部分资金进入冷地址保管。

**信托列表（群体）**：从候选信托中选出部分节点可组成信托列表。对**真币托管的地址**由当前的信托列表提供的相关信息热公钥/地址，冷公钥/地址）**在链上生成**，该地址/账户一般为**多签（多重签名）地址/账户**，对该地址/账户的操作需要当前信托列表中的大部分信托通过多签的形式操作。

**信托换届（session）**：信托列表存在更替行为，一轮信托列表称为“一届（session）”信托列表，信托列表的更替称为“信托换届”。ChainX的给予信托**极大的灵活性及权利**，因此信托**只可由上一届信托列表选定下一届信托列表**。

**多签（多重签名）地址/账户**：注意这个多签（多重签名）地址/账户是指对应被托管链上的地址（如Bitcoin的多签地址）。候选信托将自己的信托信息注册于链上后，当换届指定了新一届的信托后，ChainX根据新一届的信托列表在链上的信托信息，**自动生成**当前届的多签（多重签名）地址/账户，在当前届内，需要这些信托在**用户提现**时参与对对应链上的签名（多签）操作（如Bitcoin参与对待签原文的签名）。

**信托列表处理用户提现**：当前届的信托列表中的信托者应监控用户提现的申请列表，当用户可以提现时，当前届中的任意一个信托者可以根据用户的申请列表组件一个原链上的提现交易的原文（Bitcoin提现的待签原文）并发送到ChainX上，其他信托者在ChainX上对这提现原文进行签名（或否决这个提现），若签名数量达到多签要求，则这笔交易将会广播到原链上，并在ChainX被确认后销毁ChainX上对原链的跨链代币。

**信托多签操作**：注意这个多签指的是在ChainX上，与信托相关的一些操作需要通过ChainX链上的**多签投票决定**，如更改Bitcoin用户提现手续费，上一届信托列表选定下一届信托表等。

## 首届信托节点

第一届信托节点由测试网时期，经过良好训练的若干个节点组成，等待网络逐步稳定，新节点加入并熟悉后开始进行逐步换届。

- **热多签地址** 用于接收用户日常的充值和提现
- 如果热地址资金存余过多，则由信托节点们线下商量后打入 **冷多签地址** 进行 **热转冷** 存储
- 如果遇到大笔提现，还可以进行 **冷转热** 操作

## 信托节点换届

#### 下一届信托节点审核

1. 目前**拟定**信托节点的换届周期是10天左右，首先排查验证节点中设置了Bitcoin链信托的节点，没设置的无权参与选举

2. 然后取其中得票数最高的N个，随着系统成熟度发展，N从4逐步增长到15

3. 由上一届信托节点审核这N个节点是否已经公开了自己的身份信息、是否有基本的节点和资金保管能力，防止出现恶意节点，从中筛选出M个。

   选定的最核心条件为保证下一届信托的可靠性 因此判定条件可以通过下面进行筛选，并经过当前届的信托列表的**信托多签操作**:

   - 熟悉ChainX信托流程（重要）
     - 具备一定运维能力，需要维护对应链的一个全节点
     - 对对应的区块链操作有深入理解（比如Bitcoin信托需要熟悉Bitcoin的多签地址操作的概念与流程，需要具备操控适合自己的Bitcoin多签的工具的开发能力）
     - 具备一定技术能力，能够根据ChainX SDK及其他需求开发适合自己的工具
   - 节点愿意当选信托
   - 节点排名靠前
   - 节点抵押数较大且投票数较大
   - 节点能灵活操作冷热钱包，有冷钱包管理技术
   - 节点身份已知，且背后有较大利益关系背书（如公司，名望等）
   - 其他有效筛选条件

#### 下一届信托节点准备

1. 向社区公布该M个节点的的身份、各自的热公钥、各自的冷公钥
2. 通过**链上的接口模拟计算**生成这M个节点的冷热多签地址
3. 向这两个多签地址分别打入小额的真实币用于测试
4. M个节点组装生成 M/M 的全额多签签名，用于说明各自均会使用多签，并且私钥正确

其中对于3，4两步详细描述如下：

- 当前届信托的冷热地址称为：cold-addr1，hot-addr1，下一届模拟生成的冷热地址称为：cold-addr2，hot-addr2。

- 测试过程如下：

  - cold-addr1 -> cold-addr2 (init, test cold addr could deposit)
  - cold-addr2 -> hot-addr2 (test cold addr could withdrawal, and hot addr could deposit)
  - hot-addr2 -> cold-addr2 (test hot addr could withdrawal)
  - cold-addr2 -> cold-addr1 (optional, just return test BTC)

  ![img](https://github.com/chainx-org/images/raw/master/trustees_addr_test.png)

#### 链上信托节点换届

1. 上一届信托节点发起ChainX多签交易，提交新一届信托节点的 ChainX账户地址，这些账户地址应该已经经过模拟测试，保证注册的信息是正常可用的，将要生成的地址或合约是可用的。
2. 完成ChainX系统内的换届，用户看到的充值目标地址和轻节点程序监听的地址会变更

#### 托管资金移交

1. 首先完成换届，此时链上的多签地址已经发生了替换
2. 上一批信托节点将两个老多签地址的资金，线下组织后打入新的两个多签地址，完成资金交接。
3. 换届资金转移完成

## 信托多签操作

由于信托为一个群体，在ChainX链上（注意非原链，请区分清楚）操作和信托相关的功能需要通过ChainX上的多签模块进行操作。

信托也是一个多签操作群体。在ChainX链上，针对每一条链的每一届信托都根据信托列表生成了对应他们可以操作的信托多签地址。对于信托而言一般需要多签操作的功能如下：

- 信托换届
- 操控链的用户提现手续费
- 撤销或修正用户的提现申请

## 信托收益

信托与普通验证者相比要承受更多的责任与义务，因此ChainX目前拟定给予信托的奖励有：

1. 以BTC为例：在信托换届后，本届信托内提现会留下很多用户提现的手续费结余，手续费结余在满足后续信托执行后剩余可由这届信托瓜分
2. 议会将拿出20%的收益奖励信托
3. 在钱包内ChainX将信托节点置顶排名，置顶有利于信托吸引更多的投票，增强自己的声誉

## 信托相关操作

TODO