---
title: "The Best of VLDB 2012"
date: 2012-10-28
categories: 
  - "products"
slug: "the-best-of-vldb-2012"
---

Here are some of the highlights of the 210 papers presented at VLDB earlier this year. You can find the full list [here](http://www.vldb.org/pvldb/vol5/).

**Solving Big Data Challenges for Enterprise ApplicationPerformance Management** ([here](http://vldb.org/pvldb/vol5/p1724_tilmannrabl_vldb2012.pdf))

This paper includes a detailed analysis of the performance characteristics of Cassandra, HBase, Redis, VoltDB, MySQL Cluster and is the most comprehensive comparison I've come across.

**From Cooperative Scans to Predictive Buffer Management** ([here](http://arxiv.org/pdf/1208.4170.pdf))

Intriguing paper from the Vectorwise guys for improving IO efficiency under load. LRU/MRU caching policies are known to break down under large, concurrent workloads. SQL Server and DB2 both have mechanisms for sharing IO between queries (by attaching to an existing scan or throttling faster queries so that IO can be shared). The Cooperative Scans discussed here takes this a step further by incorporating an active buffer manager which scans use to register their interest in data. The manager then adaptively chooses which pages to load and pass to the various concurrent requests.

There is another related paper at this conference SharedDB: Killing One Thousand Queries With One Stone ([here](http://www.vldb.org/pvldb/vol5/p526_georgiosgiannikis_vldb2012.pdf))

**Processing a Trillion Cells per Mouse Click (Google)** ([here](http://www.vldb.org/pvldb/vol5/p1436_alexanderhall_vldb2012.pdf))

Interesting paper from Google suggesting an alternative to the approach to column orientation taken in Dremel. PowerDrill uses a double-dictionary encoded column store where the encodings live largely in memory. Further optimisations are made at load time to ensure minimal access to persistent storage. This makes it more akin to column stores like ParAccel or Vectorwise, applied to analytical workloads (aggregates, group bys etc).

**Can the elephants handle the NoSQL onslaught** ([here](http://vldb.org/pvldb/vol5/p1712_avriliafloratou_vldb2012.pdf))

Another paper comparing the performance of Hadoop with a relational database (in a similar vein to the Sigmod 09 paper DeWitt published previously [here](http://www.cse.nd.edu/~dthain/courses/cse598z/spring2010/benchmarks-sigmod09.pdf)). I sympathise with the message - databases outperform hadoop on small to medium workloads - but I hope that most people know that already. This time the comparison is with Microsoft's Sql Server PDW (Parallel Data Warehouse). The choice of data sizes between 250Gb and 16TB means that the study has the same failing as the previous Sigmod one; it's not looking at large dataset performance.

**Interactive Query Processing in Big Data Systems: A Cross-Industry Study of MapReduce Workloads** ([here](http://www.eecs.berkeley.edu/~alspaugh/papers/mapred_workloads_vldb_2012.pdf))

Useful, empirically driven paper with detailed data sets from a number of NoSQL implementations including Facebook. Chen et al. performed an empirical study on the implementation of Hadoop at a number of companies including Facebook. It hints at the current 'elephant in the room' that is Hadoop's focus on batch-time over real-time performance (roll on [Impala](https://github.com/cloudera/impala)!) .  Having data of this level of granularity over a range of real time systems in itself is quite valuable. They note that 90% of jobs are small (resulting in MBs of data returned).

**High-Performance Concurrency Control Mechanisms for Main-Memory Databases** ([here](http://www.vldb.org/pvldb/vol5/p298_per-akelarson_vldb2012.pdf))

Proposes an optimistic MVCC method for in memory concurrency control. The conclusion: single-version locking performs well only when transactions are short and contention is low; higher contention or workloads including some long transactions favor the multiversion methods, and the optimistic method performs better than the pessimistic one.

**Blink and It’s Done: Interactive Queries on Very Large Data** ([here](http://www.vldb.org/pvldb/vol5/p1902_sameeragarwal_vldb2012.pdf))

Blink is different to the mainstream database as it's not designed to give you an exact answer. Instead you specify either error (confidence) or maximum time constraints on your query. The approach uses a number of sampling based strategies to achieve the required confidence level. There is a related paper: Model-based Integration of Past & Future in TimeTravel ([here](http://www.vldb.org/pvldb/vol5/p1974_mohamedekhalefa_vldb2012.pdf))

**Developing and Analyzing XSDs through BonXai** ([here](http://www.vldb.org/pvldb/vol5/p1994_wimmartens_vldb2012.pdf))

This one struck a cord with me as I'm not the biggest fan of xsd. Bonxai provides and expression rather than type based approach to defining the data schema. More info [here](http://doclib.uhasselt.be/dspace/bitstream/1942/1957/1/dbpl07.pdf) and [here](http://ls1-www.cs.tu-dortmund.de/~niewerth/bonxai/bonxai-specification.pdf).

**B+-tree Index Optimization by Exploiting Internal Parallelism of Flash-based Solid State Drives** ([here](http://www.vldb.org/pvldb/vol5/p286_hongchanroh_vldb2012.pdf))

SSDs performance increases (initially) with the number of concurrent executions (in stark contrast with magnetic drives). This paper looks into maximising this with the use of concurrent B-trees that utalise parallel IO. Useful research as flash is only going to get cheaper.

**SCOUT: Prefetching for Latent Structure Following Queries** ([here](http://infoscience.epfl.ch/record/176914/files/scout-cr.pdf))

I quite like the ideas in this paper around prefetching data based on a known structure (probably because it's similar to some of the stuff we do).

**Fast Updates on Read-Optimized Databases Using Multi-Core CPUs** ([here](http://www.vldb.org/pvldb/vol5/p061_jenskrueger_vldb2012.pdf))

Addresses the problem some columnar architectures suffer where they accumulate writes in a separate partition, which must be periodically merged with the read-optimised main one.

**FDB: A Query Engine for Factorised Relational Databases** ([here](http://www.vldb.org/pvldb/vol5/p1232_nurzhanbakibayev_vldb2012.pdf))

I hadn't come across the idea of Factorised Databsaes before. An interesting concept. The paper demonstrates performance improvements over traditional methods for many-to-many join criteria.

**Only Agressive Elephants are Fast Elephants** ([here](http://www.vldb.org/pvldb/vol5/p1591_jensdittrich_vldb2012.pdf))

Interesting approach to indexing Hadoop that claims to improve both read and write performance. I couldn't find the code though so couldn't try it.

**The Vertica Analytic Database: C-Store 7 Years Later** ([here](http://vldb.org/pvldb/vol5/p1790_andrewlamb_vldb2012.pdf))

A good summary of this mature shared-everything, columnar database. They discuss their use of super projections over join indexes, due to the overheads associated with tuple construction and the verbosity of storing the associated rowids. There is a summary of the encoding types used as well as partitioning and locking strategies.

**Muppet: MapReduce-Style Processing of Fast Data** ([here](http://www.vldb.org/pvldb/vol5/p1814_wanglam_vldb2012.pdf))

Whilst the majority of MapReduce commentary focuses on improving MR query performance this paper looks at the problem of injesting data quickly for high throughput, streaming workloads. The interesting approach focuses on data as streams (in and out) in association with a moving historical window (they denote a slate). To me there seems to be a lot of similarity between this approach the one taken by products like [StreamBase](http://www.streambase.com/) and [Cloudscale](http://www.cloudscale.com/) but the authors differentiate themselves my being less schema oriented, more akin to the traditional MR style.

**Serializable Snapshot Isolation in PostgreSQL** ([here](http://drkp.net/drkp/papers/ssi-vldb12.pdf))

Interesting paper on the implementation of serializable isolation using the snapshot model.

**Other papers of note:**

- Minuet: A Scalable Distributed Multiversion B-Tree ([here](http://www.vldb.org/pvldb/vol5/p884_benjaminsowell_vldb2012.pdf))
- A Statistical Approach Towards Robust ProgressEstimation ([here](http://research.microsoft.com/pubs/154936/Progress.pdf))
- Efﬁcient Multi-way Theta-Join Processing UsingMapReduce ([here](http://vldb.org/pvldb/vol5/p1184_xiaofeizhang_vldb2012.pdf))
- Avatara: OLAP for Web-scale Analytics Products (OLAP cubes over a NoSQL @LinkedIn) ([here](http://www.vldb.org/pvldb/vol5/p1874_liliwu_vldb2012.pdf))
- 10 Year Best Paper Award: Approximate Frequency Counts over Data Streams ([here](http://www.vldb.org/conf/2002/S10P03.pdf))
