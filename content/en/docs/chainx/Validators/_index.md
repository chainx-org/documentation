---
title: "Validator guide"
linkTitle: "Validator guide"
weight: 4
date: 2022-04-20
description: ChainX validator guide
---

{{% pageinfo %}}
Validation Node Guide
{{% /pageinfo %}}

## 1. Preparations

### 1.1 A VPS

The easiest way is to use a cloud hosting, you are free to choose any hosting provider.

#### 1.1.1 Testnet hardware configuration

- CPU 2 cores, memory 2G, bandwidth 1M, operating system Ubuntu 20.04+.

#### 1.1.2 Mainnet hardware configuration

Taking Alibaba Cloud as an example, the recommended configuration for the ChainX mainnet is no less than: CPU 4 cores, memory 4G, bandwidth 10M, disk use SSD 300G+, operating system 20.04+.

### 1.2 Install the `chainx` program

#### 1.2.1 Compiling from source

Install related dependencies, take ubuntu 20.04 as an example
```bash
sudo apt update -y
sudo apt install --no-install-recommends -y \
     git curl ca-certificates \
     gcc g++ cmake clang
````

We assume you have Rust nightly and `wasm32-unknown-unknown` installed:

```bash
$ rustup install nightly-2021-11-07
$ rustup override set nightly-2021-11-07
$ rustup target add wasm32-unknown-unknown --toolchain nightly-2021-11-07
```

Next, you need to follow the steps below to complete the compilation:

```bash
$ git clone https://github.com/chainx-org/ChainX
$ cd ChainX
# switch to the latest tag
$ git checkout $(git describe --tags $(git rev-list --tags --max-count=1))
# Compile the binaries for the Release version
$ cargo build --release
```

After compilation, the `chainx` program will be in the `target/release/` directory.

#### 1.2.2 Download the compiled binary directly

Download the provided compiled binaries from the [GitHub release(https://github.com/chainx-org/ChainX/releases)](https://github.com/chainx-org/ChainX/releases) page.

### 1.3 Sync to the latest state of the chain
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

Start syncing the blockchain with the following command:

```bash
$ ./chainx-v3.0.0 --chain=mainnet --pruning=archive \
  --bootnodes="/ip4/52.77.243.26/tcp/23555/ws/p2p/12D3KooWQ6GGfmvmmmsbKRmZqMA3A8rxaHz25HvA7JNBbcZhLXtk" \
  --bootnodes="/ip4/120.26.57.227/tcp/36789/ws/p2p/12D3KooWEAX2BcQCZP79MuxQpqLQUop7P3tZY97eNxxUgc4ZTu3k"

# until block #3038400
$ ./chainx-v4.2.0 --chain=mainnet --pruning=archive 
```

After the synchronization is complete, restart the node in validator mode:

```bash
$ ./chainx --chain=mainnet --validator
````

Or start in validator mode directly to sync:

```bash
# When using --validator, the archive mode will be enabled by default, i.e. --pruning=archive
$ ./chainx --chain=mainnet --validator
````

However, note that you must wait for the synchronization to complete and set the Session Keys before letting the node participate in the election.
> If there is a synchronization exception, please ensure that the system time and network time are consistent, and then perform synchronization after deleting the database.

#### 1.3.1 Configuration file

For validator nodes, we recommend the following configuration:

```json
{
  "log-dir": "./log", // log directory
  "enable-console-log": false, // also output log to console
  "no-mdns": true,
  "validator": true, // validator node must be true, default is false
  "unsafe-ws-external": true, // The validator node recommends closing the external ws port
  "unsafe-rpc-external": true, // The validator node recommends closing the external rpc port
  "rpc-methods": "unsafe",
  "log": "info,runtime=info",
  "port": 20222, // specify the tcp port of the p2p protocol
  "ws-port": 8087, // Specify the RPC service port of websocket
  "rpc-port": 8086, // Specify the RPC service port of http
  "pruning": "archive", // It is strongly recommended to add this configuration to start in archive mode
  "execution": "NativeElseWasm",
  "db-cache": 2048, // Set the cache of the node database, the unit is MB, that is, 2GB here
  "state-cache-size": 2147483648, // Set the node state tree cache, unit B, which is 2GB here (2GB = 2 * 1024 * 1024)
  "name": "Your-Node-Name", // Node name displayed in Node Browser Telemetry
  "base-path": "data", // database path, the default under linux is `$HOME/.local/share/chainx/chains/$CHAIN_TYPE/db`
  "bootnodes": [
    "/ip4/120.26.57.227/tcp/36789/ws/p2p/12D3KooWEAX2BcQCZP79MuxQpqLQUop7P3tZY97eNxxUgc4ZTu3k",
    "/ip4/47.114.74.52/tcp/36789/ws/p2p/12D3KooWJPMUkGytfAMt3AMqm4AFn4VToXjbWZoC4Z2NxXNXvTwb"
  ] // Seed node, use built-in seed node when empty list
}
```

Some rpc services are sensitive operations. If they need to be exposed to the public network, it is recommended to use a proxy server for filtering (see: [https://github.com/paritytech/substrate/wiki/Public-RPC](https://github.com/paritytech/substrate/wiki/Public-RPC](https://github.com/paritytech/substrate/wiki/Public-RPC) com/paritytech/substrate/wiki/Public-RPC)).

If you know and understand the associated risks, you can include the `--rpc-methods unsafe` parameter when starting the node.
```txt
{
---snip---
  "unsafe-ws-external": true, // replace ws-external
  "unsafe-rpc-external": true, // replace rpc-external
  "rpc-methods": "unsafe",
---snip---
}
```

After your node has successfully started, you can see your node on [ChainX Telemetry](https://telemetry.chainx.org) or [Polkadot Telemetry](https://telemetry.polkadot.io/#list/ChainX).

#### 1.3.2 Using docker images

Put the above configuration file in the current directory, name it `config.json`, and remove the commented part. Run the following command:

```bash
$ cat ./config.json
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
````

Run the following command to start the node directly in the background (refer to the previous section to synchronize blocks from the genesis block)

```bash
docker pull chainxorg/chainx:v4.2.0
docker run -d --restart always --name chainx \
  -p 8086:8086 -p 8087:8087 -p 20222:20222 \
  -v $PWD/config.json:/config.json -v $PWD/data:/data \
  -v $PWD/log:/log -v $PWD/keystore:/keystore \
  chainxorg/chainx:v4.2.0 /usr/local/bin/chainx \
  --config /config.json
```

Among them, each parameter is the corresponding parameter in the configuration file, and the docker running in the background can pass:

```bash
$tail -f log/chainx.log # View all logs
````

When the start synchronization block appears in the log, it means that the node has started successfully.
```text
......
2021-04-02 03:05:43:700 INFO tokio-runtime-worker substrate ⚙️ Syncing, target=#1834748 (4 peers), best: #251 (0x4d58…0729), finalized #0 (0x012c…4810), ⬇ 175.1kiB/s ⬆ 11.6kiB/s
2021-04-02 03:05:48:700 INFO tokio-runtime-worker substrate ⚙️ Syncing 74.4 bps, target=#1834748 (7 peers), best: #623 (0xe3ce…db06), finalized #601 (0x78d1…b55e), ⬇ 54.0kiB/s ⬆ 6.0kiB/s
......
```

If you need to use rpc service externally, you need to add `ws-external: true` and `rpc-external: true` to the configuration file. Refer to [above](#config file) for other options.

When configuring, it is recommended to change the `name` item in the configuration file.

The port mapping must be consistent with that in `config.json`, otherwise rpc will not work properly.

### 1.4 Registering an Account

You can register an account on [ChainX Wallet(https://dapp.chainx.org/)](https://dapp.chainx.org/), and transfer a little PCX to the account as transaction fee and subsequent collateral and other expenses.
![add-account](/images/add-account.png)

## 2. Register the node

After successful registration, you can register a node on the [`Network>Staking(https://dapp.chainx.org/#/staking)`](https://dapp.chainx.org/#/staking) page.

![register-node](/images/register-node.png)

Each account can only be registered once. In addition, you need to ensure that you have enough balance to pay the transaction fee before registering. Newly registered nodes are elected by default, and you do not need to perform any additional operations. In addition to the initial pledge coins when registering a node, you can also pledge again by **voting**. After the election time, the top 40 nodes with total pledged amount will become validators to participate in the consensus.

![rebond](/images/bond.png)

## 3. Set Session Keys

You can generate Session Keys by executing the following command on the machine running the node:

```bash
$ curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "author_rotateKeys", "params":[]}' http://localhost:$YOUR_RPC_PORT
```

Among them, `YOUR_RPC_PORT` is the port specified by `rpc-port` when starting the node. If not specified, the default port is 8086.

The returned results are as follows:

```json
{
  "jsonrpc": "2.0",
  "result": "0x42a7d53603bac173eb96e4ac133e35bcd4a49f308387a0e748b6f6a6dbf5635313f065a67a42a78a2c3e261a63523d92d4e03f9e7c9bba7c3d13b13b6983f0724c46b00699362a374f3fe43dd668eae6fcd815d0b84f88998ca5fc1c41e09b2412e2b9d3a322d9229a24cbce31d53358edc77b6fbaca7d038247743f40b6f205",
  "id": 1
}
```

Among them, the `result` field is the obtained Session Keys, and then set it through `setKeys` in [`Developer>Extrinsic`](https://dapp.chainx.org/#/chainstate/extrinsics):

![setKeys](/images/setkeys.png)

- Currently, `proof` can be filled with `0x00`.

Call `nextKey` to verify that it is set correctly.

## 4. Backup node

Due to the abnormal block generation caused by improper node deployment, it will be punished to a certain extent. Therefore, additional backup nodes can be deployed, and the backup nodes are started in `--pruning=archive` mode, so that when the primary node fails, the backup node can be used instead of work to avoid being punished.

## 5. Verify block

After being elected as a validator, if you see `Prepared block for proposing at ...` in the log, it means that the node has successfully produced a block.

```text
......
Nov 04 10:12:06.008  INFO 🙌 Starting consensus session on top of parent 0x6dd1e2edbf490ade94e944b09738c258921655708f6c2b5b8a63b5e38d02ac16
Nov 04 10:12:06.009  INFO 🎁 Prepared block for proposing at 4 [hash: 0x6740b08d96a329c9be13290760d15a537f3bd6635c85261b63e44395ad830b36; parent_hash: 0x6dd1…ac16; extrinsics (2): [0xe497…419a, 0x3af6…b467]]
Nov 04 10:12:06.012  INFO 🔖 Pre-sealed block for proposal at 4. Hash now 0x66f1579117b6aba16d4f57ae7ddf19ad209c8077a4f4f78ed4cb80877754a0f5, previously 0x6740b08d96a329c9be13290760d15a537f3bd6635c85261b63e44395ad830b36.
......
```

## 6. Validation node withdrawal (deselected node has no income and does not participate in any staking activities)
### 6.1 Penalty to withdraw
When the verification node reward pool is punished to 0 or other penalties, the verification node is kicked out of the current verification node set and becomes an opt-out node.
If you want to re-participate in staking, you need to participate in the election manually.

### 6.2 Active withdrawal (if the verification node does not actively withdraw, it will be punished by the system)
Click `Drop` in the upper right corner
![drop1](/images/drop1.png)
![drop2](/images/drop2.png)

Or at [`Developer>Extrinsic`](https://dapp.chainx.org/#/chainstate/extrinsics)
Setup via `chill`
![drop3](/images/drop3.png)

### 6.3 Manual election
Click `Candidate` in the upper right corner
![candidate1](/images/candidate1.png)
![candidate2](/images/candidate2.png)

Or at [`Developer>Extrinsic`](https://dapp.chainx.org/#/chainstate/extrinsics)
Set via `validate`
![candidate3](/images/candidate3.png)

## 7. Precautions

- Before the first halving, each session will be about 5 minutes and a total of 50 PCX will be issued.
- A validator election is held every 12 sessions.
- If the node's self-mortgage is less than 1PCX or the total number of votes is less than 10PCX, it will be forced to withdraw from the election when the validator is elected.
- After ChainX v4.2.0, [validator nodes in candidate state are neither rewarded nor penalized](https://github.com/chainx-org/ChainX/pull/625)

## 8. Node Penalty

ChainX will issue rewards in each session and punish nodes that may do evil. The types of punishment generally include node double-signature and node offline. Once a node is found to be evil, all the rewards that the evil node deserves in the session will be punished into the treasury, and the node reward pool will be punished according to the evil coefficient reported on the chain, namely:
```text
penalty = max(session_reward + reward_pot_balance * F, minimum_penalty)
```

- `penalty`: penalty amount
- `session_reward`: node's session reward
- `reward_pot_balance`: Node reward pool amount
- `F`: Penalty coefficient, calculated by babe and im-online modules:
  - babe: [node double-sign penalty details](https://wiki.polkadot.network/docs/en/learn-staking/#babe-equivocation), [frame/babe/src/equivocation.rs](https:/ /github.com/paritytech/substrate/blob/c60f00840034017d4b7e6d20bd4fcf9a3f5b529a/frame/babe/src/equivocation.rs#L265).
  - im-online: [node offline penalty details](https://wiki.polkadot.network/docs/en/learn-staking/#unresponsiveness), [frame/im-online/src/lib.rs](https: //github.com/paritytech/substrate/blob/c60f00840034017d4b7e6d20bd4fcf9a3f5b529a/frame/im-online/src/lib.rs#L771).
- `minimum_penalty`: The minimum penalty value, that is, at least `minimum_penalty` for each penalty.

ChainX nodes do not punish the principal, but punish the node reward pool. When the node reward pool is exhausted, the node will be forced to withdraw from the election.

```Rust
if penalty > reward_pot_balance {
    // force the validator(offender) to be chilled
}
```
