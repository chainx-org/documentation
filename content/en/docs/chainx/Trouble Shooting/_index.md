---
title: "Trouble shooting"
linkTitle: "Trouble shooting"
weight: 7
date: 2022-04-20
description: >
---

## 1. How to unlock balances?

### 1.1 Query account balance locks

Call balances.locks via [`Developer>Extrinsic`](https://dapp.chainx.org/#/chainstate/extrinsics)
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

- (3) democracy公投(ChainX)

```
[
  {
    id: democrac,
    amount: 3.0000 PCX,
    reasons: Misc
  }
]
```

### 1.2 Lock details and unlock methods of each module
- (1) council election (ChainX)

**Query lock details: call elections.voting**

**unlock: call elections.removeVoter(), cancel the vote and unlock**

- (2) democracy referendum (ChainX)

**Query lock details: call democracy.votingOf**

**unlock: Call democracy.removeVote() to cancel the referendum vote, then call democracy.**

**unlock() to unlock**

- (3) xstaking mining (ChainX)

**Query lock details: call xStaking.locks and xStaking.nominations**

**unlock: call xStaking.unlockUnbondedWithdrawal() to unlock**

Note: 2022.04.07 xstaking will be unlocked and locked for two weeks
