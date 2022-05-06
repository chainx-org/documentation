---
title: "Asset Transfer Bridge/Gateway"
linkTitle: "Asset Transfer Bridge/Gateway"
weight: 6
description: >
   Asset transfer bridge/gateway refers to the control module that transfers assets of other chains to ChainX or back from ChainX
---

{{% pageinfo %}}
Asset Transfer Bridge/Gateway (called Asset Gateway Gateway in 2.0)
{{% /pageinfo %}}

## 介绍

Asset transfer bridge/gateway refers to the control module that transfers assets of other chains to ChainX or back from ChainX。

For example, when the BTC assets on the Bitcoin chain need to be transferred to ChainX to become X-BTC, or the process of transferring X-BTC on the ChainX chain back to the Bitcoin chain, the Bitcoin transfer bridge/gateway controls the verification of this process, and Asset issuance/destruction process。

## Term：

> This chain: refers to the ChainX chain itself, such as "processing something on this chain" means "the process of including and executing something on the ChainX chain"。
>
> Original chain: Refers to the chain from which the asset originates after the asset is moved to ChainX. If X-BTC originates from the Bitcoin chain and exists on ChainX, the Bitcoin chain is called the original chain of X-BTC。
>
> Heterogeneous chain: There is a big gap between the model structure of the original chain and the ChainX/Substrate chain model structure, so it is difficult to deal with all heterogeneous chains in a unified form, and it is necessary to have corresponding processing for different chains。
>
> Isomorphic chain: Refers to the model structure of the original chain that is consistent with the ChainX/Substrate chain structure, basically referring to the blockchain developed with Substrate。

## The core principle of heterogeneous chain cross-chain

Compared with the cross-chain principle of Polkadot parallel chains, the cross-chain of heterogeneous chains is essentially:

1. Know/proven on the ChainX chain that something did happen on the other chain；
2. If this matter is to lock assets, then issue the corresponding assets to an account on the ChainX chain；
3. If this matter is to release assets, then the corresponding assets on an account will be destroyed on the ChainX chain。

Therefore, heterogeneous cross-chain is a variant of Oracle mode in principle。

There are currently only two ways to solve the problem that Oracle data is sent to a certain chain and ensure the correctness of the data.

1. The data itself carries the proof (it can be proved by itself), such as light node/zero-knowledge proof, etc.
2. Data is proved by voting/authorizer, such as Substrate offchain worker/a link, etc.

Therefore, the mode of heterogeneous cross-chain will not jump out of the above two principles.

## Gateway classification

For the movement of assets between the ChainX chain and the original chain, it needs to be divided into the following two situations:

* An asset crosses onto ChainX (ie, recharge);
* The assets that cross over to ChainX are returned to the original chain (that is, withdrawn);

Due to the large differences in functions on heterogeneous chains, the solutions for depositing/withdrawing an asset on ChainX are different in many cases.

Currently, for ChainX's asset gateway model, a total of 4 types are designed to deal with different heterogeneous chain models:

Taking the A public chain as an example, A can handle the paradigm of moving assets to ChainX and moving back to the original chain in four ways:

1.Light node verification

Integrate the light node of the original chain on ChainX, submit the block header of the light node and the corresponding transaction to the ChainX chain, and execute the verification of the transaction on the ChainX chain by means of the light node data self-certifying method, and confirm that the transaction is in the corresponding heterogeneous It actually happened on the chain (original chain). The heterogeneous chain needs to have the function of a light node. If there is no light node function, it cannot be supported.

Light node verification is mostly used for the recharge process. If the withdrawal process also uses light nodes, the original chain is required to be able to write the ChainX light node verification logic. This function can almost only be used on the ChainX isomorphic chain, unless the original chain supports complex functional logic.

   **This mode is a purely decentralized way to cross-chain, and the data correctness is guaranteed by the light node's own proof method.**

Example: X-BTC deposit process on ChainX.

2. Multi-signature control

The control method of cross-chain assets on ChainX and the operation method of returning assets to the original chain. It is mostly used to solve the asset management and withdrawal process after light nodes and POA recharge cross-chain assets. The meaning is that the assets of the original chain are cross-chained to ChainX, and the multi-chain mode supported by the original chain requires multiple people to hold the public key and lock the assets that are cross-chained. When assets need to be returned from ChainX to the original chain, multiple people participate in the operation and multi-signature to release the originally locked assets.

Multi-signature control generally allocates a hot address and a cold address on the original chain, in which the deposit and withdrawal process of assets only occurs on the hot address, and the assets that have not been moved for a long time are transferred to the cold address for storage to protect the security of assets.

When the multi-signature control is combined with the light node, the withdrawal process is generally verified by the light node verification scheme to verify the withdrawn transaction.

   **This mode is multi-centralized and cross-chain, and the security of assets is guaranteed by the decentralized form of multi-signature private keys.**

Example: X-BTC withdrawal process on ChainX.

3. POA（Proof of authority）

   That is, an authorized individual or subject locks/releases assets on the original chain and issues/destroys assets on the ChainX chain.

   **This mode is a centralized way to cross-chain, and the security of assets is guaranteed by trusting an entity.**

4. mortgage issuance

   Cross-chain your own mainnet token on Kusama/Polkadot's ChainX parachain by staking PCX or X-BTC

   **This mode is a new decentralized cross-chain method.**

