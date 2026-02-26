---
title: "Sample Answers: Are You an HPC Architect?"
date: 2009-11-21
slug: "sample-answers-are-you-an-hpc-architect"
url: "/sample-answers-are-you-an-hpc-architect/"
---

**Question 1: Worked Answer:**

_Observations:_

The point of this question is to illustrate how both Coherence and Datasynapse can individually solve this problem. However Datasynapse is the better solution. The team should consider:

- Offloading computation from the workstation.
- That the market data is only 30MB of data so would easily fit in a JVM. Thus it’s a good candidate to cache.
- The trade data set is large so needs to be stored in a partitioned cache (could not use a replicated topology).
- We want to minimise wire transfers where possible.

_A grid solution:_

- Set up a grid estate that connects to the Coherence cache.
- Use a 30MB near cache to ensure that once requested market data will be held on that engine.
- The grid task would load the trades it needs from the cache. Then use the locally cached market data to compute the various figures and return the result to the client.

_A non-grid solution:_

- The first aim would be to collocate the market and trade data on the same machine to ensure that you don’t have to ship data around to do processing. There are a couple of options open to you here. The first would be to put the market data in a replicated (rather than partitioned) cache. This would ensure it was resident on all server machines avoiding and wire calls. However this could be detrimental if the market data was very volatile. If this was the case it would be better to use a partitioned cache (rather than replicated) and set a data affinity between the market data identifier on the trade and the market data. This will encourage Coherence to collocate market data and trade data of the same type so they are on the same machine.
- Next you want to execute the processing on the machine which has all the correct data (trade and market). Based on the slides your best option would be to use an Entry Processor, operating on the trade, to route a processing call to the machine on which the trade and market data (hopefully) reside. Calculate the risk and return the result. This is the answer that you can come up with based on the content given in the talk. However it is flawed as concurrent requests for pricing on the same trade will queue on the server as Entry Processors are executed synchronously with respect to the key.The solution is instead to use an Invocable and send it to the right machine. You get the right machine via the getKeyOwner method of the PartitionedCache interface (cast your NamedCache object to this).

_Comments:_

In the almost all situations the grid solution would be preferable for this use case. The reasons are as follows:-

- Datasynapse includes functions that facilitate fine grained control of the computational environment. For example it has a UI for tracking submissions and diagnosing problems, is fault tolerant with well developed failover strategies, has machine scavenging, redundant scheduling etc. It also has a wealth of control functions such as discrimination, batching, priorities.
- The grid is far more scalable in terms of compute power (dynamic provisioning of engines etc).
- The grid is much cheaper (per core) than Coherence.

But – for equally sized clusters Coherence can process tasks faster (at lower latency) than the grid due its ability to collocate data and processing. The question is whether the latency penalty involved with shipping the data to the engine prior to computation represents a significant proportion of the total task duration.

**Question 2: Worked Answer :**

This is actually supposed to be a trick question. I hoping that candidates would look at the problem and realise that the requirements should be easily met by the solution as is. There is no need for adding extra technology. The team should simply look at optimising what they currently have. The most likely scenario being that the query on the database is badly designed and is table scanning, slowing the whole thing down.

This conclusion can be corroborated by the simple calculation:

10,000 x 3KB = 30MB = 240Mb of data transported in up to 60s

\=> Worst transfer rate is 240Mb/60 = 4Mb/s which is well below the theoretical performance of such a DB.

However, if we were to prove that the database was adequately optimised (lets assume it has some incredibly overly normalised structure which is somehow leading to the latencies observed) it would be reasonable to seek a performance improvement by adding a caching layer over the database

Using Coherence the trades would be loaded into a cache that would be keyed on the trade ID. An index could be used on the user field from which the queries that drive the home page of the application would be derived.

It should be noted that any performance benefit is more likely to come from the fact that such a change would force the data to be now held in a denormalised form and this in would likely constitute more of a speed increase than adding the caching technology itself.

_Note to Facilitator:_ I suggest that you encourage them down both the paths i.e. make sure they notice that the solution should meet its requirements without additional technology as well as coming up with a caching solution in its own right.

**Question 3: Worked Answer:**

_Candidate Guide:_

1. **_Transactionality:_** Data writes must be transactional for most financial applications of this sort. This means we need to make sure the record hits a disk before it returns to the user. Candidates should be encouraged to consider using a Coherence CacheStore to do this (write to DB). Should it be sync or async? Is async safe enough? Alternatively maybe a messaging system could be quicker than hitting the DB? Why??
2. **_Replicated or Partitioned:_** There is a lot of data. To hold all of it in the cache would imply using a Partitioned topology. Replicated caching could only be used in a read-through mode with an eviction policy to ensure only a subset of the data was cached. Note replicated caching can be provided by a wealth of OS technologies too.
3. **_Move Read Load to a Cache:_** Decrease the load on the RAC cluster by moving **read** load to a cache. The question would be whether to preload the cache or not. Preloading the cache would completely remove read load from the database but the cache would need to be large and expensive. A more cost effective option would be to not preload the cache but instead use a simple in process caching scheme that did ‘read-through’ – that is to say that the first read gets the value from the database but subsequent ones only hit the cache. Coherence would be needed for the first solution but noit the second (could use other open source cache)
4. **_Move Write Load to a Messaging System:_** The above would significantly decrease the load on the RAC cluster but this may not be enough. To scale further we need to address **write** performance. Of course we could make the RAC cluster bigger, i.e. the brute force approach, but scaling RAC will produce a well below linear performance improvement as machines are added, particularly when writes predominate. Hence the best option is to decouple load from the database via some disk based resource. Do this with a store and forward messaging system such as Tibco EMS. EMS will provide a lower latency, higher bandwidth solution that will buffer the DB and provide on-disk queuing should load get too high. As an aside you can actually do this in Coherence too if you are using that. There is a pattern for store and forward messaging published here: [http://coherence.oracle.com/display/INCUBATOR/Messaging+Pattern](http://coherence.oracle.com/display/INCUBATOR/Messaging+Pattern)

_Comments on the Technologies Suggested:_

- Scaling Oracle RAC is unlikely to bring just rewards, particularly in this scenario.
- You could use a replicated cache could in read through mode with an eviction policy. A read request would cause the data to be loaded into one server (the one servicing the request) then replicated to the others. The reason for using a replicated cache is that there are many  free replicated caching products available in the Open Source world e.g. EHCache, Memcached, JBoss Cache etc. Such a solution would provide simple, in process, read through caching in a solution that would be much cheaper than using Coherence.
- Alternatively a partitioned cache would be an excellent choice regardless of whether you decided to preload all data or not. It is the Ferrari of the caching world and would facilitate all solutions discussed above excellently. However it’s not the cheapest.
- The grid would be completely inappropriate for this kind of application. The reason is that this kind of application only performs small tasks – simple data reads and writes. The latencies involved in wrapping these as grid tasks and sending them out would make the application seem sluggish and the architecture seems somewhat bizarre. The grid suits applications that need to do work that takes a while to run and can be split up into bits that can be run concurrently. This is not such an application.

**Question 4: Worked Answer:**

This question is quite open ended so this I just one possible solution for you to think about.

Assuming the application will have preloaded the market data that the user is interested in (either current or some previous snapshot):

(a) Perturb trade parameters

If (pricing strategy is simple) => local computation

else => send to server

(b) Perturb market data

If (pricing strategy is simple & single trade pricing request) => local computation

else => send to server

Due to the scaling requirements the compute grid would be the only sensible place to perform complex pricing routines such as this (Long running routines such as Monte Carlo simulations are bound to be used so a Cache only solution is not appropriate).

Next we want to pre-cache as much data as possible on different engines.

For the case of long running pricing routines that are based on Monte Carlo simulations the desire would be to parallelise each "path" calculation. To get minimum latency on such computations it would be ideal to utilise the full grid meaning all engines would need the same market data (for the same identifier). However it is unlikely that all engines could pre-cache all market data (as the data size would be too great). The options are thus to either:

a) Load the data from the cache for the appropriate CCY on all engines and perform the pricing routines.

b) Split engines so that different engines have different types of market data pre-cached. Use a custom discriminator which was sensitive to the market data identifier (CCY probably) to re-route computations to engines that are likely to have that market data pre-cached.

The requirement to be able to model the behaviour of the trader’s complete position, potentially thousands of trades, for sensitivity to market conditions is the generalisation of the single trade. Not only does one need to allow for the behaviour of each trade as market conditions vary but take account of how changes in value (of the trades) might be correlated. Do they reinforce each other or does a gain in one offset a loss in another? The result of the initial calculations on the individual trades is an intermediate dataset required to compute the overall results. The factors that determine the size of the intermediate results set would include the following;

- Lifespan of the portfolio to expiry.
- The complexity of the individual instruments.
- The number of risk measures that are to be calculated.

Which do you use? This depends on the market data size i.e. is the extra time needed to load the data for a certain market data identifier to all engines greater than the penalty paid by pricing on the subset of engines assigned to a single identifier. This is a function of how complex the pricing
