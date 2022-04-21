---
title: "常见问题"
linkTitle: "常见问题"
weight: 8
date: 2022-04-20
description: >
---

## 1. How to unlock balances?

### 1.1 查询账户余额的locks

通过 [`Developer>Extrinsic`](https://dapp.chainx.org/#/chainstate/extrinsics) 调用 balances.locks

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

### 1.2 各个模块的lock详情和unlock方法
- (1) council election (ChainX)

**查询lock详情：调用 elections.voting**

**unlock： 调用elections.removeVoter(), 取消投票并解锁**

- (2) democracy 公投 (ChainX)

**查询lock详情：调用 democracy.votingOf**

**unlock:  调用democracy.removeVote()取消公投投票，再调用democracy.**

**unlock()解锁**

- (3) xstaking mining (ChainX)

**查询lock详情： 调用 xStaking.locks 和 xStaking.nominations**

**unlock： 调用xStaking.unlockUnbondedWithdrawal()解锁**

备注: 2022.04.07 xstaking 解绑锁定两周
