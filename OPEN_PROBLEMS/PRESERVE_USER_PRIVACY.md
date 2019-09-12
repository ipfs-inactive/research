# Preseve full users' privacy when providing and fetching Content

## Description

The Web 2.0 and its centralized infrastructure missed to deliver full users' privacy when these are accessing and delivering content to other users in the Network. This can happen in one of two ways, either by simply transfering/storing the data uncrypted or by pattern analysis of access which can reveal users interest and intent.

In the Web 3.0, the dWeb, users get the ability to share data with other peers without using an intermediary, however, a complete solution is still missing that can prevent users leaking what data they are serving and fetching through sidechannels/pattern analysis (e.g. When searches are made, either through a search engine or simply by searching for the blocks in a Distributed Hash Table).

Some solutions exist to mitigate this problem (see the State of the Art section below). However, none is yet complete as in "it is always 100% private, period", requiring users to adapt and adopt certain strategies to anonymize the content, depending on the type of interaction they have with other users. This Open Problem is beyond a data encryption or wire encryption problem; a complete solution will have to provide a way to give access to others to access data and later revoke (Authorization), provide users with the ability to know when a piece of data they have shared is being accessed (Accounting) and offer a guarantee that the data is being shared with exactly the right user and no one else (Authentication), this while not letting a third party understand what is being shared, how and with whom.

This happens to be one of the toughest problems to solve in order to provide a complete and human rights preserving fabric for knowledge.

## State of the Art

> This survey on the State of the Art is not by any means complete, however, it should provide a good entry point to learn what are the existing work. If you have something that is fundamentally missing, please consider submitting a PR to augment this survey. 

### Within the IPFS Ecosystem
> Existing attempts and strategies

##### Wire Encryption

Thanks to libp2p, IPFS ensures that the communication between any two IPFS nodes is always encrypted. This is achieved through one of the many Crypto Channels that libp2p supports such as: SECIO, TLS 1.3, DTLS (in WebRTC) and NOISE.

##### Data Encryption

Data Encryption means that we encrypt data at rest and only decrypted to consume it. This means that only the owner of the decryption key can access it. Some solutions with this technique are:

- [ipfs-senc](https://github.com/jbenet/ipfs-senc) - Encrypts the data with a symmetric key that is shared to the receiver through a sidechannel

##### Capability Systems / Cryptographic ACLs

- [peer-base cryptographic ACLs](https://github.com/peer-base/peer-base) - These are used by [PeerPad](https://peerpad.net). For each user, a Public/Private key pair is generated. Every time a user wants to make a modification, the user signs that modification and encrypts it with a symmetric room key so that only owners of the symmetric key can change and only changes from valid peers are accepted.

##### Private/Disjoint Networks

Creating a separate IPFS Network will ensure that only member nodes can access the content within that network. 

- [libp2p-pnet](https://github.com/libp2p/specs/blob/master/pnet/Private-Networks-PSK-V1.md) takes that one step forward and creates a protection using a pre-shared key. This means that only the owners of that key can join this network (to prevent from mistakenly joining two networks and making all data accessible).

##### Onion Routing 

Onion routing is a technique for anonymous communication over a computer network. In an onion network, messages are encapsulated in layers of encryption, analogous to layers of an onion. The encrypted data is transmitted through a series of network nodes called onion routers, each of which "peels" away a single layer, uncovering the data's next destination.

- [libp2p-onion](https://github.com/OpenBazaar/go-onion-transport)

### Within the broad Research Ecosystem
> How do people try to solve this problem?

- [TahoeLAFS Capability System](https://en.wikipedia.org/wiki/Tahoe-LAFS) - The peer-base Cryptographic ACLs were inspired by TahoeLAFS Capabolity System. 
- [DoubleRatchet](https://signal.org/docs/specifications/doubleratchet/) / [Matrix.org Olm/MegaOLM](https://gitlab.matrix.org/matrix-org/olm/blob/master/docs/olm.md#olm-a-cryptographic-ratchet)
- [Using Sphinx to Improve Onion RoutingCircuit Construction](https://www.cypherpunks.ca/~iang/pubs/SphinxOR.pdf)
- [Scuttlebutt Secret Handshare](https://dominictarr.github.io/secret-handshake-paper/shs.pdf)
- [Octopus: A Secure and Anonymous DHT Lookup](https://ieeexplore.ieee.org/document/6258005)
- [Vuvuzela: Scalable Private Messaging Resistant to Traffic Analysis](https://davidlazar.org/papers/vuvuzela.pdf)
- [Define privacy threat model for DHTs (and other overlay P2P networks)](https://github.com/gpestana/notes/issues/3)
- [Talek: a Private Publish-Subscribe Protocol](https://raymondcheng.net/download/papers/talek-tr.pdf)
- [SoK: Secure Messaging](https://ieeexplore.ieee.org/document/7163029)
- [Ricochet](https://github.com/ricochet-im/ricochet/blob/master/doc/protocol.md)
- [Cwtch: Privacy Preserving Infrastructure for Asynchronous,Decentralized, Multi-Party and Metadata Resistant Applications](https://cwtch.im/cwtch.pdf)
- [HOPR - privacy-preserving messaging protocol ](https://github.com/validitylabs/hopr)

### Known shortcommins of existing solutions
> What are the limitations on those solutions?

Some of the known shortcommings of existing solutions are:

- They don't offer protection to network analysis (it is possible to infer what the user is doing by analysis the network traffic)
- Solutions that are more resistant (not fully resistent) typically trade off bandwidth + memory for creating that protection (e.g. creating noise in the network to make it hard to distinguish valid from dummy traffic) 
- Lack of data encryption at rest
- Lack of complete authorization + revocation

## Solving this Open Problem

### What is the impact

Finding a complete solution to this Open Problem will grant the ability for any human in the planet to privately share information, read content without leaking their intent to anyone else other than the provider of that content, or even possibly, not even to the provider.

It will lead to a generation of safer applications, both consumer level and business critical, that require full control of one's data (e.g. Health Data).

### What defines a complete solution?
> What hard constraints should it obey? Are there additional soft constraints that a solution would ideally obey?

Valid solutions should improve the current state of the art or offer definitive solutions for:

- [ ] Two users can transfer a piece of information that no other user knows or can guess: what it is, why it is being transfer, between whom and when.
- [ ] No single central authorities are required to mediate the communication
- [ ] A provider has a way to grant and revoke access to information.

As additional constraints

- [ ] Mechanism to prevent data exfiltration (e.g. when a user goes rogue)

## Other

### Existing Conversations/Threads

- [Censorship resistance on IPFS](https://github.com/ipfs/notes/issues/281)
- [Content Encryption](https://github.com/ipfs/notes/issues/270)
- [Shared Secret constructions for Private Networks](https://github.com/ipfs/notes/issues/177)
- [Search over encrypted data](https://github.com/ipfs/notes/issues/128)
- [Plausible deniability for blocks](https://github.com/ipfs/notes/issues/21)
- [Alternative BitSwap strategies](https://github.com/ipfs/notes/issues/20)
- [Anonymous IPFS](https://github.com/ipfs/go-ipfs/issues/6430)
- [Privacy preserving DHTs](https://github.com/gpestana/notes/issues/8)
- [DHT improvement ideas](https://github.com/libp2p/research-dht/issues/6)
- [Tor onion integration](https://github.com/ipfs/notes/issues/37)
- [Identity RFC](https://github.com/ipfs-shipyard/peer-star/pull/15)

### Extra notes
