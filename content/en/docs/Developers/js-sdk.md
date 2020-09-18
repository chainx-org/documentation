<!-- TOC -->

- [Getting Started](#getting-started)
  - [Installation](#installation)
  - [Basics](#basics)
    - [Create an Instance](#create-an-instance)
    - [Simple Connect](#simple-connect)
    - [Account Module](#account-module)
    - [Balances](#account-balances)
    
<!-- /TOC -->

# Getting Started
These sections would provide you the information needed to install the `@chainx-v2/api` package, understand the structures, and start using it. It is not a line-by-line documentation of all existing function calls.

## Installation
Install the API via

```bash
yarn add @polkadot/api @chainx-v2/api
```

## Basics
For general `polkadot-js` interactions with the blockchain, e.g. state queries, PRC calls, keyring etc., please refer to the [`polkadot-js/api documentation`](https://polkadot.js.org/api/start/install.html) for more details. The `acala-network/api` provides types and other interactions specific to the Acala Network.

### Create an Instance

```js
const  { ApiPromise } = require('@polkadot/api');
const { WsProvider } = require('@polkadot/rpc-provider');
const { options } =  require('@chainx-v2/api');

async function main() {
    const wsProvider = new WsProvider('wss://staging-1.chainx.org/ws');
    const api =  await ApiPromise.create(options({ provider: wsProvider }));
    await api.isReady;
    // use api
}

main()
```

### Simple Connect 

```js
const  { ApiPromise } = require('@polkadot/api');
const { WsProvider } = require('@polkadot/rpc-provider');
const { options } =  require('@chainx-v2/api');

async function main() {
    const wsProvider = new WsProvider('wss://staging-1.chainx.org/ws');
    const api =  await ApiPromise.create(options({ provider: wsProvider }));
    await api.isReady;
    // use api
    
    const [chain, nodeName, nodeVersion] = await Promise.all([
       api.rpc.system.chain(),
       api.rpc.system.name(),
       api.rpc.sytem.version()
    ])
    console.log(`You are connected to chain ${chain} using ${nodeName} v${nodeVersion} `)
}
main()

```
### Account Balances
ChainX NetWork supports multi-currencies. Network native token (PCX) can be queried via system balance.

```js
const  { ApiPromise } = require('@polkadot/api');
const { WsProvider } = require('@polkadot/rpc-provider');
const { options } =  require('@chainx-v2/api');
async function main() {
    const wsProvider = new WsProvider('wss://staging-1.chainx.org/ws');
    const api =  await ApiPromise.create(options({ provider: wsProvider }));
    await api.isReady;
    // use api
    const Alice = '5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY';
    // Retrieve the initial balance. Since the call has no callback, it is simply a promise
    // that resolves to the current on-chain value
    let { data: { free: previousFree }, nonce: previousNonce } = await api.query.system.account(Alice);
    console.log(`${Alice} has a balance of ${previousFree}, nonce ${previousNonce}`);
    console.log(`You may leave this example running and start example 06 or transfer any value to ${Alice}`);
    // Here we subscribe to any balance changes and update the on-screen value
    api.query.system.account(Alice, ({ data: { free: currentFree }, nonce: currentNonce }) => {
        // Calculate the delta
        const change = currentFree.sub(previousFree);
        // Only display positive value changes (Since we are pulling `previous` above already,
        // the initial balance change will also be zero)
        if (!change.isZero()) {
          console.log(`New balance change of ${change}, nonce ${currentNonce}`);
          previousFree = currentFree;
          previousNonce = currentNonce;
        }
    });
}
main()
```

### Subscribe Block Updates

This example shows how to subscribe to and later unsubscribe from listening to block updates.
In this example we're calling the built-in unsubscribe() function after a timeOut of 20s to cleanup and unsubscribe from listening to updates.

```js
const  { ApiPromise } = require('@polkadot/api');
const { WsProvider } = require('@polkadot/rpc-provider');
const { options } =  require('@chainx-v2/api');

async function main() {
    const wsProvider = new WsProvider('wss://staging-1.chainx.org/ws');
    const api =  await ApiPromise.create(options({ provider: wsProvider }));
    await api.isReady;
    // use api
    // Subscribe to chain updates and log the current block number on update.
    const unsubscribe = await api.rpc.chain.subscribeNewHeads((header) => {
       console.log(`Chain is at block: #${header.number}`);
    });
    
    // In this example we're calling the unsubscribe() function that is being
    // returned by the api call function after 20s.
     setTimeout(() => {
       unsubscribe();
       console.log('Unsubscribed');
     }, 20000);
}
main()
```

### Read Storage
In addition to querying the latest storage, you can make storage queries at a specific blockhash. 
Be aware that the node applies a pruning strategy and typically only keeps the last 256 blocks, unless run in archive mode.

```js
const  { ApiPromise } = require('@polkadot/api');
const { WsProvider } = require('@polkadot/rpc-provider');
const { options } =  require('@chainx-v2/api');
// Our address for Alice on the dev chain
const ALICE = '5GrwvaEF5zXb26Fz9rcQpDWS57CtERHpNehXCPcNoHGKutQY';
const BOB = '5FHneW46xGXgs5mUiveU4sbTyGBzmstUspZC92UhjJM694ty';

async function main() {
    const wsProvider = new WsProvider('wss://staging-1.chainx.org/ws');
    const api =  await ApiPromise.create(options({ provider: wsProvider }));
    await api.isReady;
    // Retrieve the last block header, extracting the hash and parentHash
    const { hash, parentHash } = await api.rpc.chain.getHeader();
    console.log(`last header hash ${hash.toHex()}`);
    
    // Retrieve the balance at the preceding block for Alice. For at queries
    // the format is always `.at(<blockhash>, ...params)`
    const balance = await api.query.system.account.at(parentHash, ALICE);
    console.log(`Alice's balance at ${parentHash.toHex()} was ${balance.data.free}`);
    // Now perform a multi query, returning multiple balances at once
    const balances = await api.query.system.account.multi([ALICE, BOB]);
    console.log(`Current balances for Alice and Bob are ${balances[0].data.free} and ${balances[1].data.free}`);
}
main()
```

### Account Module
We can generaet an account Object by ```js chainx.account.from(seed | privateKey | mnemonic)```  

```js 
const alice = chainx.account.from('0x....')
alice.address() // bs58 address
alice.publicKey() // publicKey 0x...
alice.privateKey() // privateKey 0x...
```
#### Account Operate

```js
const { Account } = require('chainx.js');
const account1 = Account.generate();

const publicKey1 = account1.publicKey(); 
console.log('publicKey1: ', publicKey1);

const privateKey1 = account1.privateKey(); 
console.log('privateKey1: ', privateKey1);

const address1 = account1.address(); 
console.log('address1: ', address1);

const mnemonic = Account.newMnemonic(); 
console.log('mnemonic: ', mnemonic);

const account2 = Account.from(mnemonic); 

const address2 = Account.encodeAddress(account2.publicKey());
console.log('address2: ', address2);

const publicKey2 = Account.decodeAddress(address2); /
console.log('publicKey2: ', publicKey2);

Account.setNet('testnet'); 
const address3 = Account.encodeAddress(publicKey2); 
console.log('address3:', address3);

Account.setNet('mainnet'); 
const address4 = Account.encodeAddress(publicKey2); 
console.log('address4:', address4);

const account3 = Account.from(privateKey1);
console.log('address:', account3.address());

```


