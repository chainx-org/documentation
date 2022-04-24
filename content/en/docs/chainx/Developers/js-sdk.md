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

## Install
npm install @chainx-v2/api

## quick start
```javascript
const { ChainX } = require('@chainx-v2/api');

async function main () {
  // Initialise the chainx to connect to the local node
  const chainx = new ChainX('ws://47.114.131.193:9000');

  // Create the API and wait until ready
  await chainx.ready();
  const api = chainx.getApi();
  
  //get assets
  const assets = await api.rpc.xassets.getAssets();
  console.log("balance:" + assets);
  
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

## Account module
```javascript
const { Account } = require('@chainx-v2/account');

const account1 = Account.generate();

const publicKey1 = account1.publicKey(); // public key
console.log('publicKey1: ', publicKey1);

const privateKey1 = account1.privateKey(); // private key
console.log('privateKey1: ', privateKey1);

const address1 = account1.address(); // address
console.log('address1: ', address1);

const mnemonic = Account.newMnemonic(); // random mnemonic
console.log('mnemonic: ', mnemonic);

const account2 = Account.from(mnemonic); // Generate Account from Mnemonic Phrase

const address2 = Account.encodeAddress(account2.publicKey()); // Generate address from public key
console.log('address2: ', address2);

const publicKey2 = Account.decodeAddress(address2); // Get the generated public key from the address
console.log('publicKey2: ', publicKey2);

Account.setNet('testnet'); // Set up as testnet
const address3 = Account.encodeAddress(publicKey2); // Testnet address
console.log('address3:', address3);

Account.setNet('mainnet'); // set as mainnet
const address4 = Account.encodeAddress(publicKey2); // Mainnet address
console.log('address4:', address4);

const account3 = Account.from(privateKey1); // Generate account from private key
console.log('address:', account3.address()); // address
```
## RPC

```javascript
  //get assets
  const assets = await api.rpc.xassets.getAssets();
  console.log("balance:" + assets);
```

## Transaction interface

```javascript
const ChainX = require('@chainx-v2/api');

(async () => {

  // Currently only supports websocket connections
  const chainx = new ChainX('ws://47.114.131.193:9000');

  // Awaiting async initialization
  await chainx.ready();
  
  const api = chainx.getApi();


  // Construct transaction parameters (synchronized)

  const extrinsic = api.tx.balances.transfer('5DtoAAhWgWSthkcj7JfDcF2fGKEWg91QmgMx37D6tFBAc6Qg', 12345);

  // View method hash
  console.log('Function: ', extrinsic.method.toHex());
  
  const alice = '5CtoAAhWgWSthkcj7JfDcF2fGKEWg91QmgMx37D6tFBAc6Qg';

  // Sign and send the transaction，0x0000000000000000000000000000000000000000000000000000000000000000 is the private key used for signing
  const hash =  await transfer.signAndSend(alice);
})();

```

## smart contract
