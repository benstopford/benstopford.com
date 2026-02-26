---
title: "Thinking in Graphs: Neo4J"
date: 2012-08-17
categories: 
  - "products"
slug: "thinking-in-graphs-neo4j"
---

I enjoyed listening to Ian Robinson  speak about Neo4J (slides are here [Thinking in Graphs](/uploads/Thinking-in-Graphs.pdf)). The odd one out of the NoSQL pack, Neo4J is a fascinating alternative to your regular key value store. For me it’s about a different way of thinking about data simply because the relations between nodes are as much a part of the data model as the nodes are themselves. I am left wondering somewhat how one might apply this solution to the enterprise space, particularly finance. Multistep montecarlo springs to mind as it creates a large connected space but there is no real need to traverse that space retrospectively. There may be application in other simulation techniques though. The below is a paraphrased version of Ian’s words.

Today’s problems can be classified as a function of not only size but also connectedness and structure.

_F(size, connectedness, structure)_

The Relational model struggles to deal with each of these three factors. The use of sparsely populated tables in our databases and null checks in client side code allude to the unsuitability of this model.

NoSQL offers a solution. The majority of this fledgling field rely on the concept of a Map (Dictionary) in some way. First came simple key-value stores like Dynamo. Next column-oriented stores like Cassandra and BigTable.Finally Document Databases provide a more complex document model(for example JSON), with facilities for simple introspection.

Neo4J is quite different to its NoSQL siblings: A graph database that allows users to model data as a set of nodes and relationships. Once modelled the data can be examined based on its connectedness (i.e. how one node relates to others) rather than simply based on its attributes.

Neo4J uses a specific type of graph model termed a Property Graph: Each node has associated attributes that describe its specificities. These need not be homogenous (as they would in a relational or object schema). Further the relationships between nodes are both named and directed. As such they can be used in search criteria to find relationships between nodes.

The Property Graph model represents a pragmatic trade off between the purity of a traditional graph database and what you might see in a document database. This can be contrasted with the other graph database models: In ‘Triple Stores’ every attribute is broken out as a separate node (this is a bit like third normal form for a graph database). Another alternative is Hypergraphs, where an edge can connect more than two nodes (see Ian’s slide to get a better understanding of this). Triple stores suffer from their fine-grained nature (I’m thinking binary vs red-black trees). Hypergraphs can be hard to apply to real world modelling applications as the multiplicity of relationships can make them hard to comprehend. The Property Graph model avoids the verbosity of triple stores and the conceptual complexity of Hypergraphs. As such the model works well for Complex, densely connected domains and ‘Messy’ data.

The fundamental attribute of the graph database is that Relationships are first class elements. That is to say querying relationships in a graph database is as natural as querying the data the nodes contain.

Neo4J, like many NoSQL databases is schemaless. You simply create nodes and relate them to one another to form a graph. Graphs need not be connected and many sub-graphs can be supported.

A query is simply ‘parachuted’ into a point in the graph from where it explores the local areas looking for some search pattern. So for example you might search for the pattern A-->B-->C. The query itself can be executed either via a ‘traversal’ or using the Cypher graph language. The traversal method simply visits the graph based on some criteria.For example it might only traverse arcs of a particular type. Cypher is a more formal graph language that allows the identification of patterns within the graph.

Imagine a simple graph of two anonymous nodes with an arc between them:

O-->O

In Cypher this would be represented

A-\[:connected\_to\]-B

Considering a more complex graph:

A-->B-->C, A-->C or A-->B-->C-->A

We can start to build up pattern matching logic over these graphs for exampleA-\[\*\]->B to represent that A is somehow connected to B (think regex for graphs). This allows the graph to be mined for patterns based on any combination of the properties, arc directions or name (type).

There are further Cypher examples [here](http://docs.neo4j.org/chunked/milestone/cypher-query-lang.html) including links to an online console where you can interactively experiment with the queries. Almost all of the query examples and diagrams are generated from the unit tests used to develop Cypher. This means that the manual is always an accurate reflection of the current feature set.

Physical Characteristics:

The product itself is JVM based (query language written in Scala). There is an HTTP interface too (restful). It is fully transactional (ACID) and it is possible to override the transaction manager should you need to coordinate with an external transaction manager (for example because you want to coordinate with and external store). An object cache is used to store the entities in memory with fall through to memory-mapped files if the dataset does not fit in RAM. There is also an HTTP based API.

HA support uses a master-slave, replicated model (single master model). You can write to a slave (i.e. any node) and it will obtain a lock from the master. Lucene is the default index provider.

The team have several strategies for mitigating the impact of GC pauses, the most important being a GC resistant caching strategy. This assignes a certain amount of space in the JVM heap; it then purges objects whenever the maximum size is about to be reached, instead of relying on GC to make that decision. Here the competition with other objects in the heap, as well as GC-pauses, can be better controlled since the cache gets assigned a maximum heap space usage. Caching is described in more detail [here](http://docs.neo4j.org/chunked/milestone/configuration-caches.html#_object_cache).

Ian mentioned a few applications too:

- Telcos: Managing the network graph: If something goes wrong they use the graph database he help predict where the problem likely comes from by simulating the network topology.
- Logistics: parcel routing. This is a hierarchical problem. Neo4J helps by allowing them to model the various routes to get a parcel from it’s start to end locations. Routes change (and become unavailable).
- Finally the social graph which is fairly self explanatory!

All round an eye-opening approach to the modelling and inspection of connected data sets!
