---
title: "Trouble Shooting"
linkTitle: "Trouble Shooting"
weight: 8
date: 2022-04-20
description: >
---

## 1. How to unlock balances?

### 1.1 retrieve all locked balances

Call **balances.locks** by [`Developer>Extrinsic`](https://dapp.chainx.org/#/chainstate/extrinsics)

- (1) council election

```
[
  {
    id: pcx/phre,
    amount: 100,000,000,
    reasons: All
  }
]
```

- (2) xstaking mining

```
[
  {
    id: staking ,
    amount: 4.0000 PCX,
    reasons: All
  }
]
```

- (3) democracy referendum (ChainX)

```
[
  {
    id: democrac,
    amount: 3.0000 PCX,
    reasons: Misc
  }
]
```

### 1.2 unlock the locked balances
#### (1) council election (ChainX)

`lock info`： call elections.voting by [`Developer>Extrinsic`](https://dapp.chainx.org/#/chainstate/extrinsics)

`unlock method`： call elections.removeVoter by [`Developer>Extrinsic`](https://dapp.chainx.org/#/chainstate/extrinsics)

#### (2) democracy referendum (ChainX)

`lock info`：call democracy.votingOf by [`Developer>Extrinsic`](https://dapp.chainx.org/#/chainstate/extrinsics)

`unlock method`: 
- call democracy.removeVote by [`Developer>Extrinsic`](https://dapp.chainx.org/#/chainstate/extrinsics) 
- call democracy.unlock by [`Developer>Extrinsic`](https://dapp.chainx.org/#/chainstate/extrinsics)

#### (3) xstaking mining (ChainX)

`lock info`： 
- call xStaking.locks by [`Developer>Extrinsic`](https://dapp.chainx.org/#/chainstate/extrinsics)
- call xStaking.nominations by [`Developer>Extrinsic`](https://dapp.chainx.org/#/chainstate/extrinsics)

`unlock method`： 
- call xStaking.unlockUnbondedWithdrawal by [`Developer>Extrinsic`](https://dapp.chainx.org/#/chainstate/extrinsics)

note: 2022.04.07 xstaking bondingDuration(lock duration) is 14 days 
