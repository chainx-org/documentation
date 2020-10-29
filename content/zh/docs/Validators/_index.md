---
title: "验证人"
linkTitle: "Validators"
weight: 4
date: 2017-01-05
description: >
  ChainX validator guide
---

{{% pageinfo %}}
ChainX 验证节点指南
{{% /pageinfo %}}

## 准备事项

### 一台VPS

  最简单的方式是使用一台云主机， 您可以自由选择任一主机提供商。 接下来的教程将以`Arch Linux`为例。

### 安装`chainx`程序

  我们假设您已经安装好:
  - rust:nightly-2020-09-30
  - rust target wsam32-unknown-unknown
  - ntp client

  接下来， 您需要按照以下步骤完成编译工作：
``` bash
git clone https://github.com/chainx-org/ChainX
cd ChainX
git checkout develop-2.0 && git pull
make
```
之后，`chainx`程序将在`target/release/`目录下

### 启动验证节点

  如果之前有以非archive模式启动节点， 您需要移除相关数据库。之后运行：
```bash
./chainx --chain=testnet --name=$YOUR_NODE_NAME --validator
```
{{%alert %}}如果成功启动， 您将可以在[Telemetry(stat.chainx.org)](stat.chainx.org)上看到您的节点。{{%/alert%}}

## 链节点

### 注册账户

您可以在[新钱包(https://dapp-v2.chainx.org)](https://dapp-v2.chainx.org)上注册账户。

![add-account](/images/add-account.png)

### 注册节点

注册成功后，您可以在[`Network>Staking`](https://dapp-v2.chainx.org/#/staking)页面上注册节点。

![register-node](/images/register-node.png)

{{% alert  %}}每个账号只能注册一次， 另外， 注册之前您需要保证有足够余额支付交易手续费。新注册的节点默认参选， 您无需进行额外的操作。除了注册节点时的初始质押币， 您也可以通过**投票**的方式再次进行质押。选举时间结束后， 总质押量排名前30的节点， 将成为验证人参与共识。{{%/alert%}}

![rebond](/images/bond.png)

### 设置`sessionKeys`

您可以通过
```bash
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "author_rotateKeys", "params":[]}' http://localhost:9933
```
来获取`sessionKey`, 之后在[`Developer>Extrinsic`](https://dapp-v2.chainx.org/#/extrinsics)通过`setKeys`设置.

获取的结果应形如：

```json
{
   "jsonrpc":"2.0",
   "result":"0x42a7d53603bac173eb9684ac133e35bcd4a49f308387a0e748b6f6a6dbf5635313f065a67a42a78a2c3e261a63523d92d4e03f9e7c9bba7c3d13b13b6983f0724c46b00699362a374f3fe43dd668eae6fcd815d0b84f88998ca5fc1c41e09b2412e2b9d3a322d9229a24cbce31d53358edc77b6fbaca7d038247743f40b6f205",
   "id":1
}
```

其中，`result`字段为获取的`sessionKey`。设置`sessionKey`的操作如下：

![setKeys](/images/setkeys.png)

{{%alert%}} 目前， `proof`暂时无效， 您需要填入`0x00`{{%/alert%}}


## 节点惩罚

ChainX 在每个 session 会发放奖励，同时惩罚可能作恶的节点，惩罚类型一般包括节点双签与节点离线。一旦节点被发现作恶，作恶节点在该 session 的应得奖励将会被全部惩罚进国库，同时按照链上汇报的作恶系数惩罚节点奖池, 即：

```text
penalty = max(session_reward + reward_pot_balance * F, minimum_penalty)
```

- `penalty`: 应罚金额
- `session_reward`: 节点的 session 奖励
- `reward_pot_balance`: 节点奖池金额
- `F`: 惩罚系数，由 babe 与 im-online 模块计算得出:
  - babe: 节点双签，[frame/babe/src/equivocation.rs](https://github.com/paritytech/substrate/blob/c60f00840034017d4b7e6d20bd4fcf9a3f5b529a/frame/babe/src/equivocation.rs#L265)
  - im-online: 节点离线，[frame/im-online/src/lib.rs](https://github.com/paritytech/substrate/blob/c60f00840034017d4b7e6d20bd4fcf9a3f5b529a/frame/im-online/src/lib.rs#L771)
- `minimum_penalty`: 最小惩罚值, 即每次惩罚至少罚 `minimum_penalty`。

ChainX 节点作恶并不惩罚本金，而是惩罚节点奖池。当节点奖池被罚完后，节点会被强制退选。

```Rust
if penalty > reward_pot_balance {
    // force the validator(offender) to be chilled
}
```
