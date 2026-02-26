---
title: "A Guide to building a Central, Consolidated Data Store for a Company"
date: 2014-12-02
categories: 
  - "blogroll"
  - "products"
slug: "a-guide-to-building-a-central-consolidated-data-store-for-a-company"
---

Quite a few companies are looking at some form of centralised operational store, data warehouse, or analytics platform. The company I work for set out to build a centralised scale-out operational store using NoSQL technologies five or six years ago and it's been an interesting journey. The technology landscape has changed a lot in that time, particularly around analytics, although that was not our main focus (but it will be an area of growth). Having an operational store that is used by many teams is, in many ways, harder than an analytics platform as there is a greater need for real-time consistency. The below is essentially a brain dump on the subject. 

**On Inter-System (Enterprise) Architecture**

1. Assuming you use some governed enterprise messaging schema: if it ends up just being an intermediary for replicating from one database to another, then you'll be in for trouble in the long run (see [here](/2014/05/01/enterprise-messaging-is-the-approach-flawed/)). Make something the system of record. Replicate that as a stream of changes. Ideally, make it a database replication stream via goldengate or similar so it's exactly what is in the source database. 
2. Following this - clone data from a database transaction log, rather than extracting it and manually transforming to a wire format. The problem here is the word 'manually'.
3. Prefer direct access at source. Use data virtualisation if you can get it to work.
4. Federated approaches, which leave data sets in place, will get you there faster if you can get all the different parts of the company to conform. That itself is a big ask though, but a good technical strategy can help. Expect to spend a lot on operations and automation lining disparate datasets up with one another.
5. When standardising the persisted representation don’t create a single schema upfront if you can help it. You’ll end up in EDW paralysis. Evolve to it over time.
6. Start with disparate data models and converge them incrementally over time using schema-on-need (and yes you can do this relationally, it’s just a bit harder).

<!--more-->

**On building a Multi-tenanted Read-Write store**

1. Your goal, in essence, is to appear like a single store from the outside but with performance and flexibility that simulates (or is) a dedicated instance for each customer. It works best to think federated from the start and build centralisation in, not the other way around.
2. Utilise off the shelf products as much as possible. NoSQL products, in particular, are often better suited to this use case, but know their limitations too (see technology choice later)
3. Do everything you can to frame your use case to _not_ be everything a traditional database has to do, at least holistically, but you will probably end up having to do the majority of it anyway, at least from the point of view of a single customer.
4. Question whether you really need a synchronous consolidation point for writes. It's much easier to scale asynchronous replicas.
5. Use _both_ sharding and read-replicas to manage query load. Sharding scales key-value read and write throughput linearly, replication scales complex (non-key-value) query load linearly (at the cost of write performance if it’s synchronous). You need primitives for _both_ sharding and replication to scale non-trivial workloads.
6. Help yourself by grouping actors as either Read-Only or Read-Write for different data sets. Read-Only users can generally operate on an asynchronous dataset. This removes them from the global-write-path and hence avoids them contributing to the bottleneck that forms around it. Again, question whether you really need a single consolidation point for writes. 
7. This is important enough to restate: leverage both sharding and replication (both sync and async). Make async the default. Use sharding + synchronous replicas to scale complex query load on read-write datasets. Use async for everything else. Place replicas on different hardware to provide resource isolation. Use this to create a ‘store-of-stores’ model that mixes replicas (for workload isolation) with sharding (for scale out in a replica).
8. Include a single event stream (log); one that exactly represents the entire stream of state. This should serve both as your asynchronous replication stream, but also as a basis for notification so you can listen as easily as you can query.
9. If you provide notifications on asynchronous replicas use a proxy, located on each replica, to republish events so that the read and notification ‘views’ line up temporally. This allows clients to listen to the replicas they are reading from without race conditions between events and data being present in a replica. A prerequisite for this is consistent timestamping (covered below).   
10. Leverage schema-on-need. This is a powerful concept for a centralised store as it provides an incremental approach for conformity. It gets you there far faster than schema-upfront approaches. I cannot overstate how useful this is and is the backing for concepts like Data Lake etc.
11. However, if you take the schemaless/on-need route be warned: historic data will need to be ‘migrated’ as the schema of the data changes, else programs will have to support different schemas or simply won't work with old data. You can do this with different sets of ‘late-bindings’ but the eventually migration is always needed so make sure you’re tooled up for this. 
12. So... provision a mechanism for schema migration, else new programs won’t work with older data (note that schema migration doesn’t imply an _upfront_ schema. It does imply a schema for each entity type though).
13. Accept that all non-freeform data has a schema itself, whether you declare it up-front, on-need or not-at-all.
14. Leverage the difference between query-schema and data-schema (query- being a subset of the data-schema) to minimise coupling to the store itself (all stores which utilise indexes will need a query schema as a minimum).
15. Even if you are schemaless, grow some mandatory schema over time, as data stabilises. Schemas make it much easier for you to manage change when you have more customers.
16. Whether you have a schema or not, data will change in a non-backwardly compatible way over time. Support this with two schemas (or data sets) visible concurrently to allow customers to upgrade using a _rolling window_ rather than individual, lock-step releases.
17. If you have to transform data on the way in, keep hold of the original in the store and give it the same key/versioning/timestamping so you can refer back to it. Make this original form a first class citizen.
18. Apply the single writer principal wherever possible so only one source masters a certain dataset. Often this won’t be possible but do it wherever you can. It will allow you to localise/isolate their consistency concerns and leverage asynchronicity where consumption is off the write path.
19. Don’t mix async inputs (e.g. messages that overwrite) with synchronous updates (edits) on the same entity. At best people will get very confused. If you have to mix them, hold them separately and version each. Expose this difference between these updates/overwrites on your API so they can at least be  specified declaratively to the user.
20. Leverage the fact that, in a collaborative store, atomaticity is a requirement but consistency can be varied. That is to say that different observers (readers not writers) can lock into different temporal points and see an atomic stream of changes. This alleviates the need for a single, global synchronisation on read. This only works if systems don’t message one another directly, else you’ll get race conditions but releases you from global transactional locks and that’s often worth the tradeoff, particularly if you’re crossing data-centre boundaries.   
21. Make data immutable. Timestamp and version everything (validFrom-validTo etc). Build this into the very core of the system. Flag the latest record so you don’t always have to read all versions to get the latest. Keep all changes if you can afford the storage. It will allow you to look back in time. But know that temporal indexes are the least-efficient / most-complex-to-manage index type (generally require temporal partitioning).
22. Applying time consistently in a distributed environment requires synchronisation on a central clock (don’t rely on NTP). As a central clock is not always desirable, consider using epochs (consistent periods) which are pushed to each node so to define global read-consistent periods without explicit synchronisation (but at a courser granularity of ‘tick’). See [here](/2012/05/09/cluster-time-and-consistent-snapshotting/). 
23. Don’t fall into the relational trap of splitting entities just because they are polystructured and don’t fit in a single row. Hold an entity separately only where real world  entities they represented vary independently.
24. In tension with that, don’t denormalise data from different sources on the write path (i.e. using aggregates), if those aggregates contain many->1 relationships that do change independently. It will make writing atomically more difficult as writes must update multiple denormalised entities. Prefer joins to bring together the data at runtime or use aggregates in reporting replicas.
25. Provide, as a minimum, multi-key transactions in the context of a master replica. This will require synchronisation of writes, but it is possible to avoid synchronisation of reads.   
26. Access to the data should be declarative (don’t fall into the trap of appending methods to an API to add new functionality). Make requests declarative. Use SQL (or a subset of) if you can.
27. Know that building a platform used by different teams is much harder than building a system. Much of the extra difficulty comes from peripheral concerns like testing, automation, reliability, SLAs that compound as the number of customers grow. 
28. Following from this think about the customer development lifecycle early, not just your production system. Make environments self-service. Remember data movement is network limited and datasets will be large.
29. Testing will hurt you more and more as you grow. Provide system-replay functions to make testing faster.
30. Your value in a company is based mostly on a perception of your overall value. If you’re doing a platform-based data consolidation project you will inevitably restrict what they can do somewhat. Focus on marketing and support to keep your users happy.

**On Technology Choice**

1. Use off the shelf products as much as possible.
2. The relational model is good for data you author but not so good for data sourced from elsewhere (data tends to arrive polystructured so is best stored polystructured).
3. Be wary of pure in-memory products and impressive RAM-centric benchmarks. Note their performance as data expands onto disk. You always end up with more data than you expect and write amplification is always more than vendors state.
4. Accept that, despite the attraction of a polystructured approach, the relational model is a necessity in most companies, at least for reporting workloads, so the wealth of software designed to work with it (and people skilled in it) can be leveraged.
5. NoSQL technologies are stronger in a number of areas, most notably:
    1. scalability for simple workloads,
    2. polystructured data,
    3. replication primitives,
    4. continuous availability,
    5. complex programmable analytics.
6. Relational technologies are stronger at:
    1. keeping data usable for evolving programs over long periods of time,
    2. transactional changes,
    3. pre-defined schemas,
    4. breadth of query function,
    5. analytical query performance.
7. In effect this ends up being: use the right tool for the right job, refer to 5/6 with as few technologies as you can survive with.
