---
title: "Validators"
linkTitle: "Validators"
weight: 4
date: 2017-01-05
description: >
  ChainX validator guide
---

{{% pageinfo %}}
This page is about running a validator node in ChainX network.
{{% /pageinfo %}}

## Hardware requirements

The most common way for a beginner to run a validator is on a cloud server running Linux. You may choose whatever VPS provider that your prefer, Ubuntu 18.04+ is recommended.

- OS: Ubuntu 18.04+
- CPU: 4+ Core
- Memory: 4+ GB
- Storage: SSD 300+ GB
- Bandwidth: 10+ M

## Install the `chainx` binary

### Build from source

First, you need to install Rust on your system:

```bash
$ rustup install nightly-2020-09-30
$ rustup override set nightly-2020-09-30
$ rustup target add wasm32-unknown-unknown --toolchain nightly-2020-09-30
```

Fetch the source code from GitHub and compile it locally:

```bash
$ git clone https://github.com/chainx-org/ChainX
$ cd ChainX
# Switch to the latest tag
$ git checkout $(git describe --tags $(git rev-list --tags --max-count=1))
# Compile the binary in release
$ cargo build --release
```

The binary is under `/target/release`.

### Download the prebuilt binary

You can also directly download from the prebuilt binary from [GitHub release(https://github.com/chainx-org/ChainX/releases)](https://github.com/chainx-org/ChainX/releases).

## Synchronize Chain Data

Run command below to synchronize chain data.

```bash
$ ./chainx --chain=mainnet --pruning=archive
```

When compeletion, you need to restart node with validator flag.

```bash
$ ./chainx --chain=mainnet --validator
```

or you can run above command directly, cause the validator node will run with `archive` by default.
IMPORTANT! You should wait node syncing completely and set the session key correctly before you as a candidate.

{{%alert%}}If error occurred when node syncing, you need to check if the system time consist of the internet time and remove the full database before you try again.{{%/alert%}}

## Configuration

The recommended config for validator node:

```json
{
  "log-dir": "./log", // log directory
  "enable-console-log": false, // disable print log to stdout
  "no-mdns": true,
  "validator": true, // For validator node, it must be set to 'true'.
  "ws-external": false, // ws port is only enabled on localhost
  "rpc-external": false, // rpc port is only enabled on localhost
  "log": "info,runtime=info",
  "port": 20222, // port that p2p uses
  "ws-port": 8087, // ws port
  "rpc-port": 8086, // http port
  "pruning": "archive", // Must be 'archive' for validator node.
  "execution": "NativeElseWasm",
  "db-cache": 2048, // database cache size in MB
  "state-cache-size": 2147483648, // state cache size in B
  "name": "Your-Node-Name", // The display name in telemetry
  "base-path": "data", // path to database, in linux `$HOME/.local/share/chainx/chains/$CHAIN_TYPE/db` by default.
  "bootnodes": [] // bootnodes' url, left empty to use built-in bootnodes.
}
```

{{%alert color="warning"%}} Some rpc is dangerous for validator node, if you want to expose, please use a proxy server to filter. Visit [https://github.com/paritytech/substrate/wiki/Public-RPC](https://github.com/paritytech/substrate/wiki/Public-RPC) for details. If you are aware of and understand the potential risks, you can enable it by add `--rpc-method unsafe` to starting arguments.{{%/alert%}}

{{%alert%}} When you node is setup correctly, you can see it at [ChainX Telemetry](https://telemetry.chainx.org) or [Polkadot Telemetry](https://telemetry.polkadot.io/#list/ChainX). {{%/alert%}}

## Use docker

Copy above configuration to `config.json`, and put it in current directory.

```bash
$cat ./config.json
{
  "log-dir": "/log",
  "enable-console-log": false,
  "no-mdns": true,
  "validator": true,
  "ws-external": false,
  "rpc-external": false,
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

{{%alert%}} You may want to change `name` field to something else you want to display in telemetry{{%/alert%}}

Run command below to start a validator node in background.

```bash
docker run -d --restart always --name chainx -p 8086:8086 -p 8087:8087 -p 20222:20222 -v $PWD/config.json:/config.json -v $PWD/data:/data -v $PWD/log:/log -v $PWD/keystore:/keystore chainxorg/chainx:v2.0.9 /usr/local/bin/chainx --config /config.json
```

You can fetch the log by

```bash
$ tail -f log/chainx.log
```

## Test your node

If some text like below occurs in your log file, means your node start successfully.

```text
......
2021-04-02 03:05:43:700 INFO tokio-runtime-worker substrate ⚙️ Syncing, target=#1834748 (4 peers), best: #251 (0x4d58…0729), finalized #0 (0x012c…4810), ⬇ 175.1kiB/s ⬆ 11.6kiB/s
2021-04-02 03:05:48:700 INFO tokio-runtime-worker substrate ⚙️ Syncing 74.4 bps, target=#1834748 (7 peers), best: #623 (0xe3ce…db06), finalized #601 (0x78d1…b55e), ⬇ 54.0kiB/s ⬆ 6.0kiB/s
......
```
