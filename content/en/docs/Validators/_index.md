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

### Download the prebuilt binary

You can also directly download from the prebuilt binary from [GitHub release(https://github.com/chainx-org/ChainX/releases)](https://github.com/chainx-org/ChainX/releases).

## Synchronize Chain Data

```bash
$ ./chainx --chain=mainnet --pruning=archive
```
