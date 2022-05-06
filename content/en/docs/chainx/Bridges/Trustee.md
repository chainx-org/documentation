---
title: "Trust"
linkTitle: "Trust"
weight: 9
description: >
    Trust introduction
---

## 介绍

The significance of the existence of trust is that for cross-chain, if ChainX can support the verification of the light node of the cross-chain (such as Bitcoin), but the cross-chain cannot support the verification of the light node of ChainX, only one-way cross-chain (one-way cross-chain) can only be carried out. Relay). Therefore, a chain that can cross-chain in both directions does not need trust, and only a chain that can cross-chain in one direction requires trust. **Trust is a compromise solution for the inability to cross-chain in both directions. **

This compromise solution requires a high degree of flexibility, and many details cannot be processed on the chain (such as trust qualification confirmation, etc.), so trust can be regarded as requiring a certain amount of trust to some extent. Therefore, ChainX gives trust **great flexibility and rights**

Therefore, for a one-way cross-chain (such as Bitcoin), an address/account needs to be locked (saved) on the cross-chain side of the assets that need to be cross-chain, and the relevant personnel who generate and manipulate this address/account, Called "trust" in ChainX.

**Trust**: It is the role of ChainX to host the tokens of the corresponding chain, so the trust is the **real currency custodian** of cross-chain tokens. Therefore, the participants of the trust must undergo strict KYC verification and publish their own node information, and rank high among the verification nodes to ensure sufficient interest binding relationship to prevent evil.

**Classification of trusts**: Trusts are distinguished by chain units. For example, the trust corresponding to Bitcoin only handles BTC. If Ethereum uses trusts in the future, ETH and ERC20 tokens on Ethereum belong to trust custody.

**Cross-chain (original chain)**: The name for cross-chain on ChainX, such as Bitcoin.

**Cross-chain (original chain) token** is the name on ChainX for the credential that came from the cross-chain on ChainX, such as Bitcoin cross-chain to ChainX, the BTC on the original chain is locked in the multi-signature address of the trust , on ChainX, it will be exchanged 1:1 for a token on ChainX.

**Candidate trust**: Register your own trust information on ChainX when you are already a node, that is, a candidate trust.

**Register as a candidate trust**: To ensure the security and openness of cross-chain addresses, when an account (an entity operating a node) wants to become a trust node, it first needs to register itself as a node (candidate node, verification node) , and then you can register the relevant information (hot public key/address, cold public key/address) you want to become a trust of a certain chain (such as Bitcoin) on ChainX, which is called **candidate trust node**.

**Information of candidate trust registration**: The core part of registered trust information needs to have **hot public key/address, cold public key/address**, of which hot public key/address participates in generating hot address on the chain, cold public key/address The key/address participates in the generation of cold addresses on the chain, and the hot addresses are used to accept users' cross-chain deposits and perform cross-chain withdrawals. When the number of hot addresses saved is large, some funds are transferred to cold addresses for safekeeping.

**Trust list (group)**: Select some nodes from candidate trusts to form a trust list. For the **real currency custody address** the relevant information provided by the current trust list (hot public key/address, cold public key/address)** is generated on the chain**, the address/account is generally **multi-signature (Multi-signature) address/account**, the operation of this address/account requires most of the trusts in the current trust list to operate in the form of multi-signature.

**Trust change (session)**: There is a change of trust list, a round of trust list is called "session" trust list, and the change of trust list is called "trust change". ChainX gives trusts **great flexibility and rights**, so trusts** can only be selected by the previous trust list to the next trust list**.

**Multi-signature (multi-signature) address/account**: Note that this multi-signature (multi-signature) address/account refers to the address on the corresponding escrow chain (such as Bitcoin's multi-signature address). After the candidate trust registers its own trust information on the chain, when a new term of trust is designated for the new term, ChainX automatically generates the multi-signature of the current term based on the trust information on the chain of the new term trust list. (Multi-signature) addresses/accounts, in the current session, these trusts are required to participate in the signature (multi-signature) operation on the corresponding chain when **users withdraw** (such as Bitcoin participating in the signature of the original text to be signed).

**Trust list processing user withdrawal**: The trustee in the current trust list should monitor the application list for user withdrawal. When the user can withdraw, any trustee in the current session can use the user's application list component to create an original application list. The original text of the withdrawal transaction on the chain (the original text to be signed for Bitcoin withdrawal) is sent to ChainX, and other trustees sign the original text of the withdrawal on ChainX (or reject the withdrawal). If the number of signatures reaches the multi-signature requirement, this The transaction will be broadcast to the original chain, and after ChainX is confirmed, the cross-chain tokens on ChainX to the original chain will be destroyed.

**Trust multi-signature operation**: Note that this multi-signature refers to ChainX, and some operations related to trust need to be decided through **multi-signature voting** on the ChainX chain, such as changing the withdrawal fee for Bitcoin users. One trust list selects the next trust list, etc.

## The first trust node

The first trust node is composed of several well-trained nodes during the test network period. After the network is gradually stabilized, the new nodes will join and become familiar with them and begin to change their terms gradually.

- **Hot multi-signature address** is used to receive users' daily deposits and withdrawals
- If there are too many funds in the hot address, the trust nodes will enter the **cold multi-signature address** after offline negotiation for **hot-to-cold** storage
- If you encounter a large withdrawal, you can also perform a **cold-to-hot** operation

## Trust node change

#### The next trust node review
#### The next trust node review

1. At present, the replacement cycle of the proposed trust node is about 10 days. First, check the nodes that have set up the Bitcoin chain trust among the verification nodes.

2. Then take the N with the highest number of votes. With the development of the system maturity, N gradually increases from 4 to 15

3. The last trust node examines whether these N nodes have disclosed their identity information, whether they have basic node and fund custody capabilities, and prevent malicious nodes from appearing, and screen out M nodes.

   The core condition selected is to ensure the reliability of the next trust. Therefore, the judgment conditions can be filtered through the following, and passed the **trust multi-signature operation** of the current trust list:
    - Familiar with the ChainX trust process (important)
        - Have certain operation and maintenance capabilities, and need to maintain a full node of the corresponding chain
        - In-depth understanding of the corresponding blockchain operations (for example, Bitcoin Trust needs to be familiar with the concept and process of Bitcoin's multi-signature address operation, and needs to have the development ability to control tools suitable for its own Bitcoin multi-signature)
        - Possess certain technical ability and can develop suitable tools according to ChainX SDK and other requirements
    - Nodes are willing to be elected as trusts
    - Nodes are ranked high
    - The number of node mortgages is large and the number of votes is large
    - Nodes can flexibly operate hot and cold wallets, and have cold wallet management technology
    - The identity of the node is known and endorsed by a larger interest relationship (such as company, reputation, etc.)
    - other valid filters

#### Preparation for the next trust node

1. Announce the identities of the M nodes, their respective hot public keys, and their respective cold public keys to the community
2. Generate the hot and cold multi-signature addresses of the M nodes through the **chain interface simulation calculation**
3. Enter a small amount of real money into the two multi-signature addresses for testing
4. M nodes assemble to generate a full multi-signature signature of M/M, which is used to indicate that each of them will use multi-signature and the private key is correct

The detailed description of steps 3 and 4 is as follows:

- The hot and cold addresses of the current trust are called: cold-addr1, hot-addr1, and the hot and cold addresses generated by the next simulation are called: cold-addr2, hot-addr2.

- The testing process is as follows:

    - cold-addr1 -> cold-addr2 (init, test cold addr could deposit)
    - cold-addr2 -> hot-addr2 (test cold addr could withdrawal, and hot addr could deposit)
    - hot-addr2 -> cold-addr2 (test hot addr could withdrawal)
    - cold-addr2 -> cold-addr1 (optional, just return test BTC)

  ![img](https://github.com/chainx-org/images/raw/master/trustees_addr_test.png)

#### On-chain trust node change

1. The previous trust node initiated a ChainX multi-signature transaction, and submitted the ChainX account address of the new trust node. These account addresses should have undergone simulation tests to ensure that the registered information is normally available, and the address or contract to be generated is available. of.
2. After completing the transition in the ChainX system, the deposit target address that the user sees and the address monitored by the light node program will change.

#### escrow funds transfer

1. First complete the transition, at this time the multi-signature address on the chain has been replaced
2. The last batch of trust nodes will transfer the funds from the two old multi-signature addresses to the new two multi-signature addresses after offline organization to complete the transfer of funds.
3. The transfer of transfer funds is completed

## Trust multi-signature operation

Since trust is a group, operations and trust-related functions on the ChainX chain (note the non-original chain, please distinguish clearly) need to be operated through the multi-signature module on ChainX.

Trust is also a multi-signature operation group. On the ChainX chain, for each trust of each chain, a trust multi-signature address corresponding to them can be generated according to the trust list. For trusts, the functions that generally require multi-signature operations are as follows:
- trust transition
- User withdrawal fee of the control chain
- Cancel or amend the user's withdrawal request

## Trust income

Compared with ordinary verifiers, trusts have to bear more responsibilities and obligations, so ChainX currently plans to give trusts the following rewards:

1. Take BTC as an example: after the trust is renewed, the withdrawal within the current trust will leave a lot of handling fee balances for withdrawals by users. The remaining handling fee balance can be divided up by this trust after the subsequent trust execution is met.
2. The council will take out 20% of the proceeds to reward the trust
3. ChainX puts trust nodes on the top ranking in the wallet, which helps the trust to attract more votes and enhance its reputation

## Trust related operations

TODO