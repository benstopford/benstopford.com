---
title: "Data Storage for Extreme Use Cases"
date: 2011-12-13
categories: 
  - "talks"
slug: "data-storage-for-extreme-use-cases-the-lay-of-the-land-and-a-peek-at-odc-slides-and-notes-for-advanced-database-course"
---

This text is adapted from the guest lecture given on the Advanced Databases Course at Birkbeck. Full slides available here: [http://www.slideshare.net/benstopford/advanced-databases-ben-stopford](http://www.slideshare.net/benstopford/advanced-databases-ben-stopford)

**[Advanced databases ben stopford](http://www.slideshare.net/benstopford/advanced-databases-ben-stopford "Advanced databases ben stopford")**     

View more [presentations](http://www.slideshare.net/) from [Ben Stopford](http://www.slideshare.net/benstopford)

**Introduction**

Comp Sci 101 normally includes something about the simplest and most efficient ways to hold and access data being via a Hashmap. Hashmaps provide rapid key based access to data – up to 20 nanoseconds for a fast implementation in Java. This speed is largely due to the structure sitting wholly in memory, allowing the computer to take advantage of its various layers of caching to optimise performance. In fact a hashmap lookup will complete in around the time it takes a light beam to travel around a typical room. That’s pretty fast!

Querying a database is a somewhat different affair. There are more steps for a start, far more codeto be executed, the OS gets involved, as will the network, and of course a disk. This brings a simple database query in at around the 20 milliseconds mark. That’s a big difference to our hashmap; around six orders of magnitude!

A comparison between these two is of course unfair, but it highlights the importance of mechanical sympathy when thinking about how we store our data.We need to be aware of the performance characteristics of each element of our systembecause each extra step costs performance. In fact there are two key factors that separate the database and the hashmap. First they are physically very different: One being a single process and one a variety of processes and a variety of steps. Secondly they are functionally different: the database provides far more functionality that the hashmap.

Modern times have brought with them a huge array of different data storage system. These systems are built using a variety of architectures, differentiated by different physical characteristics. This allows them to take different positions on the performance trade-off curve.

The onset of these new technologies has prompted some pretty vocal debate about the applicability of the traditional database architecture, characterised by row-oriented operations on a magnetic disk. Michael Stonebraker, a leading database expert, puts it quite bluntly:

_“Because RDBMSs can be beaten by more than an order of magnitude on the standard OLTP benchmark, then there is no market where they are competitive. As such, they should be considered as legacy technology more than a quarter of a century in age, for which a complete redesign and re-architecting is the appropriate next step.”_

The point he makes is that, if performance is truly a factor (and the data size and population are appropriate), solutions that change the architectures are more likely to win-out. In the wealth of solutions available today there are a few common themesand we’ll elaborate on these next.

**Simplifying the Contract &NoSQL**

One of the most recent, and pertinent, developments has been the idea of simplifying the contract. For some years data-storage has been synonymous with the implementation of ACID. However the last few years have seen a notable move away from ACID when dealing with very large data-sets where the amount of distribution required makes implementing ACID prohibitive. What’s more many applications simply don’t require these levels of guarantee. This brings us to the idea of _simplifying the contract_. The Internet currently contains around 5 Exabytes of data. That is a fantastically large amount, certainly in database terms. By comparison the average enterprise database is around 1 terabyte (based on research in 2009). The point is simple: the context of data management has changed and for those dealing in high-web scale data volumes simplifying the contract is absolutely mandatory.

An interesting development of the last few years has been the, rather poorly named NoSQL movement. If the name were indicative of anything it would be a (not so subtle) hint that the movers and shakers in early NoSQL technology were keen to shrug off the constraints of traditional data storage. In fact the early NoSQLstores like Voldamort and Cassandra really grew out of a simply storing data in lots of files, in an attempt to gain the scalability of simple “sharded” storage.

The idea of simplifying the contract is not limited solely to distributed datastores. Even traditional databases, residing on a single machine, have large operational overheads (with one piece of research suggesting less than 10% of instructions contribute to “useful” work).

If there is a point it is this: as you increase the level of distribution (needed to process large data sets) the practicality of implementing ACID starts to spiral out of control.

However the risks of dropping ACID, in particular embracing eventual consistency, should not be taken lightly. Drop ACID because you have to, not because you think DBAs are yesteryear weirdos that wear sandals and attach their blackberries to their belts ;-)

**The Three Directions of Database Progression**

The are essentially three mechanisms for providing better performance over the ‘traditional’ database architecture (and I’ve tacked a fourth on the end – you’ll see why later):

- Shared disk: Several machines share a single shared disk array. Popular for mid-range data sets; but problem of disk/lock contention. Oracle RAC is a good example but there are many more in the enterprise space.
- Shared nothing – characterised by partitioning the data across different machines so that each node has complete autonomy over the data it holds; more scalable; popular for high-end data sets. Big Data era has provided a need for this architecture. But limited by performance of joins across different nodes.
- In-memory database – everything in a single address space. Query planning less important as the penalty of getting it wrong is not as crippling as in disk-based systems. The speed improvement comes from memory being at least 100 times faster than disk, as well as it being far better suited to random access c.f. TPC-H benchmark results \[1\]. The problem is that the address spaces is relatively small and of a fixed size (but rapidly growing over recent years from GBs to TBs). Also, there is the Durability issue of main-memory databases.
- A solution to the two problems with in-memory is to add distribution. Distributed, in-memory, shared-nothing architectures solve both the ‘one more bit’ problem as well as enabling durability. Fixed data space being solved by simply adding more machines, and durability by keeping backups elsewhere in the cluster.  The downside however is that we have lost the single address space and all the advantages that go with it.

**ODC – RBS’s In-Memory Datastore**

(A better textual version can be found at \[2\] and a video covering this can be found at \[3\])

ODC is RBS’s in-memory data-storage solution, built on Oracle Coherence. ODC occupies an interesting position on the performance trade-off curve: Being in-memory makes it very low latency whilst being distributed, shared-nothing allows it to be high throughput. The downside is the cost of all the RAM storage.

ODC uses an interesting approach to a problem that plagues all shared-nothing data stores: the distributed join problem. This occurs when data that lives on different nodes must be joined together across the network – with the network “hops” associated with transferring the intermediary results degrading its performance.

One approach to this distributed join problem is to denormalise so that related ‘rows’ (or object graphs in our case) are always bound together. There is no need to bind them across the network because all relations are held in one row (or object graph). This is great for reducing communication costs, but hugely increases the amount of data duplication, particularly when data is versioned. The consequence is that a lot of memory is used up (memory being something of a commodity in in-memory solutions, even today). There is an additional problem of maintaining the replicated data – more specifically the issue of needing large shared locks across the multiple replicas.

So what we really want is all the advantages of normalised data with the speed of denormalisation!

The solution to this problem has two stages:

The first is to use (or rather bastardise) a Snowflake structure (of the type typical in Data Warehouse schemas) to collocate records that have the same keys. “Fact Table” records are spread across the cluster of machines while “Dimension Table” records are replicated at all nodes. Fact tables are generally much larger than Dimension tables, which is the reason that it is reasonable to replicate them.

This is best demonstrated with a simple example: Consider you have are building an online shopping application (think Amazon). Let’s say we decide to partition (“shard”) by userId. The “facts” of the system would be your basket, orders, order delivery details etc. All facts that are specific to _you_ (i.e. to one userId) and hence can be collocated on the same machine by using userId in the hash function that specifies where data is held in the network (the well known hashing algorithm). The “dimensions” provide the context for the “facts”. Some of this context will be user-specific – like your address -  but other items would be shared across many users - like the list of products the site sells. Dimensions, for example the list of products, have keys that ‘crosscut’ the key used to partition the facts, that is to say that it is not possible to uniquely partition products so that they are collocated with orders because the keys simply don’t ‘line up’. This inability to collocate Dimensions leads us to the cross-network joins we are trying to avoid.

The solution is simple: Partition facts and Replicate Dimensions. By doing so any join is possible without the need for network “hops” (i.e. no distributed joins) because all the related records are collocated at one network node.

However there is a problem, the solution to which brings us to that second stage mentioned above. It is inevitable that all Dimensions will not play to our nice heuristic. In fact, in reality, some Dimension tables will be quite large.  Because they are replicated, large dimension tables are impractical due to the total memory they will consume across the cluster.

The solution is to make use of the “Connected Replication” pattern.  This simply tracks whether, at a point in time, a certain Dimension record is ‘connected’ (via some path of foreign keys) to a Fact record in the database. Put another way it tracks whether a dimension record is actually _used_. This ‘trick’ works because, in reality, much of the Dimension data we hold is not actually used. In fact one recent study showed that 80% of the data we hold is no longer used. By implementing a simple, recursive process that navigates the hierarchy of foreign key relationships when data is inserted we can track which dimension records are used and which are not. This ‘trick’ reduces the cost of replicated storage to around 10% of its original size and by doing so really makes the idea of replicating dimensions practical in in-memory architectures.

**In conclusion:**

- Traditional database architectures are inappropriate for applications that require very low latency or very high throughput.
- At one end of the scale are the huge shared-nothing architectures, favouring scalability.
- At the other end are in-memory architectures, leveraging the simplicity and speed of a single address space.
- You can blend the two approaches (as, for example, in ODC).
- ODC attacks the Distributed Join Problem in an unusual way: By balancing Replication and Partitioning we can do any join in a single step. Connected Replication adds an additional ‘twist’ that reduces the amount of data replicated by an order of magnitude, making replication in an in-memory architecture practical.

**References**

\[1\] [http://www.tpc.org/tpch/results/tpch\_perf\_results.asp?resulttype=cluster](http://www.tpc.org/tpch/results/tpch_perf_results.asp?resulttype=cluster)

\[2\] [/2011/09/22/achieving-fast-joins-in-distributed-data-stores-through-the-application-of-snowflake-schemas-and-the-connected-replication-pattern-2/](/2011/09/22/achieving-fast-joins-in-distributed-data-stores-through-the-application-of-snowflake-schemas-and-the-connected-replication-pattern-2/)

\[3\] [http://www.infoq.com/presentations/ODC-Beyond-The-Data-Grid](http://www.infoq.com/presentations/ODC-Beyond-The-Data-Grid)

**Further Reading**

An fantastic paper covering many of the issues. Strongly recommended:

- The End of An Architectural Era (It’s Time for a Complete Rewrite), M. Stonebraker et al. VLDB 2007, pp 1150-1159. At [http://www.vldb.org/conf/2007/papers/industrial/p1150-stonebraker.pdf](http://www.vldb.org/conf/2007/papers/industrial/p1150-stonebraker.pdf)

Good blog to follow:

- [http://dbmsmusings.blogspot.com/](http://dbmsmusings.blogspot.com/)

Related modern database technologies:

- [http://www.exasol.com/en/home.html](http://www.exasol.com/en/home.html)
- http://www.paraccel.com/

Related articles from me:

- [/2009/12/06/are-databases-a-thing-of-the-past/](/2009/12/06/are-databases-a-thing-of-the-past/)
- [/2009/11/24/understanding-the-shared-nothing-architecture/](/2009/11/24/understanding-the-shared-nothing-architecture/)
- [/2011/08/14/distributed-storage-phase-change-memory-and-the-rebirth-of-the-in-memory-database/](/2011/08/14/distributed-storage-phase-change-memory-and-the-rebirth-of-the-in-memory-database/)
- [/2011/01/27/beyond-the-data-grid-building-a-normalised-data-store-using-coherence/](/2011/01/27/beyond-the-data-grid-building-a-normalised-data-store-using-coherence/)
