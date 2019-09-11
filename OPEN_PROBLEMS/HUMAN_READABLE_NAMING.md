# Human Readable Naming

## Description




## State of the Art

> This survey on the State of the Art is not by any means complete, however, it should provide a good entry point to learn what are the existing work. If you have something that is fundamentally missing, please consider submitting a PR to augment this survey. 

### Within the IPFS Ecosystem
> Existing attempts and strategies

##### DNSlink

- https://docs.ipfs.io/guides/concepts/dnslink/

##### ENS with IPFS

- Talk: "ENS + IPFS: Using ENS as a naming system for IPFS", Makoto Inoue https://github.com/ipfs/camp/blob/master/LIGHTNING_TALKS/ipfscamp2019-lightningtalk-ensipfs.pdf
- https://youtu.be/3AS2BD22DZg

##### mnemonic base

Multibase now has support for a [mnemonic base](https://github.com/multiformats/js-multibase/compare/36d60d3f9379ea005327fe3a375f03ba286d0ecc...tableflip:feat/mnemonic-base) and given that CIDs are encoded with multibase so that they can be represented as strings, you can get your own mnemonic base encoded content address. This makes a name pronunceable, but given the amount of information in a CID, it is a long mnemonic.

### Within the broad Research Ecosystem
> How do people try to solve this problem?

### Known shortcommins of existing solutions
> What are the limitations on those solutions?


## Solving this Open Problem

### What is the impact

### What defines a complete solution?
> What hard constraints should it obey? Are there additional soft constraints that a solution would ideally obey?

## Other

### Existing Conversations/Threads

- [Human Friendly Names](https://github.com/ipfs/notes/issues/286)
- [JRFC 28 - note on DNS and the Web](https://github.com/jbenet/random-ideas/issues/28)

### Extra notes

- [https://decentralized.blog/ten-terrible-attempts-to-make-ipfs-human-friendly.html](https://decentralized.blog/ten-terrible-attempts-to-make-ipfs-human-friendly.html)
