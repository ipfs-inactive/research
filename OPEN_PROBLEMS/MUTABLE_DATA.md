# Mutable Data (Naming, Real-Time, Guarantees)

## Short Description
> In one sentence or paragraph.

How to enable a multitude of patterns of interaction between users and users, users and machines and machines and machines. In another words, what primitives to create to dynamic data apps and what guarantees can these offer.

## Long Description

Mutable Data in a Permanent Datastore? WAT?! If this is your reaction, don't despair, we've been there too. However, the good news is that achieving mutability over immutable datastores is not a new idea, in fact, if you are checking this document through Git/GitHub, you are experiencing just that.

The simple trick is to use pointers. While each data struct will have its own reference (in the case of IPFS, they will have a CID), mutability is achieved by updating the pointer to point to a new version of that data structure, just like when you update your master branch (pointer) to your latest commit (immutable reference) on your Git projects.

Having Mutable Data is essential to create a whole new set of applications for the Web 3.0. These applications will have different types of requirements, from millions of records being updated every second to millions of users interacting and mutating a data source.

Creating a sound solution to achieve Mutable Data for the dWeb requires considering the three parts of the problem:

- **Update Propagation** - As noted, diffent applications will have different requirementsm, these can go from real-time delivery, to delivery at least/most once, confirmation of delivery, conflict resolution, etc.
- **Update Authentication** - How to trust the parties that are propagating the updates, how to trust that they are not hiding any important update
- **Interface/API** - This is where things that shape up to feel like a database start to show. From convinence features to powerful APIs that enable a user to query, update and search over multiple records

All of these problems should be solved to work on a Distributed Network scenario, this means: disconnected, offline first and over unreliable networks.

## State of the Art

> This survey on the State of the Art is not by any means complete, however, it should provide a good entry point to learn what are the existing work. If you have something that is fundamentally missing, please consider submitting a PR to augment this survey. 

### Within the IPFS Ecosystem
> Existing attempts and strategies

##### IPNS, the InterPlanteary Naming System

IPNS is the solution baked into IPFS Core that handles naming. IPNS takes inspiration from the [Self-Certified FileSystem (SFS)](https://en.wikipedia.org/wiki/Self-certifying_File_System) and uses the concept of signed records and a distributed record store for storing and sharing them. When a user gets a signed record, that user can verify its signature, check the updated pointer and resolve it accordingly. Today IPNS can be used over multiple routers, DHT, PubSub and Cloudfare WebWorkers.

- https://docs.ipfs.io/guides/concepts/ipns

##### dnslink

DNSLink leverages the DNSSystem to store a TXT record that contains a hash. This is equivalent to having an A record that stores an IP, in this case, the TXT record is resolved by an IPFS node, fetching the hash and resolving the content for the user. All IPFS project websites are loaded through this naming system (e.g. https://ipfs.io)

- https://docs.ipfs.io/guides/concepts/dnslink

##### libp2p's floodsub/gossipsub

libp2p currently offers two PubSub implementations, floodsub and gossipsub. 

- https://github.com/libp2p/specs/blob/master/pubsub/README.md
- https://github.com/libp2p/specs/blob/master/pubsub/gossipsub/README.md

##### peer-base's CRDTs implementations

CRDTs are an extraordinary building block for data mutability without requiring central mediation. The field of CRDT is vast and there has been many developments. Below you can find some of the implementations done to work with IPFS.

- https://github.com/ipfs-shipyard/js-delta-crdts
- https://github.com/ipfs-shipyard/peer-crdt 
- https://github.com/ipfs-shipyard/peer-crdt-ipfs
- https://orbitdb.org/

##### Textile's data wallet and caffee 

Textile offers a set of tools for build decentralized applications that also work on mobile platforms. Their documentation is pretty amazing and keeps evolving, to learn more follow the links below:

- https://docs.textile.io/concepts/the-wallet
- https://docs.textile.io/concepts/cafes
- [Tutorial on how to use Textile form IPFS Camp](https://github.com/ipfs/camp/tree/master/CORE_AND_ELECTIVE_COURSES/ELECTIVE_COURSE_D)

### Within the broad Research Ecosystem
> How do people try to solve this problem?

There are multiple decades of Research and Projects to solve this Open Problem. Don't feel disencourged by the sheer amount of articles, it is really high valuable work!

- Data Structures
  - [Conflict-free replicated data types](https://scholar.google.pt/citations?view_op=view_citation&hl=en&user=NAUDTpMAAAAJ&citation_for_view=NAUDTpMAAAAJ:M3ejUd6NZC8C)
  - [A comprehensive study of Convergent and Commutative Replicated Data Types](http://hal.upmc.fr/inria-00555588/document)
  - [Merging OT and CRDT Algorithms](http://dl.acm.org/citation.cfm?id=2596636)
  - [Delta State Replicated Data Types](https://arxiv.org/abs/1603.01529)
  - [CRDTs: Making δ-CRDTs Delta-Based](http://novasys.di.fct.unl.pt/~alinde/publications/a12-van_der_linde.pdf)
  - [Key-CRDT Stores](https://run.unl.pt/bitstream/10362/7802/1/Sousa_2012.pdf)
  - [Compreensive PubSub Reading List](https://ipfs.io/ipfs/QmNinWNHd287finciBwbgovkAqEBQKvnys1W26sY8uupc5/pubsub%20reading%20list.pdf)
  - [A CRDT Primer Part I: Defanging Order Theory](http://jtfmumm.com/blog/2015/11/17/crdt-primer-1-defanging-order-theory/)
  - [A CRDT Primer Part II: Convergent CRDTs](http://jtfmumm.com/blog/2015/11/24/crdt-primer-2-convergent-crdts/)
  - Talks
    - [RedisConf18: CRDTs and Redis—From Sequential to Concurrent Executions by Carlos Baquero](https://www.youtube.com/watch?v=ZoMIzBM0nf4)
    - [QCon London 2018: CRDTs and the Quest for Distributed Consistency by Martin Kleppmann](https://www.infoq.com/presentations/crdt-distributed-consistency)
    - ["CRDTs Illustrated" by Arnout Engelen](https://www.youtube.com/watch?v=9xFfOhasiOE)
    - [Coding CRDT](https://www.youtube.com/playlist?list=PLzUeAPxtWcqxBXjUelmcm5ORVjEpbUlHH)
    - [Dmitry Ivanov & Nami Naserazad - Practical Demystification of CRDT (Lambda Days 2016)](https://www.youtube.com/watch?v=PQzNW8uQ_Y4)
    - [ElixirConf 2015 - CRDT: Datatype for the Apocalypse by Alexander Songe](https://www.youtube.com/watch?v=txD1tfyIIvY)
    - [GOTO 2016 • Conflict Resolution for Eventual Consistency • Martin Kleppmann](https://www.youtube.com/watch?v=yCcWpzY8dIA)
    - [CRDTs in IPFS](https://www.youtube.com/watch?v=2VOF-Z-nLnQ)
    - [Journal Club - 2018 06 13 CRDT JSON Datatype, by Gonçalo Pestana](https://www.youtube.com/watch?v=TRvQzwDyVro)
  - [CRDT Tutorial for Beginners](https://github.com/ljwagerfield/crdt)
  - [Conflict-Free Replicated Data Types (CRDTs), An Offline Camp passion talk](https://medium.com/offline-camp/conflict-free-replicated-data-types-crdts-2c6ae67ab9a4#.duh4g0r9k) 
  - [CRDT Notes by Paul Frazee](https://github.com/pfrazee/crdt_notes)
  - [Towards a unified theory of Operational Transformation and CRDT by Raph Levien](https://medium.com/@raphlinus/towards-a-unified-theory-of-operational-transformation-and-crdt-70485876f72f)
  - [Data Laced with History: Causal Trees & Operational CRDTs](http://archagon.net/blog/2018/03/24/data-laced-with-history/) 
  - [A Conflict-Free Replicated JSON Datatype](https://arxiv.org/pdf/1608.03960.pdf)
  - [Snapdoc: Authenticated snapshots with history privacy in peer-to-peer collaborative editing](https://martin.kleppmann.com/papers/snapdoc-pets19.pdf)
  - [OpSets: Sequential Specifications for Replicated Datatypes](https://arxiv.org/abs/1805.04263)
- Access Control
  - [Access Control for Weakly Consistent Data Stores](http://www.complang.tuwien.ac.at/kps2015/proceedings/KPS_2015_submission_25.pdf)
  - [ACGreGate: A Framework for Practical Access Control for Applications using Weakly Consistent Databases](https://arxiv.org/abs/1801.07005)
  - [TRVE Data: Placing a bit less trust in the cloud](https://www.cl.cam.ac.uk/research/dtg/trve/)
  - [LSEQ: an Adaptive Structure for Sequences in Distributed Collaborative Editing](https://hal.archives-ouvertes.fr/hal-00921633/document)
- Applications & Libraries
  - [A simple approach to building a real-time collaborative text editor](http://digitalfreepen.com/2017/10/06/simple-real-time-collaborative-text-editor.html)
  - http://y-js.org/
  - http://swarmdb.net/
  - http://gun.js.org/
  - http://scuttlebot.io/
  - https://github.com/mafintosh/hyperlog
  - https://github.com/jboner/akka-crdt

### Known shortcommins of existing solutions
> What are the limitations on those solutions?

Given the sheer amount of solutions and experiments to solve the multiple challenges of this Open Problem, one common shortcomming is actually a qualitative and quantitative assessement of what is possible today and how these stack up to existing centralized versions and if they are capable or not to take on the load to fulfil the needs of the multiple use cases.

## Solving this Open Problem

### What is the impact

Mutable Data is a fundamental building block for creating applications. Improving the existing solutions and creating new ones that will support a large number of users in a distributed context, will enable the entry of the large growing number of Internet users to be part of the dWeb.

### What defines a complete solution?
> What hard constraints should it obey? Are there additional soft constraints that a solution would ideally obey?

A complete solution should repetably demonstrate that it can sustain the load under multiple network conditions for use cases that in the following categories:

- writers (i.e. publishers) to readers (i.e. consumers)
  - 1 to many - Blog
  - some to many - Code Repository
- writers/readers to writers/readers
  - 1 to 1 - Chat
  - few to many - Collaborative Documents
  - many to many - Social Network, Forums, Chat Rooms

More use cases defined at https://github.com/libp2p/research-pubsub/blob/master/USECASES.md

## Other

### Existing Conversations/Threads

- [IPNS Improvement Design Exploration](https://github.com/ipfs/notes/issues/260)
- [DNS over IPFS](https://github.com/ipfs/notes/issues/250)
- [IPFS database: pubsub, consistency and persistence](https://github.com/ipfs/notes/issues/244)
- [Implement databases over IPFS using Persistent Balanced Trees](https://github.com/ipfs/notes/issues/161)
- [Petnames for multihashes](https://github.com/ipfs/notes/issues/157)
- [pluggable resolvers](https://github.com/ipfs/notes/issues/127)
- [Optimizing IPNS](https://github.com/ipfs/notes/issues/109)
- [Aggregation --> CRDTs discussion](https://github.com/ipfs/notes/issues/40)

### Extra notes
