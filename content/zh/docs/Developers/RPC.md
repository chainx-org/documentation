---
title: "RPC"
date: 2020-07-13
weight: 1
description: >
  Docs about ChainX specific RPC APIs.
---

可以通过

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "rpc_methods",
  "params": []
}
```

获取所有可用的 RPC 方法，以 `x` 开头的为 ChainX 特有的 RPC 方法，其他 RPC 由 Substrate 框架原生提供。

## Assets

### `xassets_getAssets`

Parameters: `[]`

Request:

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "xassets_getAssets",
  "params": []
}
```

Response:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "1": {
      "balance": {
        "Locked": "0",
        "Reserved": "0",
        "ReservedDexSpot": "0",
        "ReservedWithdrawal": "0",
        "Usable": "23058037208"
      },
      "info": {
        "chain": "Bitcoin",
        "decimals": 8,
        "desc": "ChainX's Cross-chain Bitcoin",
        "token": "XBTC",
        "tokenName": "ChainX Bitcoin"
      },
      "isOnline": true,
      "restrictions": {
        "bits": 32
      }
    }
  },
  "id": 1
}
```

### `xassets_getAssetsByAccount`

Parameters: `[AccountId]`

Request:

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "xassets_getAssetsByAccount",
  "params": ["5RjfjwXjzJtVd6EiTCG3RsJmUM9h4FgocswJyAaLvuBicwE4"]
}
```

Response:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "1": {
      "Locked": "0",
      "Reserved": "0",
      "ReservedDexSpot": "0",
      "ReservedWithdrawal": "0",
      "Usable": "210558966"
    }
  },
  "id": 1
}
```

## Staking

### `xstaking_getValidators`

获取所有验证人。

Parameters: `[]`

Request:

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "xstaking_getValidators",
  "params": []
}
```

Response:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "account": "5RjfjwXjzJtVd6EiTCG3RsJmUM9h4FgocswJyAaLvuBicwE4", // 节点账户地址
      "isChilled": false, // 是否退选，false 表示没有退选
      "isValidating": true, // 是否正在参与出块
      "lastChilled": 0, // 节点上一次退选高度。如果为 null, 表示节点从未退选过。
      "lastTotalVoteWeight": "15052283706149193236", // 节点票龄上一次更新时累计的总票龄
      "lastTotalVoteWeightUpdate": 189717, // 节点总票龄上一次更新高度
      "referralId": "HashQuark", // 用于跨链挖矿的推荐人渠道ID
      "registeredAt": 0, // 节点注册高度
      "rewardPotAccount": "5Q7qY67db3n9wwXoEwmsjq3VkrSkMiHD26LaTrAYgFNTnpjL", // 节点奖池账户
      "rewardPotBalance": "698200755775", // 节点奖池余额
      "selfBonded": "2752054904694", // 节点自抵押金额
      "totalNomination": "26005907097205" // 节点总得票数
    }
  ],
  "id": 1
}
```

### `xstaking_getValidatorByAccount`

获取单个验证人信息。

Parameters: `[AccountId]`

Request:

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "xstaking_getValidatorByAccount",
  "params": ["5RjfjwXjzJtVd6EiTCG3RsJmUM9h4FgocswJyAaLvuBicwE4"]
}
```

Response:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "account": "5RjfjwXjzJtVd6EiTCG3RsJmUM9h4FgocswJyAaLvuBicwE4",
    "isChilled": false,
    "isValidating": true,
    "lastChilled": 0,
    "lastTotalVoteWeight": "15052283706149193236",
    "lastTotalVoteWeightUpdate": 189717,
    "referralId": "HashQuark",
    "registeredAt": 0,
    "rewardPotAccount": "5Q7qY67db3n9wwXoEwmsjq3VkrSkMiHD26LaTrAYgFNTnpjL",
    "rewardPotBalance": "698200755775",
    "selfBonded": "2752054904694",
    "totalNomination": "26005907097205"
  },
  "id": 1
}
```

### `xstaking_getDividendByAccount`

获取用户的投票分红。

Parameters: `[AccountId]`

Request:

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "xstaking_getDividendByAccount",
  "params": ["5RjfjwXjzJtVd6EiTCG3RsJmUM9h4FgocswJyAaLvuBicwE4"]
}
```

Response:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "5Pjajd12o9hVixBPRPHZEdjsrct3NZp9Ge7QP4PiSivQrBZa": "158905", // key 为该用户投票的验证人地址，value 为对应的Staking分红奖励。
    "5RjfjwXjzJtVd6EiTCG3RsJmUM9h4FgocswJyAaLvuBicwE4": "257865635289",
    "5VJPK9vpjfw2HjvgGdX56zuTi6Pr7vGvYbyN5CWEZyYaby7w": "42847"
  },
  "id": 1
}
```

### `xstaking_getNominationByAccount`

获取用户的投票信息, 包括投票金额，票龄等。

Parameters: `[AccountId]`

Request:

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "xstaking_getNominationByAccount",
  "params": ["5RjfjwXjzJtVd6EiTCG3RsJmUM9h4FgocswJyAaLvuBicwE4"]
}
```

Response:

```json
{
  "jsonrpc": "2.0",
  "result": {
    "5Pjajd12o9hVixBPRPHZEdjsrct3NZp9Ge7QP4PiSivQrBZa": {
      "lastVoteWeight": "2368000000000",
      "lastVoteWeightUpdate": 0,
      "nomination": "0",
      "unbondedChunks": []
    },
    "5RjfjwXjzJtVd6EiTCG3RsJmUM9h4FgocswJyAaLvuBicwE4": {
      "lastVoteWeight": "5039840899021026894",
      "lastVoteWeightUpdate": 0,
      "nomination": "2752054904694",
      "unbondedChunks": []
    },
    "5VJPK9vpjfw2HjvgGdX56zuTi6Pr7vGvYbyN5CWEZyYaby7w": {
      "lastVoteWeight": "450000000000",
      "lastVoteWeightUpdate": 0,
      "nomination": "0",
      "unbondedChunks": []
    }
  },
  "id": 1
}
```

## DEX

### `xspot_getTradingPairs`

获取所有交易对信息。

Parameters: `[]`

Request:

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "xspot_getTradingPairs",
  "params": []
}
```

Response:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "baseCurrency": 0, // PCX/BTC -> PCX
      "highestBid": "0", // 当前最高价
      "id": 0, // 交易对ID
      "lastUpdated": 0, // 最新成交对价更新高度
      "latestPrice": "100000", // 最新成交价
      "lowestAsk": "0", // 当前最低价
      "maxValidBid": "0", // 最高有效买入价
      "minValidAsk": "100", // 最低有效卖出价
      "pipDecimals": 9, // 交易对精度
      "quoteCurrency": 1, // PCX/BTC -> BTC
      "tickDecimals": 2, // 单跳精度
      "tradable": true // 交易对可正常交易
    }
  ],
  "id": 1
}
```

### `xspot_getOrdersByAccount`

获取用户订单列表。

Parameters: `[AccountId, page_index, page_size]`

Request:

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "xspot_getOrdersByAccount",
  "params": ["5RjfjwXjzJtVd6EiTCG3RsJmUM9h4FgocswJyAaLvuBicwE4", 0, 100]
}
```

Response:

### `xspot_getDepth`

获取交易对盘口附近深度。

Parameters: `[pair_id, depth_size]`

Request:

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "xspot_getDepth",
  "params": [0, 10]
}
```

Response:

## Mining Asset

### `xminingasset_getMiningAssets`

获取所有参与挖矿的跨链资产。

Parameters: `[]`

Request:

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "xminingasset_getMiningAssets",
  "params": []
}
```

Response:

```json
{
  "jsonrpc": "2.0",
  "result": [
    {
      "assetId": 1, // 跨链资产ID
      "lastTotalMiningWeight": "23491518002714020", // 上一次更新的总挖矿权重
      "lastTotalMiningWeightUpdate": 188250, // 上一次总挖矿权重更新高度
      "miningPower": 400, // 资产挖矿算力
      "rewardPot": "5RVjEbHJak6YQDwujo78pRH9V8WVoM3CYn3Ub7BhnMLs9Yx8", // 奖池地址
      "rewardPotBalance": "470655234171" // 奖池金额
    }
  ],
  "id": 1
}
```

### `xminingasset_getDividendByAccount`

获取跨链挖矿用户的挖矿利息。

Parameters: `[AccountId]`

Request:

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "xminingasset_getDividendByAccount",
  "params": ["5RjfjwXjzJtVd6EiTCG3RsJmUM9h4FgocswJyAaLvuBicwE4"]
}
```

Response:

```json
{
  "jsonrpc": "2.0",
  "result": {
    // key 为用户的跨链资产ID
    "1": {
      "insufficientStake": "0", // 跨链挖矿用户需要在staking中抵押相应资产才能提息，如果 insufficientStake 不为0，表示用户还需要在staking中抵押更多才能提息
      "other": "1742250653", // 用户跨链挖矿总利息的10%归推荐人，如果没有推荐人则归为财库
      "own": "15680255878" // 用户最终能领取的跨链挖矿利息
    }
  },
  "id": 1
}
```

### `xminingasset_getMinerLedgerByAccount`

获取跨链挖矿用户的挖矿记录。

Parameters: `[AccountId]`

Request:

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "xminingasset_getNominationByAccount",
  "params": ["5RjfjwXjzJtVd6EiTCG3RsJmUM9h4FgocswJyAaLvuBicwE4"]
}
```

Response:

```json
{
  "jsonrpc": "2.0",
  "result": {
    // key 为跨链资产ID
    "1": {
      "lastClaim": null, // 上一次提息高度，null 表示还从未提过息
      "lastMiningWeight": "831778601884312", // 上一次挖矿总票龄
      "lastMiningWeightUpdate": 0 // 上一次挖矿总票龄更新高度
    }
  },
  "id": 1
}
```
