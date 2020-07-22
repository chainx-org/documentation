---
title: "RPC"
date: 2020-07-13
weight: 1
description: >
  Docs about ChainX specific RPC APIs.
---

## Assets

## Staking

### `xstaking_getValidators`

Parameters: `[]`

Request:

```json
{
    "id":1,
    "jsonrpc":"2.0",
    "method":"xstaking_getValidators",
    "params":[]
}
```

Response:

```json
{
    "jsonrpc": "2.0",
    "result": [
        {
            "account": "5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY",
            "isChilled": false,
            "isValidating": true,
            "lastChilled": null,
            "lastTotalVoteWeight": "0",
            "lastTotalVoteWeightUpdate": 0,
            "registeredAt": 0,
            "rewardPotAccount": "5EJ3va1i3SYn3mTmKzoZmTjJgQJwqrT1kEuDhFwEcmbi7oE7",
            "rewardPotBalance": "28512000000",
            "selfBonded": "10000000000000",
            "total": "10000000000000"
        }
    ],
    "id": 1
}
```

### `xstaking_getValidatorByAccount`

Parameters: `[AccountId]`

Request:

```json
{
    "id":1,
    "jsonrpc":"2.0",
    "method":"xstaking_getValidatorByAccount",
    "params":["5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY"]
}
```

Response:

```json
{
    "jsonrpc": "2.0",
    "result": {
        "account": "5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY", // 节点账户地址
        "isChilled": false, // 是否退选，false 表示已退选
        "isValidating": true, // 是否正在参与出块
        "lastChilled": null, // 节点上一次退选高度。如果为 null, 表示节点从未退选过。
        "lastTotalVoteWeight": "0", // 节点上一次总票龄
        "lastTotalVoteWeightUpdate": 0, // 节点总票龄上一次更新高度
        "registeredAt": 0, // 节点注册高度
        "rewardPotAccount": "5EJ3va1i3SYn3mTmKzoZmTjJgQJwqrT1kEuDhFwEcmbi7oE7", // 奖池地址
        "rewardPotBalance": "17107200000", // 奖池金额
        "selfBonded": "10000000000000", // 自抵押数
        "total": "10000000000000" // 总得票数
    },
    "id": 1
}
```

## DEX
