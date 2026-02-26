---
title: "Sizing Coherence Indexes"
date: 2014-04-28
categories: 
  - "blogroll"
  - "coherence"
slug: "sizing-coherence-indexes"
---

This post suggests three ways to measure index sizes in Coherence: (a)Using the Coherence MBean (not recommended) (b) Use JMX to GC the cluster (ideally programatically) as you add/remove indexes - this is intrusive (c) Use the SizeOf invocable **(recommended)** - this requires teh use of a -javaagent option on your command line.

**(a) The Coherence MBean**

Calculating the data size is pretty easy, you just add a unit calculator and sum over the cluster (there is code to do that here: [test](https://github.com/benstopford/coherence-bootstrap/blob/master/src/com/benstopford/coherence/bootstrap/morecomplex/sizing/CountBinarySizeOfAllObjects.java), [util](https://github.com/benstopford/coherence-bootstrap/blob/3ba215f77e2e52d615ad926ecb3cdc8be9fcfcbe/src/com/benstopford/coherence/bootstrap/structures/tools/jmx/BinaryCacheSizeCounter.java)). Indexes however are more tricky.

Coherence provides an [IndexInfo](http://docs.oracle.com/cd/E15357_01/coh.360/e15723/appendix_mbean.htm#COHDG5471) MBean that tries to calculate the size. This is such an important factor in maintaining you cluster it's worth investigating.

Alas the IndexInfo Footprint is not very accurate. There is a test,[IsCoherenceFootprintMBeanAccurate.java](https://github.com/benstopford/coherence-bootstrap/blob/master/src/com/benstopford/coherence/bootstrap/morecomplex/sizing/IsCoherenceIndexFootprintMbeanAccurate.java),which demonstrates there are huge differences in some cases (5 orders of magnitude). In summary:

\- The Footprint is broadly accurate for fairly large fields (~1k) where the index is unique. - As the cardinality of the index drops the Footprint Mbean starts to underestimate the footprint. - As the size of the field being indexed gets smaller the MBean starts to underestimate the index.

Probably most importantly for the most likely case, for example the indexed fields is fairly small say 8B, and the cardinality is around half the count, the MBean estimate is out by three orders of magnitude.

Here are the results for the cardinality of half the count and field sizes 8B, 16B, 32B

```
     Ran: 32,768 x 32B fields [1,024KB indexable data], Cardinality of 512 [512 entries in index, each containing 64 values], Coherence MBean measured: 49,152B. JVM increase: 3,162,544B. Difference: 6334%
     Ran: 65,536 x 16B fields [1,024KB indexable data], Cardinality of 512 [512 entries in index, each containing 128 values], Coherence MBean measured: 40,960B. JVM increase: 5,095,888B. Difference: 12341%
     Ran: 131,072 x 8B fields [1,024KB indexable data], Cardinality of 512 [512 entries in index, each containing 256 values], Coherence MBean measured: 40,960B. JVM increase: 10,196,616B. Difference: 24794%
```

_**In short, it's too inaccurate to be useful.**_<!--more-->

**(b) Using JMX to GC before and after adding indexes**

So the we're left with a more intrusive process to work out our index sizes:

1. Load your cluster up with indexes.
2. GC a node and take it's memory footprint via JMX/JConsole/VisualVm
3. Drop all indexes
4. GC the node again and work out how much the heap dropped by.

I have a script which does this programatically via JMX. It cycles through all the indexes we have doing:

ForEach(Index) { GC->MeasureMemory->DropIndex->GC->MeasureMemory->AddIndexBack }

This method works pretty well although it takes a fair while to run if you have a large number of indexes, and is intrusive so you couldn't run in production. It also relies on our indexes all being statically declared in a single place. This is generally a good idea for any project. I don't know of a way to extract the ValueExtractor programatically from Coherence so just use the static instance in our code.

**(c) Use Java's Inbuilt Instrumentation**

This is the best way (in my opinion). It's simple and accurate. The only issue is that you need to start your coherence processes with a javaagent as it's using the Instrumentation API to size indexes.

The instrumentation agent itself is very simple, and uses the library found [here](http://sizeof.sourceforge.net/). All we need to wrap that is an invocable which executes it on each node in the cluster.

The invocable just loops over each cache service, and each cache within that service, calculating the size of the IndexMap using the instrumentation SizeOf.jar

To implement this yourself:

1) Grab these two classes: [SizeOfIndexSizer.java](https://github.com/benstopford/coherence-bootstrap/blob/master/src/com/benstopford/coherence/bootstrap/structures/tools/index/SizeOfIndexSizer.java), [IndexCountingInvocable.java](https://github.com/benstopford/coherence-bootstrap/blob/master/src/com/benstopford/coherence/bootstrap/structures/tools/index/IndexCountingInvocable.java) and add them to your classpath. The first sets the invocable off, handling the results. The second is the invocable that runs on each node and calculates the size of the index map.

2) Take a copy of SizeOf.jar from [here](http://sizeof.sourceforge.net/) and add -javaagent:<pathtojar>/SizeOf.jar to your command line.

3) Call the relevant method on SizeOfIndexSizer.
