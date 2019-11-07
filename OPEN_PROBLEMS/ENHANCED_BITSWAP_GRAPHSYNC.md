# Enhanced Bitswap/GraphSync with more Network Smarts

## Short Description
> In one sentence or paragraph.

Once IPFS has resolved the CID to a peerID and further to the exact location of the node that  stores the requested content, it then uses a very simple protocol called bitswap to exchange content between the requestor and the server.

Although bitswap is simple and generally works, *its performance is significantly suboptimal*. This is mainly due to the fact that a node cannot request a subgraph of the DAG and results in many round-trips in order to “walk down” the DAG. The current operation of bitswap is also very often linked to duplicate transmission and receipt of content which overloads both the end nodes but also the network.

## Long Description

In order to synchronise a DAG between untrusted nodes, bitswap is exploiting the content-addressability feature of IPFS. This means that bitswap starts with the root hash of the DAG and once it has the data, it rehashes the data to verify that it results to the same hash. The node can now trust this block and hence, can continue with the rest of the blocks in the DAG (aka walk the DAG), until it gets the whole DAG and the data associated with it.

In the current implementation of bitswap, requesting nodes send their WANT lists to all the peers they are directly connected to. This inevitably results in potentially duplicate traffic travelling back to the receiving node. When the receiving node has received the block(s) it asked for, it sends a CANCEL message to its peers to let them know that the block(s) is not in its WANT list anymore.

If none of the directly connected peers have any of the WANT list blocks, bitswap falls back to the DHT to find the requested content. Obviously, this results in long delays to get to the actual peer that stores the requested content.

Furthermore, once the recipient node starts receiving content from multiple peer nodes, it prunes down the long-latency peers and keeps the one to which the RTT is the shortest. Instead, current proposals within the IPFS ecosystem are considering optimising for throughput. Although this has its advantages, which one is the ideal to follow is not clear at this point.

It is important to highlight that bitswap is a message-oriented protocol and _not_ a request-response protocol. That is, it sends messages including WANT lists and  is then waiting for the blocks in the WANT list to be delivered back, as and when the discovery protocol manages to find the blocks in the WANT list.


This process of bitswap results in the following major problems:

- **Many Roundtrips:** The most pressing issue is that requesting nodes have to make several roundtrips to walk the DAG, especially given that IPFS is a network run by untrusted nodes.
- **Duplicate Data:** as mentioned, bitswap is operating at the block level. In turn, this means that every block has to be requested from each peer that the node is connected to. This process results in duplicate traffic towards the data requestor unnecessary load for the peers and high overhead for the network.

## State of the Art

> This survey on the State of the Art is not by any means complete, however, it should provide a good entry point to learn what are the existing work. If you have something that is fundamentally missing, please consider submitting a PR to augment this survey. 

### Within the IPFS Ecosystem
> Existing attempts and strategies

The IPFS community has long been aware of the issues of bitswap and has experimented (at least conceptually) with potential solutions. We are discussing some of them here:

- *Parallelise the DAG walk.* Inherently, DAGs have one root node and then split into binary trees. Doing one walk for each of the branches results in the same number of round trips, but reduces the actual delay in half. Clearly, this approach does not apply for single-branch graphs, such as blockchains.

- *DAG Block Interconnection.* Although bitswap does not/cannot recognise any relationship between different blocks of the same DAG, a requesting node can artificially continuously ask for subsequent blocks of the same block the node that it found the previous block. This approach intuitively assumes that a node that had a block of a DAG is very likely to have subsequent ones. This is often referred to as “session” between the peers that have provided some part of the DAG.

- *Latency vs Throughput.* Bitswap is currently sorting peers by latency, i.e., it is pruning down the connections that incur higher latency. It has been suggested that this is changed to maximise throughput (i.e., keep the pipe full).

- *Coding and Reed Solomon Codes.* This is a very efficient way to reduce storage space in general (trusted or untrusted) caching systems. The area of coded caching has seen significant development lately in the research community, hence, it is discussed further down.

- *GraphSync through IPLD selectors:* GraphSync is a specification of a protocol to synchronise graphs across peers. Graphsync answers the question of “how do we succinctly identify selectors connected to the root”. In doing that, it develops WANT lists of selectors instead of blocks and requests have selectors instead of hashes. Furthermore, in contrast to bitswap which is message-oriented, graph sync includes responses and hence, it is closer to a request-response protocol. If designed carefully, Graphsync can be very effective, but there are challenges that are still unresolved: i) it is more difficult to parallelise (see above), given that GraphSync operates in terms of requests and not blocks, ii) it can potentially be much easier to carry DDoS attacks with queries than with requests for blocks.

- *WANT Potential and HAVE message.* There have been several optimisation proposals, such as the implementation of a HAVE message before actually sending the requested block back. Although this immediately reduces duplicate traffic, it introduces one extra RTT, which might not be desirable in some cases, but might not have any negative impact in many others.

- Golang Implementation of Bitswap: https://github.com/ipfs/go-bitswap
- GraphSync: https://github.com/ipld/specs/blob/master/block-layer/graphsync/graphsync.md
- Bitswap Latency Measurement: https://github.com/ipfs/go-bitswap/issues/166
- Bitswap Sessions Exploration: https://github.com/ipfs/go-bitswap/issues/165
- Bitswap Protocol Extensions: https://github.com/ipfs/go-bitswap/issues/186
- Reed-Solomon layer over IPFS: https://github.com/ipfs/notes/issues/196
- Bitswap Request Sharding: https://github.com/ipfs/go-bitswap/issues/167

### Within the broad Research Ecosystem
> How do people try to solve this problem?

*Bittorrent*

The bittorrent protocol has a different architectural design, which includes the “tracker” that keeps track of chunks per node and coordinates what is downloaded from which node. The design is substantially different to the IPFS, but included some interesting policies, such as fetch “Rarest First”, “Random First” and “Endgame Mode”, which is a greedy mode to collect all the chunks that a node has not managed to find yet.

- Peer-to-Peer Networking with Bittorrent, 2005, http://web.cs.ucla.edu/classes/cs217/05BitTorrent.pdf
- THE BITTORRENT P2P FILE-SHARING SYSTEM: MEASUREMENTS AND ANALYSIS https://pdos.csail.mit.edu/archive/6.824-2007/papers/pouwelse-btmeasure.pdf
- Do incentives build robustness in BitTorrent?, NSDI 2007, https://www.usenix.org/legacy/events/nsdi07/tech/piatek/piatek.pdf
- Modeling and performance analysis of BitTorrent-like peer-to-peer networks, ACM CCR 2004 http://conferences.sigcomm.org/sigcomm/2004/papers/p444-qiu1.pdf
- Analyzing and Improving a BitTorrent Network’s Performance Mechanisms, Infocom 2006, http://jmvidal.cse.sc.edu/library/bharambe06a.pdf

*Coded Caching*

There have been significant research efforts lately in the area of coded caching. The main concept has been proposed in 1960s in the form of error correction and targeted the area of content delivery over wireless, lossy channels. It has been known as Reed-Solomon error correction. Lately, with seminal works such as “Fundamental Limits of Caching”, Niesen et. al. have proposed the use of coding to improve caching performance. In a summary, the technique works as follows: if we have a file that consists of 10 chunks and we store all 10 chunks in the same or different memories/nodes, then we need to retrieve those exact 10 chunks in order to reconstruct the file.

In contrast, according to the coded caching theory, before storing the 10 chunks we encode the file using erasure codes. This results in some number of chunks x>10, say 13, for the sake of illustration. This clearly results in more data produced after adding codes to the original data. However, when attempting to retrieve the original file, a user needs to collect *any 10 of those 13 chunks*. By doing so, the user will be able to reconstruct the original file, without needing to get all 13 chunks. Although such approach does not save bandwidth (we still need to reconstruct 10 chunks of equal size to the original one), it makes the network more resilient to nodes being unavailable. In other words, in order to reconstruct the original file without coding, all 10 of the original peers that store a file have to be online and ready to deliver the chunks, whereas in the coded caching case, any 10 out of the 13 peers need to be available and ready to provide the chunks. Blind replication of the original chunks will not provide the same benefit, as the number of peers will need to be much higher (at least 20 as compared to 13) in order to operate with the same satisfaction ratio.

- Reed-Solomon Error Correction: https://en.wikipedia.org/wiki/Reed–Solomon_error_correction
- Maddah-Ali,  M.A.,  Niesen,  U.   Fundamental  limits  of  caching. IEEE  Transactions  on  Information Theory,60(5):2856–2867, 2014
- Fundamental Limits of Caching (slides): http://archive.dimacs.rutgers.edu/Workshops/Green/Slides/niesen.pdf 
- Shanmugam,  K.,  Golrezaei,  N.,  Dimakis,  A.G.,  Molisch,  A.F.,  Caire,  G.    Femtocaching:   Wire-
less content delivery through distributed caching helpers. IEEE Transactions on Information Theory, 59(12):8402–8413, 2013
- Amiri, M.M., Gündüz, D.  Fundamental limits of coded caching: Improved delivery rate-cache capacity
tradeoff. IEEE Transactions on Communications, 65(2):806–815, 2017
- Decentralized Coded Caching Attains Order-Optimal Memory-Rate Tradeoff, IEEE Transactions on Networking, 2015, https://ieeexplore.ieee.org/document/6807823, https://arxiv.org/pdf/1301.5848.pdf
-  Coded Caching With Nonuniform Demands, IEEE Transactions on Information Theory, 2017, https://ieeexplore.ieee.org/document/7782760


### Known shortcomings of existing solutions
> What are the limitations on those solutions?

The shortcomings of the current bitswap protocol have been discussed above in the State of the Art section. GraphSync is a very promising next step, which however, comes with its own challenges, also discussed above.

The approaches that bittorrent has used need to be evaluated, but always keeping in mind that its architecture is different and based on a (logically) centralised tracker.

Coded caching on the other hand incurs some overhead, which might not be ideal for several use-cases of the IPFS system (e.g., popular content), but very convenient for others (cold storage).

## Solving this Open Problem

### What is the impact

Bitswap is a central component of the IPFS system and a protocol that generally influences the performance of IPFS as a whole. Designing and integrating smart algorithmic solutions to improve its performance can be instrumental for the performance and adoption of IPFS. It is worth highlighting that bitswap allows ample space for improvement, hence, several different solutions are encouraged and will be considered. Addressing sub-graphs of the DAG through IPLD as GraphSync is attempting to do in a secure manner is a first necessary step.

### What defines a complete solution?
> What hard constraints should it obey? Are there additional soft constraints that a solution would ideally obey?

First and foremost, any complete solution should account for extensibility as the IPFS system needs to scale up and more applications are implemented on top. The active number of users of IPFS is increasing exponentially and the requests submitted to the network are following accordingly. That said, a complete solution should account for those numbers.

There are several desirable features discussed within IPFS that should be implemented in a complete solution for bitswap and its successor GraphSync - see list below in Existing Conversations/Threads section. Some of them target clean protocol extensions, while some others target higher-level measurement and UI/UX issues.

## Other

### Existing Conversations/Threads

- [Batch gets](https://github.com/ipfs/notes/issues/285)
- [Move giant files/datasets at Level1 + Level2 ](https://github.com/ipfs/notes/issues/218)
- [Reed-Solomon layer over IPFS](https://github.com/ipfs/notes/issues/196)
- [Tracking progress and other data](https://github.com/ipfs/notes/issues/107)
- [Advanced Pinning](https://github.com/ipfs/notes/issues/49)
- [bandwidth estimators](https://github.com/ipfs/notes/issues/30)
- [Alternative BitSwap strategies](https://github.com/ipfs/notes/issues/20)
- [2-Hop Bitswap](https://github.com/ipfs/notes/issues/386)
- [IPFS Spy](https://github.com/jimpick/go-ipfs/blob/jim/network-logging/README.ipfs-spy.md)
- [Bitswap Simulator](https://github.com/heems/bssim)
- [bsdash - dashboard for go-ipfs bitswap](https://www.npmjs.com/package/bsdash)

- Presentation of bitswap in Sept 2019 IPFS Weekly Call: https://www.youtube.com/watch?v=G_Q7iTpwYQU&list=PLuhRWgmPaHtSGRSHdU9dbsukHKlihZZAe&index=4


### Extra notes
