---
title: "UNFINISHED (BIT LAME): Big Data or Big Database?"
date: 2012-11-10
draft: true
slug: "id-2358"
---

There is a more recent rant of this on my harddrive

It’s hard not to be just a little bit cynical of the Big Data movement as more and more vendors jump onto the marketing bandwagon. Big Data seems to be the Gold Rush of the moment. Whilst it undoubtedly contains the promise of fame and fortune, the signal to noise ratio is high. There is also, I believe, some misconception out there.

**Does Big Data technology let you extract information you couldn't previously get at?**

Just like any other commodity, the harder it is to extract, the more it costs - some of the big data tools will reduce this cost for certain types of data and that is potential win but there will always be a lot of hard graft. More specifically the process of analysing data is effectively a constant: You have a data source, you apply some interpretation of the data from that source, you perform analysis on the data in its interpreted form. Whether you do this analysis up front and put the results into a relational database, or bind a 'conceptual schema' at runtime using a big data technology like Splunk or Hadoop, you are following the same path for your analysis.

The big difference is your ability to change and move quickly. Interpreting the data at runtime allows you to change your 'schema' dynamically. It's a bit like having a view over your underlying data. This is quite different to the traditional database model where you tend to interpret your data before you put it into your schema (the traditional database equivilent is to have a staging table and a process that migrates the staged data to your domain model).

So it's not so much that Big Data technologies give you access to data you couldn't get at before, it's more that give you a different access path to it, one which makes it easier for you to experiment and change  your mind.

**Misconception: I've got lots of data, I need a Big Data technology**

Firstly do you really have a lot of data? A lot of data should really mean petabytes or at least hundreds of terabytes. If you're dealing with video streams or sensor data (i.e.  youtube, meteoritical sensors etc) you 'might' be in this range but as discussed before (here) a petabyte is a lot of data (so we're talking more than the textual content on the inernet).

Secondly relational technology will work at large data sizes (albeit with a bit more tuning). Ebay for example runs a 20PB+ database. Yahoo and Google both have larger MR clusters, but not that much larger. The point is that size is not the driving (or gating) factor here. Both camps can deal with the data volumes (and even for the most part the [three Vs](http://blogs.sas.com/content/sascom/2012/04/12/turning-big-data-volume-variety-and-velocity-into-value/)). For most problems that we are likely to be faced with, data volume alone is not enough to make a sensible technology choice. It is not the technologies abilities that are the real problem. It is the whole lifecycle of our data and what we interned to do with it that matters.

> Our ability to model data is much more of a gating factor than raw size

**Big Data is really about a different access path**

Big data technologies give us a window into types of data that we can’t easily process in other ways. However often the data we collect (post interpretation) is likely stored in a database. My view is that the real power of Big Data comes from binding the schema at runtime. Dave Campell put it well in his [VLDB keynote](http://www.vldb.org/2011/files/slides/keynotes/campbell_keynote.pptx) when he says ‘ability to model data is much more of a gating factor than raw size, particularly when considering new forms of data’. Modelling data, getting it into a form we can interpret and understand  can be a longwinded and painful task, and something we must do before we can do anything useful with it. Big data solutions often leave their data in its natural form. A ‘virtual schema’ is bound at runtime. This concept of binding the schema ‘late’ is powerful. It allows the interpretation of the data to be changed at any time without having to change the physical format of the data on disk. Something that becomes increasingly important as the size of the dataset increases.

> The concept of binding the schema ‘late’, with the data held in its natural form, is powerful

Big Data technologies also posses very different performance profiles to relational analytics tools. The lack of indexing and overarching structure make inserts fast, making them suitable for high velocity systems. The programming interface, in the absence of a schema, makes diverse, ad hoc analytics hard though. Instead they work best for specific, well defined data operations (hence the data-enabled grid analogy).

These technologies excel in a number of specific use cases. If you are looking for a needle in a 'haystack of diverse data types', where you don't want to remodel and store the data yourself, the ability to crunch data with an imperative programming language that avoids the pains of a predetermined schema is a powerful thing. Alternatively, by combining fast, key-based access to a NoSQL store which has MapReduce analytics built in, you can reap the benefits of two paradigms at once (low latency access and scalable analytics). This is a powerful tool, particularly in the internet space.

However for more complex data sets and access requirements; where you care about data, where that data comes from a variety of sources and you want to slice and dice it in a variety of different ways, the relational database is still the better choice. It comes down to the cost of change. For simple data sets it is less apparent. Start joining data sets together though and it becomes a different ball game. Not that you couldn't do this with a Big Data technology, it's just going to cost more and managing the complexity with the absence of a schema becomes an increasingly uphill struggle.
