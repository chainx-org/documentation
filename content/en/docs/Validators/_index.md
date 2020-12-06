---
title: "Validators"
linkTitle: "Validators"
weight: 4
date: 2017-01-05
description: >
  ChainX validator guide
---

{{% pageinfo %}}
Guide to Validator Node for ChainX 2.0
{{% /pageinfo %}}

## Preparation

### A VPS

The most easiest way is to use a cloud hosted vps. You can choose any provider you like.

#### Hardware requirement for testnet

- Cpu 2 core
- Mem 2GB
- Bondwidth 1Mbps
- Operation System Ubuntu18.04(not required)

#### Hardware requirement for Mainnet

- Cpu 4 core
- Mem 4GB
- Bondwidth 10Mbps
- Disk SSD 300G
- Operation System Ubuntu18.04(not required)

#### Use Docker Image

You can launch a validator node by run follow commands:

```bash
docker run -it -p 8086:8086 -p 8087:8087 chainxorg/chainx:v2.0.0 /usr/local/bin/chainx --name deeeemo --chain=mainnet --validator
```

{{%alert%}} You can use `-v` options to custom your config file or db base path.{{%/alert%}}

### Install `chainx`

#### Build from source
#### Preparation

Dependencies list:
- `clang`
- `gcc`
- `rustup`

#### Installation

Install toolchain:

```bash
$ rustup install nightly-2020-09-30
$ rustup override set nightly-2020-09-30
$ rustup target add wasm32-unknown-unknown --toolchain nightly-2020-09-30
```

Get source code:

```bash
$ git clone https://github.com/chainx-org/ChainX
$ cd ChainX
$ git checkout v2.0.0
$ cargo build --release
```

After compilation, `chainx` is under `target/release`.

#### Download pre-built binary.

Download binardy from [GitHub release(https://github.com/chainx-org/ChainX/releases)](https://github.com/chainx-org/ChainX/releases). 

#### Sync chain state

Before start, you should make sure your chain state is sync with network.

```bash
$ ./chainx --chain=mainnet --pruning=archive
```

After that, restart node with validator mode.

```bash
$ ./chainx --chain=mainnet --validator
```

or, simplely launch with validator mode directly.

```bash
# 使用 --validator 时，同时将默认启用存档模式即 --pruning=archive
$ ./chainx --chain=mainnet --validator
```

You **should** confirm you have set Session Keys and your node has synchronized with network before nominating your node.
{{%alert%}}If error occured during synchronization， check your system if synchronized with network time. And retry after clean database. {{%/alert%}}

#### Configuration

Recommendation for validator node:

```json
{
  "log-dir": "./log", // log dir
  "enable-console-log": false, // whether print log to console
  "no-mdns": true,
  "validator": true, // true is required for validator node
  "ws-external": false, // external rpc port is unsafe, and recommand as false
  "rpc-external": false, // external rpc port is unsafe, and recommand as false
  "log": "info,runtime=info",
  "port": 20222, // port for p2p network 
  "ws-port": 8087, // port for ws rpc service
  "rpc-port": 8086, // port for http rpc serrvice 
  "pruning": "archive", // Strongly recommand 
  "execution": "NativeElseWasm",
  "db-cache": 2048, // Cache for node database
  "state-cache-size": 2147483648, 
  "name": "Your-Node-Name", // Display name in Telemetry
  "base-path": "<数据存放路径>", // database path. `$HOME/.local/share/chainx/chains/$CHAIN_TYPE/db` by default
  "bootnodes": [] // seed
}
```

{{%alert color="warning"%}}部分 rpc 服务属于敏感操作，如需暴露于公网，建议使用代理服务器进行过滤（详见：[https://github.com/paritytech/substrate/wiki/Public-RPC](https://github.com/paritytech/substrate/wiki/Public-RPC)）。如果您已知悉并了解相关风险，可在启动节点时加入`--unsafe-{ws,rpc}-external`参数{{%/alert%}}

{{%alert %}}
节点成功启动后， 可以在[Telemetry(stats.chainx.org)](https://stats.chainx.org)上看到您的节点。
{{%/alert%}}

### 注册账户

您可以在[新钱包(https://dapp-v2.chainx.org)](https://dapp-v2.chainx.org)上注册账户, 并向该账户转入一点 PCX 作为交易手续费以及后续抵押等费用。

![add-account](/images/add-account.png)

## 注册节点

注册成功后，您可以在[`Network>Staking`](https://dapp-v2.chainx.org/#/staking)页面上注册节点。

![register-node](/images/register-node.png)

{{% alert  %}}
每个账号只能注册一次. 另外，注册之前您需要保证有足够余额支付交易手续费。新注册的节点默认参选，您无需进行额外的操作。除了注册节点时的初始质押币，您也可以通过**投票**的方式再次进行质押。选举时间结束后，总质押量排名前 30 的节点，将成为验证人参与共识。
{{%/alert%}}

![rebond](/images/bond.png)

## 设置 Session Keys

您可以在运行节点的机器上执行以下命令来生成 Session Keys:

```bash
$ curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "author_rotateKeys", "params":[]}' http://localhost:$YOUR_RPC_PORT
```

其中，`YOUR_RPC_PORT`为启动节点时`rpc-port`指定的端口， 未指定的情况下默认端口是 8086。

返回结果如下：

```json
{
  "jsonrpc": "2.0",
  "result": "0x42a7d53603bac173eb96e4ac133e35bcd4a49f308387a0e748b6f6a6dbf5635313f065a67a42a78a2c3e261a63523d92d4e03f9e7c9bba7c3d13b13b6983f0724c46b00699362a374f3fe43dd668eae6fcd815d0b84f88998ca5fc1c41e09b2412e2b9d3a322d9229a24cbce31d53358edc77b6fbaca7d038247743f40b6f205",
  "id": 1
}
```

其中，`result`字段即为获取的 Session Keys, 然后在[`Developer>Extrinsic`](https://dapp-v2.chainx.org/#/extrinsics)通过`setKeys`进行设置：

![setKeys](/images/setkeys.png)

{{%alert%}}
- 目前，`proof` 填入`0x00` 即可。
{{%/alert%}}

调用`nextKey`可以验证是否正确设置。

## 备份节点

由于当节点部署不当导致出块异常时， 会受到一定的惩罚。 所以可以部署额外的备份节点， 备份节点以`--pruning=archive`模式启动， 这样当主节点出现异常时， 可以用备份节点代替工作， 以免受到惩罚。

## 验证

当选验证人之后，如果在日志中看到`Prepared block for proposing at ...`, 即说明节点已成功出块。

```text
......
Nov 04 10:12:06.008  INFO 🙌 Starting consensus session on top of parent 0x6dd1e2edbf490ade94e944b09738c258921655708f6c2b5b8a63b5e38d02ac16
Nov 04 10:12:06.009  INFO 🎁 Prepared block for proposing at 4 [hash: 0x6740b08d96a329c9be13290760d15a537f3bd6635c85261b63e44395ad830b36; parent_hash: 0x6dd1…ac16; extrinsics (2): [0xe497…419a, 0x3af6…b467]]
Nov 04 10:12:06.012  INFO 🔖 Pre-sealed block for proposal at 4. Hash now 0x66f1579117b6aba16d4f57ae7ddf19ad209c8077a4f4f78ed4cb80877754a0f5, previously 0x6740b08d96a329c9be13290760d15a537f3bd6635c85261b63e44395ad830b36.
......
```

## 节点惩罚

ChainX 在每个 session 会发放奖励，同时惩罚可能作恶的节点，惩罚类型一般包括节点双签与节点离线。一旦节点被发现作恶，作恶节点在该 session 的应得奖励将会被全部惩罚进国库，同时按照链上汇报的作恶系数惩罚节点奖池, 即：

```text
penalty = max(session_reward + reward_pot_balance * F, minimum_penalty)
```

- `penalty`: 应罚金额
- `session_reward`: 节点的 session 奖励
- `reward_pot_balance`: 节点奖池金额
- `F`: 惩罚系数，由 babe 与 im-online 模块计算得出:
  - babe: [节点双签惩罚详情](https://wiki.polkadot.network/docs/en/learn-staking/#babe-equivocation), [frame/babe/src/equivocation.rs](https://github.com/paritytech/substrate/blob/c60f00840034017d4b7e6d20bd4fcf9a3f5b529a/frame/babe/src/equivocation.rs#L265).
  - im-online: [节点离线惩罚详情](https://wiki.polkadot.network/docs/en/learn-staking/#unresponsiveness)，[frame/im-online/src/lib.rs](https://github.com/paritytech/substrate/blob/c60f00840034017d4b7e6d20bd4fcf9a3f5b529a/frame/im-online/src/lib.rs#L771).
- `minimum_penalty`: 最小惩罚值, 即每次惩罚至少罚 `minimum_penalty`。

ChainX 节点作恶并不惩罚本金，而是惩罚节点奖池。当节点奖池被罚完后，节点会被强制退选。

```Rust
if penalty > reward_pot_balance {
    // force the validator(offender) to be chilled
}
```
