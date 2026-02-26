---
title: "Balancing Replication and Partitioning in a Distributed Java Database @JavaOne"
date: 2011-10-05
categories: 
  - "blogroll"
  - "talks"
slug: "session-on-fast-joins-in-distributed-databases-javaone"
---

Here are a the slides for the talk I gave at JavaOne:

**Balancing Replication and Partitioning in a Distributed Java Database**

This session describes the ODC, a distributed, in-memory database built in Java that holds objects in a normalized form in a way that alleviates the traditional degradation in performance associated with joins in shared-nothing architectures. The presentation describes the two patterns that lie at the core of this model. The first is an adaptation of the Star Schema model used to hold data either replicated or partitioned data, depending on whether the data is a fact or a dimension. In the second pattern, the data store tracks arcs on the object graph to ensure that only the minimum amount of data is replicated. Through these mechanisms, almost any join can be performed across the various entities stored in the grid, without the need for key shipping or iterative wire calls.

**[Balancing Replication and Partitioning in a Distributed Java Database](http://www.slideshare.net/benstopford/balancing-replication-and-partitioning-in-a-distributed-java-database "Balancing Replication and Partitioning in a Distributed Java Database")** 

View more [presentations](http://www.slideshare.net/) from [Ben Stopford](http://www.slideshare.net/benstopford)

**See Also**

- [Original talk, given at QCon London, which is more Coherence specific](/2011/01/27/beyond-the-data-grid-building-a-normalised-data-store-using-coherence/)
- [\[pptx-9MB\]](/uploads/JavaOneTalk.pptx) [\[pdf-48MB\]](/uploads/JavaOneTalk.pdf)

 

- [A related post documenting the main points covered in the talk](/2011/09/22/achieving-fast-joins-in-distributed-data-stores-through-the-application-of-snowflake-schemas-and-the-connected-replication-pattern-2/)
