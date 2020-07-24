---
title: "JS-SDK"
date: 2020-07-13
weight: 2
description: >
  A short lead descripton about this content page. It can be **bold** or _italic_ and can be split over multiple paragraphs.
---

{{% pageinfo %}}
This is a placeholder page. Replace it with your own content.
{{% /pageinfo %}}

## 安装
npm install @chainx-v2/api

## 快速开始
```js
const { ChainX } = require('@chainx-v2/api');

async function main () {
  // Initialise the chainx to connect to the local node
  const chainx = new ChainX('ws://47.114.131.193:9000');

  // Create the API and wait until ready
  await chainx.ready();
  const api = chainx.getApi();
  
  // Retrieve the chain & node information information via rpc calls
  const [chain, nodeName, nodeVersion] = await Promise.all([
    api.rpc.system.chain(),
    api.rpc.system.name(),
    api.rpc.system.version()
  ]);
  console.log(`You are connected to chain ${chain} using ${nodeName} v${nodeVersion}`);
}
```

## chainx.js

## 账户模块
```js
const { Account } = require('@chainx-v2/account');

const account1 = Account.generate();

const publicKey1 = account1.publicKey(); // 公钥
console.log('publicKey1: ', publicKey1);

const privateKey1 = account1.privateKey(); // 私钥
console.log('privateKey1: ', privateKey1);

const address1 = account1.address(); // 地址
console.log('address1: ', address1);

const mnemonic = Account.newMnemonic(); // 随机助记词
console.log('mnemonic: ', mnemonic);

const account2 = Account.from(mnemonic); // 从助记词生成账户

const address2 = Account.encodeAddress(account2.publicKey()); // 从公钥生成地址
console.log('address2: ', address2);

const publicKey2 = Account.decodeAddress(address2); // 从地址获取生成公钥
console.log('publicKey2: ', publicKey2);

Account.setNet('testnet'); // 设置为测试网
const address3 = Account.encodeAddress(publicKey2); // 测试网地址
console.log('address3:', address3);

Account.setNet('mainnet'); // 设置为主网
const address4 = Account.encodeAddress(publicKey2); // 主网地址
console.log('address4:', address4);

const account3 = Account.from(privateKey1); // 从私钥生成账户
console.log('address:', account3.address()); // 地址
```

## 交易接口

## 智能合约
