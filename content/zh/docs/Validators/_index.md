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
