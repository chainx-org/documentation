---
title: "验证节点指南"
linkTitle: "验证节点指南"
weight: 4
date: 2022-04-20
description: ChainX validator guide
---

{{% pageinfo %}}
验证节点指南
{{% /pageinfo %}}

## 1. 准备事项

### 1.1 一台 VPS

最简单的方式是使用一台云主机， 您可以自由选择任一主机提供商。

#### 1.1.1 测试网硬件配置

- CPU 2 核，内存 2G, 带宽 1M, 操作系统 Ubuntu 20.04+。

#### 1.1.2 主网硬件配置

以阿里云为例，ChainX 主网推荐配置不低于: CPU 4 核, 内存 4 G, 带宽 10M，磁盘使用 SSD 300G+, 操作系统 20.04+.

### 1.2 安装`chainx`程序

#### 1.2.1 从源码编译

安装相关依赖库, 以ubuntu 20.04为例
```bash
sudo apt update -y
sudo apt install --no-install-recommends -y \
     git curl ca-certificates \
     gcc g++ cmake clang
```

我们假设您已经安装好 Rust nightly 与 `wasm32-unknown-unknown`:

```bash
$ rustup install nightly-2021-11-07
$ rustup override set nightly-2021-11-07
$ rustup target add wasm32-unknown-unknown --toolchain nightly-2021-11-07
```

接下来， 您需要按照以下步骤完成编译工作：

```bash
$ git clone https://github.com/chainx-org/ChainX
$ cd ChainX
# 切到最新的 tag
$ git checkout $(git describe --tags $(git rev-list --tags --max-count=1))
# 编译 Release 版本的二进制
$ cargo build --release
```

编译完成后，`chainx`程序将在`target/release/`目录下。

#### 1.2.2 直接下载编译好的二进制

从 [GitHub release(https://github.com/chainx-org/ChainX/releases)](https://github.com/chainx-org/ChainX/releases) 页面下载提供编译好的二进制。

### 1.3 同步至链的最新状态
>
>#### `How to sync blocks from genesis(block #0)`
>- (0)  You should know [Debug: panicked at 'Storage root must match that calculated ' #609](https://github.com/chainx-org/ChainX/issues/609)
   >  **if  you use ChainX v4.x.x directly sync blocks will be stuck at #881910** or other block.
>- (1)  Compile [ChainX v3.0.0](https://github.com/chainx-org/ChainX/tree/v3.0.0) by `nightly-2020-09-30` or Download  [chainx-v3.0.0-ubuntu20.04-x86_64-unknown-linux-gnu-1](https://github.com/chainx-org/ChainX/releases/download/v3.0.0/chainx-v3.0.0-ubuntu20.04-x86_64-unknown-linux-gnu-1)
   >  the ChainX v3.0.0 seed nodes are bad, so you should use new mainnet bootnodes with `--bootnodes`
>```
>"/ip4/52.77.243.26/tcp/23555/ws/p2p/12D3KooWQ6GGfmvmmmsbKRmZqMA3A8rxaHz25HvA7JNBbcZhLXtk"
>"/ip4/120.26.57.227/tcp/36789/ws/p2p/12D3KooWEAX2BcQCZP79MuxQpqLQUop7P3tZY97eNxxUgc4ZTu3k"
>"/ip4/47.114.74.52/tcp/36789/ws/p2p/12D3KooWJPMUkGytfAMt3AMqm4AFn4VToXjbWZoC4Z2NxXNXvTwb"
>```
>- (2)  Until **#3038400**, please use ChainX v3.0.0 to synchronize with `NativeElseWasm (default mode)`
>- (3)  For blocks after **#3038400**, complete (2) first, and then replace ChainX v3.0.0 with ChainX v4.x.x to complete the db migration (note that the migration process is irreversible, it is recommended to back up the data first)
>- (4)  ChainX v4.x.x continues to synchronize blocks
>

通过下面的命令开始同步区块链:

```bash
$ ./chainx-v3.0.0 --chain=mainnet --pruning=archive \
  --bootnodes="/ip4/52.77.243.26/tcp/23555/ws/p2p/12D3KooWQ6GGfmvmmmsbKRmZqMA3A8rxaHz25HvA7JNBbcZhLXtk" \
  --bootnodes="/ip4/120.26.57.227/tcp/36789/ws/p2p/12D3KooWEAX2BcQCZP79MuxQpqLQUop7P3tZY97eNxxUgc4ZTu3k"

# until block #3038400
$ ./chainx-v4.2.0 --chain=mainnet --pruning=archive 
```

同步完成后，以验证人模式重启节点:

```bash
$ ./chainx --chain=mainnet --validator
```

或直接以验证人模式启动进行同步：

```bash
# 使用 --validator 时，同时将默认启用存档模式即 --pruning=archive
$ ./chainx --chain=mainnet --validator
```

不过注意，一定等待同步完成并且设置好 Session Keys 后再让节点参选。
> 如果出现同步异常， 请确保系统时间和网络时间一致， 删除数据库之后再进行同步。

#### 1.3.1 配置文件

对于验证者节点， 我们建议如下配置：

```json
{
  "log-dir": "./log", // 日志目录
  "enable-console-log": false, // 同时将日志输出到控制台
  "no-mdns": true,
  "validator": true, // 验证者节点必须为 true, 默认为false
  "unsafe-ws-external": true, // 验证者节点建议关闭对外的ws端口
  "unsafe-rpc-external": true, // 验证者节点建议关闭对外的rpc端口
  "rpc-methods": "unsafe",
  "log": "info,runtime=info",
  "port": 20222, // 指定p2p协议的tcp端口
  "ws-port": 8087, // 指定websocket的RPC服务端口
  "rpc-port": 8086, // 指定http的RPC服务端口
  "pruning": "archive", // 强烈建议加上该配置，以存档模式启动
  "execution": "NativeElseWasm",
  "db-cache": 2048, // 设置节点数据库的缓存，单位MB，即这里为2GB
  "state-cache-size": 2147483648, // 设置节点状态树缓存，单位B，即这里为2GB (2GB = 2 * 1024 * 1024)
  "name": "Your-Node-Name", // 在节点浏览器Telemetry中显示的节点名
  "base-path": "data", // 数据库路径， linux下默认为`$HOME/.local/share/chainx/chains/$CHAIN_TYPE/db`
  "bootnodes": [
    "/ip4/120.26.57.227/tcp/36789/ws/p2p/12D3KooWEAX2BcQCZP79MuxQpqLQUop7P3tZY97eNxxUgc4ZTu3k",
    "/ip4/47.114.74.52/tcp/36789/ws/p2p/12D3KooWJPMUkGytfAMt3AMqm4AFn4VToXjbWZoC4Z2NxXNXvTwb"
  ] // 种子节点， 为空列表时使用内置的种子节点
}
```

部分 rpc 服务属于敏感操作，如需暴露于公网，建议使用代理服务器进行过滤 (详见：[https://github.com/paritytech/substrate/wiki/Public-RPC](https://github.com/paritytech/substrate/wiki/Public-RPC)).

如果您已知悉并了解相关风险，可在启动节点时加入`--rpc-methods unsafe`参数.
```txt
{
---snip---
  "unsafe-ws-external": true, // replace ws-external
  "unsafe-rpc-external": true, // replace rpc-external
  "rpc-methods": "unsafe",
---snip---
}
```

节点成功启动后， 可以在[ChainX Telemetry](https://telemetry.chainx.org)或者 [Polkadot Telemetry](https://telemetry.polkadot.io/#list/ChainX) 上看到您的节点。

#### 1.3.2 使用 docker 镜像

将上述配置文件放在当前目录下， 命名为`config.json`, 去掉注释的部分。 运行如下命令：

```bash
$cat ./config.json
{
  "log-dir": "/log",
  "enable-console-log": false,
  "no-mdns": true,
  "validator": true,
  "unsafe-ws-external": true,
  "unsafe-rpc-external": true,
  "rpc-methods": "unsafe",
  "log": "info,runtime=info",
  "port": 20222,
  "ws-port": 8087,
  "rpc-port": 8086,
  "pruning": "archive",
  "execution": "NativeElseWasm",
  "db-cache": 2048,
  "state-cache-size": 2147483648,
  "name": "Your-Node-Name",
  "base-path": "/data",
  "bootnodes": []
}
```

运行以下命令，可以直接后台启动节点(从genesis block同步区块参考上一节)

```bash
docker pull chainxorg/chainx:v4.2.0
docker run -d --restart always --name chainx \
  -p 8086:8086 -p 8087:8087 -p 20222:20222 \
  -v $PWD/config.json:/config.json -v $PWD/data:/data \
  -v $PWD/log:/log -v $PWD/keystore:/keystore \
  chainxorg/chainx:v4.2.0 /usr/local/bin/chainx \
  --config /config.json
```

其中，各参数为配置文件中对应参数, 后台运行的 docker 可以通过:

```bash
$tail -f log/chainx.log # 查看全部日志
```

当日志出现开始同步块的时候， 即说明节点成功启动。

```text
......
2021-04-02 03:05:43:700 INFO tokio-runtime-worker substrate ⚙️ Syncing, target=#1834748 (4 peers), best: #251 (0x4d58…0729), finalized #0 (0x012c…4810), ⬇ 175.1kiB/s ⬆ 11.6kiB/s
2021-04-02 03:05:48:700 INFO tokio-runtime-worker substrate ⚙️ Syncing 74.4 bps, target=#1834748 (7 peers), best: #623 (0xe3ce…db06), finalized #601 (0x78d1…b55e), ⬇ 54.0kiB/s ⬆ 6.0kiB/s
......
```

如果需要在外部使用 rpc 服务, 需要在配置文件中加入`ws-external: true`和`rpc-external: true`。其他选项参考[上文](#配置文件)。

在配置时，建议更改配置文件中的`name`项。

端口的映射必须与`config.json`中保持一致，否则将无法正常使用 rpc。

### 1.4 注册账户

您可以在[ChainX钱包(https://dapp.chainx.org/)](https://dapp.chainx.org/) 上注册账户, 并向该账户转入一点 PCX 作为交易手续费以及后续抵押等费用。

![add-account](/images/add-account.png)

## 2. 注册节点

注册成功后，您可以在[`Network>Staking(https://dapp.chainx.org/#/staking)`](https://dapp.chainx.org/#/staking) 页面上注册节点。

![register-node](/images/register-node.png)

每个账号只能注册一次. 另外，注册之前您需要保证有足够余额支付交易手续费。新注册的节点默认参选，您无需进行额外的操作。除了注册节点时的初始质押币，您也可以通过**投票**的方式再次进行质押。选举时间结束后，总质押量排名前 40 的节点，将成为验证人参与共识。

![rebond](/images/bond.png)

## 3. 设置 Session Keys

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

其中，`result`字段即为获取的 Session Keys, 然后在[`Developer>Extrinsic`](https://dapp.chainx.org/#/chainstate/extrinsics) 通过`setKeys`进行设置：

![setKeys](/images/setkeys.png)

- 目前，`proof` 填入`0x00` 即可。

调用`nextKey`可以验证是否正确设置。

## 4. 备份节点

由于当节点部署不当导致出块异常时， 会受到一定的惩罚。 所以可以部署额外的备份节点， 备份节点以`--pruning=archive`模式启动， 这样当主节点出现异常时， 可以用备份节点代替工作， 以免受到惩罚。

## 5. 验证出块

当选验证人之后，如果在日志中看到`Prepared block for proposing at ...`, 即说明节点已成功出块。

```text
......
Nov 04 10:12:06.008  INFO 🙌 Starting consensus session on top of parent 0x6dd1e2edbf490ade94e944b09738c258921655708f6c2b5b8a63b5e38d02ac16
Nov 04 10:12:06.009  INFO 🎁 Prepared block for proposing at 4 [hash: 0x6740b08d96a329c9be13290760d15a537f3bd6635c85261b63e44395ad830b36; parent_hash: 0x6dd1…ac16; extrinsics (2): [0xe497…419a, 0x3af6…b467]]
Nov 04 10:12:06.012  INFO 🔖 Pre-sealed block for proposal at 4. Hash now 0x66f1579117b6aba16d4f57ae7ddf19ad209c8077a4f4f78ed4cb80877754a0f5, previously 0x6740b08d96a329c9be13290760d15a537f3bd6635c85261b63e44395ad830b36.
......
```

## 6. 验证节点退选（退选节点无收益，不参与任何staking活动）
### 6.1 被罚退选
当验证节点奖池被惩罚为0或其他惩罚, 验证节点被踢出当前验证节点集合, 成为退选节点。
若想重新参与staking，则需要手动参选。

### 6.2 主动退选(如果验证节点不主动退选,会被系统惩罚)
点击右上角 `退选(Drop)`
![drop1](/images/drop1.png)
![drop2](/images/drop2.png)

或者在[`Developer>Extrinsic`](https://dapp.chainx.org/#/chainstate/extrinsics)
通过`chill`进行设置
![drop3](/images/drop3.png)

### 6.3 手动参选
点击右上角 `参选(Candidate)`
![candidate1](/images/candidate1.png)
![candidate2](/images/candidate2.png)

或者在[`Developer>Extrinsic`](https://dapp.chainx.org/#/chainstate/extrinsics)
通过`validate`进行设置
![candidate3](/images/candidate3.png)

## 7. 注意事项

- 第一次减半前，每个 session 约 5 分钟，共发行 50 PCX。
- 每 12 个 session 进行一次验证人选举换届。
- 如果节点自抵押小于 1PCX 或总得票数小于 10PCX, 在选举验证人时将会被强制退选。
- ChainX v4.2.0之后, [候选状态的验证节点既不被奖励也不被惩罚](https://github.com/chainx-org/ChainX/pull/625)

## 8. 节点惩罚

ChainX 在每个 session 会发放奖励，同时惩罚可能作恶的节点，惩罚类型一般包括节点双签与节点离线。一旦节点被发现作恶，作恶节点在该 session 的应得奖励将会被全部惩罚进国库，同时按照链上汇报的作恶系数惩罚节点奖池, 即：

```text
penalty = max(session_reward + reward_pot_balance * F, minimum_penalty)
```

- `penalty`: 应罚金额
- `session_reward`: 节点的 session 奖励
- `reward_pot_balance`: 节点奖池金额
- `F`: 惩罚系数，由 babe 与 im-online 模块计算得出:
    - babe: [节点双签惩罚详情](https://wiki.polkadot.network/docs/en/learn-staking/#babe-equivocation), [frame/babe/src/equivocation.rs](https://github.com/paritytech/substrate/blob/c60f00840034017d4b7e6d20bd4fcf9a3f5b529a/frame/babe/src/equivocation.rs#L265).
    - im-online: [节点离线惩罚详情](https://wiki.polkadot.network/docs/en/learn-staking/#unresponsiveness), [frame/im-online/src/lib.rs](https://github.com/paritytech/substrate/blob/c60f00840034017d4b7e6d20bd4fcf9a3f5b529a/frame/im-online/src/lib.rs#L771).
- `minimum_penalty`: 最小惩罚值, 即每次惩罚至少罚 `minimum_penalty`。

ChainX 节点作恶并不惩罚本金，而是惩罚节点奖池。当节点奖池被罚完后，节点会被强制退选。

```Rust
if penalty > reward_pot_balance {
// force the validator(offender) to be chilled
}
```