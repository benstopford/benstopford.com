---
title: "The Big Data Conundrum"
date: 2012-11-10
categories: 
  - "analysis"
  - "distributed-data-storage"
coverImage: "Merging.png"
slug: "the-big-data-conundrum"
---

I attended an interesting talk at JAX earlier this year by guy called Ian Plosker, from Riak, somewhat amusingly entitled 'TheBigDataCon' (worth [a look](http://www.slideshare.net/jaxlondon2012/the-big-data-con-why-big-data-is-a-problem-not-a-solution) by the way - the slides are good). Ian makes a little fun of all the current hype, joking that vendors seemed to be the only people actually monitising Big Data. I think we can't help but be a little cynical of anything that has this much hype.

On another level the term has become overloaded. It has many definitions, Oracle for example talk about Big Data in a very different way to say MapR. It seems to broadly boil down to two angles though:

- The promise of greater insight using the huge amounts of data we produce
- A change in the technologies we use to crunch our way through the data we have (or expect to have)

Like any other commodity, the harder it is to extract, the more it costs. The aspirational, needle-in-a-haystack concept that drives much of the marketing paraphernalia is certainly real and should not be ignored. However the hype around the 'hidden insight' thing masks a more fundamental, and grounded point: the technology shift that facilitates all this.

There is a view that todays data is 'big' and that having big data means some form of MapReduce. Yet it is not size that really matters. Both relational and nosql camps can deal with the data volumes (and even, for the most part, the [three Vs](http://blogs.sas.com/content/sascom/2012/04/12/turning-big-data-volume-variety-and-velocity-into-value/) in one way or another). Ebay for example runs a 20PB+ database. Yahoo and Google both have larger MR clusters, but not that much larger. For most problems data volume alone is not enough to make a sensible technology choice (and I'd contest that any of the Vs were really enough either). As the academic world likes to keep reminding us ([here](http://vldb.org/pvldb/vol5/p1712_avriliafloratou_vldb2012.pdf) and [here](http://www.cse.nd.edu/~dthain/courses/cse598z/spring2010/benchmarks-sigmod09.pdf)) performance is not the reason to pick up a big data technology. There reason is that these new technologies embrace a very different approach to data analysis, particularly in the context of the whole 'lifecycle' of our data analysis work. Big Data technologies decouple us from some of the shackles that make big data problems hard. However, there is no free lunch and they come with some shackles of their own.

A core difference is the ability to define a schema at runtime, rather than upfront. That alone is a powerful, and game changing idea. Dave Campell put it well in his [VLDB keynote](http://www.vldb.org/2011/files/slides/keynotes/campbell_keynote.pptx) when he says ‘ability to model data is much more of a gating factor than raw size, particularly when considering new forms of data’. Modelling data, getting it into a form we can interpret and understand can be a longwinded and painful task, and something we must do before we can do anything useful with it.

> _Our ability to model data is much more of a gating factor than raw size_

Traditional databases push us to model our data before we store it. Big data solutions often leave their data in its natural form. A ‘virtual schema’ is bound at runtime. This concept of binding the schema ‘late’ is powerful. It allows the interpretation of the data to be changed at any time without having to change the physical format of the data on disk. Something that becomes increasingly important as the size of the dataset increases. The downside of not imposing a schema from the point of ingestion is that keeping old forms of data 'current' becomes an increasingly difficult task. That's to say that the client is left with the problem of handling many data representations. Fine if the model is free text, tougher if the model has any real structure (explicit or implicit).

> _The concept of binding the schema ‘late’, with the data held in its natural form, is powerful._

Big Data technologies offer very different performance profiles to relational analytics tools. The lack of indexing and overarching structure means inserts are fast, making them suitable for high velocity systems and batch processing. The imperative interface and the absence of a schema, makes diverse, ad hoc analytics hard though. Instead they work best for specific, well-defined data operations (I often use the data enabled grid analogy). It is likely this that has driven Big Data leaders, Google, and more recently Hadoop, to add more database-like features to their products (Dremel/Magastore/Spanner providing SQL like interface and ACID semantics). Yet it's much harder to optimise in a late-bound world, no big data solution today comes close to the raw performance of the top end analytics engines.

> _Most of today's databases are hindered hugely by needing the schema to be defined upfront._

The last thing to consider is the cost of change. For simple data sets it is less apparent. Start joining data sets together though and it becomes a different ball game. Whist possible with Big Data technologies, it's just going to cost more and managing the complexity with the absence of a schema becomes an increasingly uphill struggle. In this case, better to stick in the relational world (for now at least).

However most of today's databases have the HUGE disadvantage that the schema needs to be defined, and the data understood, upfront. Great for simple, well defined business data, but if you're searching free text, machine generated data or simply a hugely diverse data population (like the data that gets thrown around most big organisations) it's simply not practical or maybe not possible to understand, and model, the data upfront. By applying the schema later in the cycle the cost of change, the availability of insight and the inherent feedback cycles can all be improved.

![](images/Merging-300x265.png "Merging")

**As for the future?**

You’ll probably have noticed that every database vendor worth their salt now have some form or Big Data offering, be it bought in, ‘tacked on’ or genuinely integrated. Likewise the Big Data vendors are looking more and more like their relational counterparts, sprouting query languages, loose schemas, columnar storage, indexing, even elements of transactionality. The two camps are converging.

Many of new set of relational technologies look more like MapReduce than they do like System R (IBM’s original relational database). Yet the majority of the database community still seem to be lurking in the corner of the playground, wearing anoraks and murmuring (although these days the anoraks are made by Armani). They are a long way from penetrating the progressive Internet space. Joe Hellerstien’s [words](http://db.cs.berkeley.edu/jmh/talks/hpts2001-we-lose.pdf) still ring true today.

> _The new cool kids of the database world are making their mark with technologies of the moment, backed with a hefty dose of academic acumen._

The future is likely to be one of convergence, and redirecting the database community is undoubtedly good. In fact possibly the most most useful things the NoSQL movement has done has been to give a well timed boot to the database world's behind, reminding them that they need to listen to their consumers. They got stuck in a rut and the internet space wasn't going to wait around. Convergence over some newly shared values that sit between the two camps is of course inevitable, and welcomed.

> _The database world got stuck in it's ways and the internet space wasn't going to wait around._

The evidence is quite plain already. There are a host of young (ish) upstart technologies hitting the space. The number of shared nothing analytics engines has significantly increased (Asterdata, Vertica, VoltDB, Exasol, ParAccel, Greenplum, Hana the list goes on) and the benchmarks they are extremely impressive. There are hybrid engines mixing MapReduce engines with smarter storage, routing and indexing strategies. [Hadapt](http://hadapt.com/) and [Impala](http://blog.cloudera.com/blog/2012/10/cloudera-impala-real-time-queries-in-apache-hadoop-for-real/) are good examples. The former particularly as it is the one that probably best personifies the blending of the two worlds.

> _These new upstart database technologies redefine the current mainstream with, not in spite of, the lessons of the past._

Finally there are some interesting one-stop-shop approaches. Holistic solutions that span dynamic schema provisioning and data access, all the way to presentation in a single package. Originating in the machine generated data space, [Splunk](http://www.splunk.com/) (dominant) and [Logscape](http://logscape.com/) (scalable), are the current leaders in this space and there is likely to be a lot more activity. For answering the what-if questions or assembling high level MI stacks these all inclusive solutions get the closest to answering the more insightful questions we have today.

Whether this ever breaks the strangle hold clenched by the oligopoly of key database players remains to be seen. Michael Stonebraker still rains disdain on the NoSQL world even today \[[see here](http://cacm.acm.org/blogs/blog-cacm/149074-possible-hadoop-trajectories/fulltext)\]. He may be outspoken, he may come across like a bit of \*\*\*\* at times, but it is unlikely that he is wrong.  The solutions of the future will not be the pure (and relatively simplistic) MapReduce of today. They will be blends that protect our data, even at scale. For me the new technologies coming from both camps are exciting as they redefine the current mainstream thinking _with, not in spite of_, the lessons of the past.

Related posts:

- [Big Data - An Epic Essay](/2012/07/28/thoughts-on-big-data-technologies-part-1/)
