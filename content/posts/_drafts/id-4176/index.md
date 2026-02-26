---
title: "Stuff Worth Reading"
draft: true
---

### (doesn't include either papers or ibooks entries yet)

### Distributed Programming

- CAP 12 Years Later - http://www.infoq.com/articles/cap-twelve-years-later-how-the-rules-have-changed
- Scalability & Correctness - Covers the tradeoff between RPC and Message Passing Methods (3) http://steve.vinoski.net/pdf/IEEE-Convenience\_Over\_Correctness.pdf
- The Declarative Imperative: http://db.cs.berkeley.edu/papers/sigrec10-declimperative.pdf
- CALM  - http://www.bloom-lang.net/calm/
- Problems with simultaneity in distributed systems - ACM - http://queue.acm.org/detail.cfm?id=2745385

### Database Architectures & Approaches

- Intro to Distributed Hash Tables - http://www.freedomlayer.org/articles/dht\_intro.html
- Druid Sigmod - http://static.druid.io/docs/druid.pdf

### Database Transactions

- Highly Available Transactions - http://db.cs.berkeley.edu/papers/vldb14-hats.pdf
- CRDTs: Consistency without concurrency control - commutative use cases naturally diverge as a partitioned acyclic graph converges after a partitioning event - http://pagesperso-systeme.lip6.fr/Marc.Shapiro/papers/RR-6956.pdf
- Multi-key transactions in hyperdex - [http://hyperdex.org/papers/warp.pdf](http://hyperdex.org/papers/warp.pdf)
- Amazon on backoff in OCC - http://www.awsarchitectureblog.com/2015/03/backoff.html

### Non Relational Analytics

- Pathologies of Big Data, ACM - includes famous measurement of disk outperforming random memory access  - http://queue.acm.org/detail.cfm?id=1563874
- Spark in the works - Phd by M Zaharia - An Architecture for Fast and General Data Processing on Large Clusters - http://www.eecs.berkeley.edu/Pubs/TechRpts/2014/EECS-2014-12.pdf
- BigDataBenchmark -  https://amplab.cs.berkeley.edu/benchmark/
- Awesome Big Data links - https://github.com/onurakpolat/awesome-bigdata/blob/master/README.me

 

### Stream Processing

- Milwheel - Google's fault tolerant stream processor - http://static.googleusercontent.com/media/research.google.com/en//pubs/archive/41378.pdf

### Database Indexing

- LSM Trees
    - Original Log Structured Merge Tree paper [here](http://staff.ustc.edu.cn/~jpq/paper/flash/1996-The%20Log-Structured%20Merge-Tree%20%28LSM-Tree%29.pdf).
    - Yahoo's 2012 Sigmod adaptation of LSMT [here](http://www.eecs.harvard.edu/~margo/cs165/papers/gp-lsm.pdf).
    - Bigtable [here](file:///Users/benji/BensWorld/To%20Read/Papers/Data%20Storage/bigtable-osdi06%20\(1\).pdf).
    - Rose - LSM engine designed for replication  [here](http://www.vldb.org/pvldb/1/1453914.pdf).
- Data Structures for text sequences: https://www.cs.unm.edu/~crowley/papers/sds.pdf
- Stratified BTrees as Versioned Dictionaries - http://static.usenix.org/event/hotstorage11/tech/final\_files/Twigg.pdf

### Columnar Databases

- Integration of Ingres and Vectorwise Sigmod paper [here](http://www.sigmod.org/publications/sigmod-record/1109/pdfs/08.industry.inkster.pdf).
- Masters Thesis covering query optimisation in Vectorwise [here](http://homepages.cwi.nl/~boncz/msc/2012-AndreiCosteaAdrianIonescu.pdf).
- MonetDB's X100 [here](http://www.cidrdb.org/cidr2005/papers/P19.pdf).
- Alabdi's Design & Implementation of Column Oriented DBs - http://db.csail.mit.edu/pubs/abadi-column-stores.pdf

 

### Disks & SSDs

- HDD vs SSD 2008 -  http://www.pdsi-scidac.org/events/PDSW08/resources/papers/simsa\_PDSW.pdf

### Programming

- False sharing is covered beautifully here. http://daniel.mitterdorfer.name/articles/2014/false-sharing/
- Drepper's holistic coverage of how the memory heirachy affects programs - (5) http://www.akkadia.org/drepper/cpumemory.pdf
- Epic book by McKenny - Is Parallel Programming Hard, And, If So, What Can You Do About It? -  https://www.kernel.org/pub/linux/kernel/people/paulmck/perfbook/perfbook.2015.01.31a.pdf
- The art of unix usibility - http://www.catb.org/~esr/writings/taouu/html/

 

### Other

- Feynman Lectures - http://www.feynmanlectures.caltech.edu
