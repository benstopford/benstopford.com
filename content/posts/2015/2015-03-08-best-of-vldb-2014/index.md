---
title: "Best of VLDB 2014"
date: 2015-03-08
categories: 
  - "blogroll"
  - "products"
slug: "best-of-vldb-2014"
---

[REWIND: Recovery Write-Ahead System for In-Memory Non-Volatile Data-Structures](http://www.vldb.org/pvldb/vol8/p497-chatzistergiou.pdf)

Interesting paper on write ahead logs in persistent in memory media. Recent non-volatile memory (NVM) technologies, such as PCM, STT-MRAM and ReRAM, can act as both main memory and storage. This has led to research into NVM programming models, where persistent data structures remain in memory and are accessed directly through CPU loads and stores. REWIND outperforms state-of-the-art approaches for data structure recoverability as well as general purpose and NVM-aware DBMS-based recovery schemes by up to two orders of magnitude.

[Storage Management in Asterix](http://www.vldb.org/pvldb/vol7/p841-alsubaiee.pdf)

Asterix is an academically established hierarchical store. It's now an Apache Incubator project. It utilises sets of LSM structures, tied transactionally together. Additional index structures can also be formed, for example R-Trees.

[Staring into the Abyss: An Evaluation of Concurrency Control with One Thousand Cores](http://www.vldb.org/pvldb/vol8/p209-yu.pdf)

As the number of cores increases, the complexity of coordinating competing accesses to data will likely diminish the gains from increased core counts.We conclude that rather than pursuing incremental solutions, many-core chips may require a completely redesigned DBMS architecture that is built from ground up and is tightly coupled with the hardware.

<!--more-->

[E-Store: Fine-Grained Elastic Partitioning for Distributed Transaction Processing Systems](http://www.vldb.org/pvldb/vol8/p245-taft.pdf)

OLTP DBMS need to be elastic; that is, they must be able to expand and contract resources in response to load fluctuations and dynamically balance load as hot tuples vary over time. This paper presents E-Store, an elastic partitioning framework for distributed OLTP DBMSs. It automatically scales resources in response to demand spikes, periodic events, and gradual changes in an application’s workload. E-Store addresses localized bottlenecks through a two-tier data placement strategy: cold data is distributed in large chunks, while smaller ranges of hot tuples are assigned explicitly to individual nodes. This is in contrast to traditional single-tier hash and range partitioning strategies.

[Large-Scale Distributed Graph Computing Systems](http://www.vldb.org/pvldb/vol8/p281-lu.pdf) An Experimental EvaluationGood coverage of different systems for distributed graph computation, including reflection on why some work better than others. Interesting. 

[Faster Set Intersection with SIMD instructions by Reducing Branch Mispredictions](http://www.vldb.org/pvldb/vol8/p293-inoue.pdf)

This paper describes our new algorithm to efficiently find set intersections with sorted arrays on modern processors with SIMD instructions and high branch misprediction penalties. The key insight for our improvement is that we can reduce the number of costly hard-to-predict conditional branches by advancing a pointer by more than one element at a time. Although this algorithm increases the total number of comparisons, we can execute these comparisons more efficiently using the SIMD instructions and gain the benefits of the reduced branch misprediction overhead. Also see [Improving Main Memory Hash Joins on Intel Xeon Phi Processors: An Experimental Approach](http://www.vldb.org/pvldb/vol8/p642-Jha.pdf)

[Memory-Efficient Hash Joins](http://www.vldb.org/pvldb/vol8/p353-barber.pdf)

A new hash tables for joins, and a hash join based on them, that consumes far less memory and is usually faster than recently published in-memory joins. The join mechanism is not restricted to outer tables that fit wholly in memory. The key to this hash join is a new concise hash table (CHT), a linear probing hash table that has 100% fill factor, and uses a sparse bitmap with embedded population counts to almost entirely avoid collisions. This bitmap also serves as a Bloom filter for use in multi-table joins. Our experiments show that we can reduce the memory usage by one to three orders of magnitude, while also being competitive in performance.

[General Incremental Sliding-Window Aggregation](http://www.vldb.org/pvldb/vol8/p702-tangwongsan.pdf)

This paper presents Reactive Aggregator (RA), a new framework for incremental sliding-window aggregation. RA is general in that it does not require aggregation functions to be invertible or commutative, and it does not require windows to be FIFO. We implemented RA as a drop-in replacement for the Aggregate operator of a commercial streaming engine.

[Persistent B+-Trees in Non-Volatile Main Memory](http://www.vldb.org/pvldb/vol8/p786-chen.pdf)

A look at the application of B+- trees optimisation to provide efficient retrieval algorithms for Phase Change Memory structures.

[Understanding the Causes of Consistency Anomalies in Apache Cassandra](http://www.vldb.org/pvldb/vol8/p810-fan.pdf)

A recent paper on benchmarking eventual consistency showed that when a constant workload is applied against Cassandra, the staleness of values returned by read operations exhibits interesting but unexplained variations when plotted against time. In this paper we reproduce this phenomenon and investigate in greater depth the low-level mechanisms that give rise to stale reads. We show that the staleness spikes exhibited by Cassandra are strongly correlated with garbage collection, particularly the “stop-the-world” phase which pauses all application threads in a Java virtual machine. We show experimentally that the staleness spikes can be virtually eliminated by delaying read operations artificially at servers immediately after a garbage collection pause. In our experiments this yields more than a 98% reduction in the number of consistency anomalies that exceed 5ms, and has negligible impact on throughput and latency.

[MRCSI: Compressing and Searching String Collections with Multiple References](http://www.vldb.org/pvldb/vol8/p461-wandelt.pdf)

Efficiently storing and searching collections of similar strings, such as large populations of genomes or long change histories of documents from Wikis, is a timely and challenging problem. We then propose three heuristics for computing Multi-Reference Compressed Search Indexes, achieving increasing compression ratios. Compared to state-of-the-art competitors, our methods target an interesting and novel sweet-spot between high compression ratio versus search efficiency.
