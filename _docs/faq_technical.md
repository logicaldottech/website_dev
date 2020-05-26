---
title: "FAQ: Technology"
category: FAQ
---

<ul id="auto-table-of-contents">
</ul>

### Is Diode Chain a permissioned or permissionless blockchain?

Diode Chain is a permissionless, decentralized public blockchain which requires minimal computational power to join.


### What is BlockQuick?

Developed by the Diode team, [BlockQuick](https://diode.io/burning-platform-pki/blockquick-super-light-blockchain-client-for-trustless-time-19144/) is the first super light client protocol for Ethereum that addresses the issue of [man-in-the-middle attacks](https://en.wikipedia.org/wiki/Man-in-the-middle_attack) and [eclipse attacks](https://diode.io/blockchain/how-blockquick-super-light-client-protocol-can-help-mitigate-eclipse-attacks-19161/). It is capable of preventing attacks because it doesn’t depend solely on choosing the longest chain with the highest difficulty on the Proof-of-Work (PoW) network when validating a block. Instead, it validates a block based on the so-called [Consensus Reputation Table](https://diode.io/blockquick/blockquick-consensus-reputation-table-explained-19182/).

BlockQuick super light client protocol is designed for anyone who hopes to connect IoT devices to public blockchains. Unlike the current full sync, fast sync, or light sync requirements (i.e. a full sync typically requires more than 200GB of disk storage), through BlockQuick the device only needs a handshake size of 20KB to establish the most recent block hash. The design enables a client to sync up to a recent block with a consensus reputation table.


### What is the difference between a full node, a light client, and a super light client?

In the Ethereum network, you have the option of running various types of nodes: full nodes, light clients, super-light clients. One of the key differences between a full node, a light client, and a super light client concerns the size of required storage to operate. For a full node, it requires hundreds of gigabytes of storage to operate (think more than 100GB). For a light client, it commonly requires less storage size (think a few gigabytes) to operate. For a super-light client, it only takes less than 100KB of storage to operate.

Below is an overview of the three node types with their storage requirements.

![alt_text](images/blog/FAQ-03.png "image_tooltip"){: .center-block }

Full nodes are typically run on servers in data-centers; they are commonly used for mining purposes. Light clients require less storage, but also offer less functionality as compared to running a full node. Light clients only need to download the block headers of the blockchain, and are designed for desktop and mobile wallet applications. The amount of data they have to store is much smaller, but still grows at the same pace as the blockchain itself.

Super light clients such as [Diode’s BlockQuick](https://diode.io/burning-platform-pki/blockquick-super-light-blockchain-client-for-trustless-time-19144/) require even less storage than light clients. Super light clients do not need to download every single block header for validation, and are designed for resource-constrained devices. As a result, super light clients can’t be used to monitor the blockchain in real time; instead, they read data periodically. 

A more detailed introduction of blockchain clients’ hardware requirements can be found in our [blog post](https://diode.io/iot/hardware-requirements-of-blockchain-clients-19196/).


### What is BlockQuick Reputation Table?

Consensus reputation table is a key security policy which BlockQuick adopts when validating a block. The consensus reputation table is constructed on each client based on the last known block headers in a given time frame. For instance, in a Proof-of-Work (PoW) system the client will be able to construct the consensus reputation table by reading 100 previous block headers from the last known state of the blockchain. All past block headers of the blockchain can be validated on the client using the parent block checksum alone. Thus, clients are able to fetch these block headers from untrusted nodes as the data can be validated using the existing block header and its contained parent block hash.


### If BlockQuick Consensus Reputation Table wants to keep the whole history of blockchain, wouldn’t it make the IoT device slower?

The BlockQuick consensus reputation table is not designed to keep the whole history of the blockchain. Instead, the reputation table is based on the last 100 block headers. Block headers are much smaller than the whole block and only requiring 100 of them means that even very small devices can store and process them. 


### How did BlockQuick decide to use 100 last blocks in the reputation table? Where did the number come from? Why not more, why not less?

The current proof of concept clients are working with consensus table sizes of 100. 

What we learned in Ethereum is that small miners are fluctuating a lot, while big miners stay for very long periods. So for the reputation table size, the question is how we can achieve the maximum consensus stability.

Factors influencing the size of the table:

Pro Small
*   Smaller tables mean fewer bytes to transport and store
*   Large/Infinite tables would collect "zombies"

Pro Big
*   Larger tables increase the cost of an attack
*   Variability vs Stability

### So every DNS change is stored into the blockchain, forever?

The question came from some comments from Hacker News ([https://news.ycombinator.com/item?id=20764104#20764865](https://news.ycombinator.com/item?id=20764104#20764865)) "So every DNS change is stored into the blockchain, forever? Will you have to download terabytes and terabytes of the blockchain in order to serve as a node? Why is that kind of audit history necessary?"

You don’t necessarily need to store DNS changes into the blockchain. The blockchain will only keep the current state and would prune the changes. According to Diode’s blog posts, 20kb of storage is all it needs with BlockQuick, the newly developed light-client protocol. The point is less about storing the audit history, but more about preventing Man-in-the-Middle attacks and solving the timestamp-certificate chicken-egg problem.

### If you can https/ssh Pi through the local network, why do you need Diode network?

SSH: If you’re not local, Diode is the only choice. (SSH is not using PKI) \
Usually nobody is checking the remote key - so Diode is better since you don’t have to.

HTTPS: Even if you’re local, there are security issues when using a self-signed certificate to accessing your Pi. 

### Why did you want to design edgeRPC? Why don’t you go for existing options such as  gRPC or JsonRPC?

At this stage, we use edgeRPC to allow nodes to interact with devices. We started out with ASN1 (like OpenSSL - super small binary format), and there is a chance that we would start using JsonRPC.

### Did you experience any “inconsistency issues” when using OpenSSL? For instance, the functions in OpenSSL could be different, such as the  function interface parameters could be different.

When choosing a secure communication channel, we looked at RLPx and TLS (OpenSSL). We decided to use OpenSSL, but forced it into using Secp256k1 to ensure both sides authenticate with their Eth wallet keys. But this means we are forcing OpenSSL to only accept Secp256k1 and self-signed certificates (self signed using the wallet key). 

OpenSSL does support Secp256k1. However, not all TLS libraries and clients support Secp256k1 at this time. So this can lead to connection issues when not using OpenSSL. We will be researching other libraries such as ARMmbed, wolfSSL, and SChannel for that matter.

### How are you guys using websocket?

The default Go client has a built-in capability to convert any binary TCP socket into a websocket. So, the Pi in the demo is simply publishing the raw video on port, and then the Go client is converting that into websocket frames.

### If you are using OpenSSL, does it mean you are using a CGo compiler?

Yes, we use CGo to call C functions.
