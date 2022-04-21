---
title: "链上治理"
linkTitle: "链上治理"
weight: 2
date: 2022-04-19
description: >
  ChainX user guide
---

### 2 链上治理
点击治理转到治理主页

![](/images/goverment_homepage.png)

#### 2.1 民主权利

现阶段民主权利主要体现为公民提案与全民公投。公民提案即每个公民可以提任意特定提案，该提案在运行时采用特权函数调用的形式（其中包括最强大的调用：set_code，它可以切换出运行时的整个代码，从而实现原本需要的“硬叉”）。每个固定周期内，会选出该期间附议最多的议案，进入全民公投。全民公投同样具有固定的投票发生时间段，然后被计票，如果投票获得批准，则会进行各类交易调用。全民公投是简单，包容，基于抵押的投票方案，主要根据不同锁定时长及锁定数额影响投票的权重。所有全民公投通过的议案都有与之相关的执行延迟，这是全民公投通过到实施更改之间的时间段。
注释：后期我们将形成类似 polkassembly 的提案论坛，公民皆可于论坛提出文字方案，待达成多数人赞同后，再以提交特权函数调用的形式发起链上提案。

#### 2.2 民主提案
**第1步**
点击提交原象hash,如图，原像哈希是0xc3ddb44b117574311101843e1fdb64d0fc5491fcd16b832f76e773cd8b060b12

![](/images/submit_preimage.png)

**第2步**
提交提案，将刚才的原象hash填进去，点击提交

![](/images/submit_proposal.png)

#### 2.3 民主附议
**第1步**
选择赞同的提案，点击“Second“

![](/images/second.png)

**第2步**
选择参与附议的账户，点击“Second“

![](/images/second2.png)


#### 2.4 议会

议会是一个代表被动利益相关者的链上集体。它通过提出重要的改变和取消毫无争议的危险提议来做到这一点。任何 PCX 代币持有者都可以竞选议会，
用户可以投票给他们支持的任意数量的候选人， 议会选举还说明了用户投票给议员的顺序，方法是根据排名领先的数量对议员进行评分。得分最高的议员为首席议员。

##### 2.4.1 参选议会
**第1步**
导入账户后，点击治理栏中的“议会”，进入议会界面

![](/images/council.png)

**第2步**
点击“Submit Candidacy”

![](/images/submit_candidacy.png)

**第3步**
确认参与竞选的账户无误后，点击“提交”

![](/images/submit_candidacy2.png)

**第4步**
输入密码后，若参选成功，页面将出现绿色弹窗

![](/images/submit_candidacy3.png)

##### 2.4.2 投票

**第1步**
点击“Vote”

![](/images/vote.png)

**第2步**
填写要投票的数额，从候选人栏中选择投票人选，点击“Vote”
注:一次最多可以给16个人投票，投票权重根据顺序依次递减

![](/images/vote2.png)

**第3步**
输入密码后，若投票成功，界面将显示下图绿色弹窗

![](/images/vote3.png)

#### 2.5 国库

##### 2.5.1 国库提案

**第1步**
导入账户后，点击治理模块栏中的“treasury”，进入财政界面

![](/images/treasury.png)

**第2步**
点击“Submit Proposal”

![](/images/treasury2.png)

**第3步**
填写受益人及金额，点击“Submit Proposal”

![](/images/treasury3.png)

##### 2.5.2 小费提案

**第1步**
导入账户后，点击治理模块栏中的“treasury”，进入财政界面

![](/images/treasury.png)

**第2步**
点击“Tip”

![](/images/treasury4.png)

**第3步**
点击“Proposal Tip”

![](/images/treasury5.png)

**第4步**
填写受益人地址、奖励原因，点击“Proposal Tip”

![](/images/treasury6.png)