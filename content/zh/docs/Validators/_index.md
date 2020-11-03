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

### 一台 VPS

最简单的方式是使用一台云主机， 您可以自由选择任一主机提供商。

#### 测试网硬件配置

- CPU 2 核，内存 2G, 带宽 1M。

### 安装`chainx`程序

#### 从源码编译

我们假设您已经安装好 Rust nightly 与 `wasm32-unknown-unknown`:

```bash
$ rustup install nightly-2020-09-30
$ rustup override set nightly-2020-09-30
$ rustup target add wasm32-unknown-unknown --toolchain nightly-2020-09-30
```

接下来， 您需要按照以下步骤完成编译工作：

```bash
$ git clone https://github.com/chainx-org/ChainX
$ cd ChainX
$ git checkout develop-2.0
$ cargo build --release
```

编译完成后，`chainx`程序将在`target/release/`目录下。

#### 直接下载编译好的二进制

从 [GitHub release(https://github.com/chainx-org/ChainX/releases)](https://github.com/chainx-org/ChainX/releases) 页面下载提供编译好的二进制。

### 同步至链的最新状态

通过下面的命令开始同步区块链:

```bash
$ ./chainx --chain=testnet --pruning=archive
```

同步完成后，以验证人模式重启节点:

```bash
$ ./chainx --chain=testnet --validator
```

或直接以验证人模式启动进行同步：

```bash
# 使用 --validator 时，同时将默认启用存档模式即 --pruning=archive
$ ./chainx --chain=testnet --validator
```

不过注意，一定等待同步完成并且设置好 Session Keys 后再让节点参选。
{{%alert%}}如果出现同步异常， 请确保系统时间和网络时间一致， 删除数据库之后再进行同步。 {{%/alert%}}

#### 配置文件

对于验证者节点， 我们建议如下配置：
```javascript
{
  "validator": true, //  验证者节点必须为 true, 默认为false
  "rpc-external": false, // 验证者节点建议关闭对外的rpc端口
  "ws-external": false, // 验证者节点建议关闭对外的ws端口
  "log": "info,runtime=info",
  "port": 20222, //指定p2p协议的tcp端口
  "ws-port": 8087, //指定websocket的RPC服务端口
  "rpc-port": 8086, //指定http的RPC服务端口
  "other-execution": "NativeElseWasm",
  "syncing-execution": "NativeElseWasm",
  "block-construction-execution": "NativeElseWasm",
  "pruning": "archive",  // 目前强烈建议加上该配置，以存档模式启动
  "db-cache": 1024,  // 设置节点数据库的缓存，单位MB，即这里为1GB
  "state-cache-size": 2147483648, // 设置节点状态树缓存，单位B，即这里为2GB (2GB = 2 * 1024 * 1024)
  "no-mdns": true, 
  "bootnodes": [], //种子节点， 为空列表时使用内置的种子节点
  "name": "Your-Node-Name",             // 在节点浏览器Telemetry中显示的节点名
  "base-path": "<数据存放路径>",  //数据库路径， linux下默认为`$HOME/.local/share/chainx/chains/$CHAIN_TYPE/db`
}
```

{{%alert color="warning"%}}部分rpc服务属于敏感操作，如需暴露于公网，建议使用代理服务器进行过滤（详见：[https://github.com/paritytech/substrate/wiki/Public-RPC](https://github.com/paritytech/substrate/wiki/Public-RPC)）。如果您已知悉并了解相关风险，可在启动节点时加入`--unsafe-{ws,rpc}-external`参数{{%/alert%}}

{{%alert %}}
节点成功启动后， 可以在[Telemetry(stat.chainx.org)](stat.chainx.org)上看到您的节点。
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

其中，`YOUR_RPC_PORT`为启动节点时`rpc-port`指定的端口， 未指定的情况下默认端口是8086。

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
目前，`proof` 填入`0x00` 即可。
{{%/alert%}}


## 备份节点

由于当节点部署不当导致出块异常时， 会受到一定的惩罚。 所以可以部署额外的备份节点， 备份节点以`--pruning=archive`模式启动， 这样当主节点出现异常时， 可以用备份节点代替工作， 以免受到惩罚。

## 验证

当选验证人之后，如果在日志中看到`Prepared block for proposing at 6467`, 即说明节点已成功出块。

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
