---
title: "Interesting Links Dec 2011"
date: 2011-12-31
categories: 
  - "blogroll"
  - "links"
slug: "interesting-links-for-dec-2011"
---

**Hardware**

- Intel managing to squeeze 50 cores on a single chip, breaking through the teraflop boundary as they do so: [Brier Dudley's Blog | Wow: Intel unveils 1 teraflop chip with 50-plus cores | Seattle Times Newspaper](http://seattletimes.nwsource.com/html/technologybrierdudleysblog/2016775145_wow_intel_unveils_1_teraflop_c.html)
- RISC architectures have had a renaissance thanks largely to the needs of the mobile sector, could their low power consumption make them a serious contender for enterprise space? [x86 Faces Unexpected RISC Competition](http://drdobbs.com/architecture-and-design/231903053)
- AMD announce 4 memory channels allowing massive addressable spaces up to 364GB per CMP : [AMD's Interlagos and Valencia finally emerge](http://www.eetimes.com/electronics-news/4230565/AMD-s-Interlagos-and-Valencia-finally-emerge)
- Anyone who follows my blog will know of my belief in large address spaces reshaping the landscape, certainly for enterprise applications. This articles echoes these views:  [Megatrend: Cheap RAM Reshaping All of Computing | Dr Dobb's](http://drdobbs.com/architecture-and-design/232300106)

**FPGA**

- IBM’s Lime is an interesting approach to simplifying the programming of secondary devices. See [Lime paper](http://domino.watson.ibm.com/comm/research_people.nsf/pages/bacon.refereed-pubs.html/$FILE/Auerbach10Lime.pdf) and the related [Liquid Metal](https://researcher.ibm.com/researcher/view_project.php?id=122) project.
- JVM on FPGA: [JOP: A Tiny Java Processor Core for FPGA](http://www.jopdesign.com/)
- An interesting paper on using FPGA for Monte Carlo Simulation: [FPGA for monte carlos](http://www.doc.ic.ac.uk/~dt10/research/thomas-08-finance-mcmc.pdf)

**High Performance Java**

- An excellent talk about using memory efficiently in Java applications, that the costs are often higher than we think. It includes clear descriptions of the footprint of all Java objects and utilities : [Building Memory Efficient Java Applications](http://www.cs.virginia.edu/kim/publicity/pldi09tutorials/memory-efficient-java-tutorial.pdf)
- There has been a flurry of activity coming from Azul Systems recently. Most notably the release of [Zing](http://www.infoq.com/news/2011/11/zing5-native), their pauseless garbage collector. Gene Til’s talk about the State of the Art in GC from QCon SF 2011 is one of the best I’ve seen (QConSF 2011: [State of the Art in Garbage Collection](/uploads/MemoryWallQConSF2011.pdf)).
- Azul have also recently released JHiccup. An interesting utility that measures operating system stalls. [Java Developer Tools: jHiccup Java Performance Analysis](http://www.azulsystems.com/dev_resources/jhiccup#Help_Developers)
- Charles Nutter’s comments on his favourite JVM flags including my favourite (-XX:+PrintOptoAssembly): [Headius: My Favorite Hotspot JVM Flags](http://blog.headius.com/2009/01/my-favorite-hotspot-jvm-flags.html)

**Distributed Data Storage**

- A great paper from VLDB describing an approach for balancing replication and partitioning, something close to my own heart: [Schism: a Workload-Driven Approach to Database Replication and Partitioning](http://www.vldb.org/pvldb/vldb2010/papers/R04.pdf)
- Hasso Plattner (the P is SAP) wrote this paper which provides an insigntful view of where he believes the field should be going (and of course SAP’s solution Hana): [Hasso Plattner on In-Memory OLAP & OLTP](http://epic.hpi.uni-potsdam.de/pub/Home/TrendsAndConceptsII2010/Plattner-AcommonDBApproach.pdf)
- I enjoyed watching this talk about Mongo: [InfoQ: Scaling with MongoDB](http://www.infoq.com/presentations/Scaling-with-MongoDB)

**Interesting:**

- An entertaining article from the Economist about David Gelernter’s predictions of the future of computing: [Brain scan: Seer of the mirror world | The Economist](http://www.economist.com/node/21540383)
- Could Prezi really dislodge PowerPoint? [Prezi](http://prezi.com/index/)
- Double Loop Learning – a different view on organizational learning. [Chris Argyris](http://www.infed.org/thinkers/argyris.htm).
- Worth reading if you are not familiar with the idea already: [CQRS](http://martinfowler.com/bliki/CQRS.html)
- An interesting twist on the traditional storyboard approach [Our Story Board is Better Than Yours...](http://agilesoftwaredevelopment.com/blog/janusz-gorycki/out-story-board-better-yours) I’m a big fan of replacing estimation with uniformly sized stories.
- Booked your next holiday? What about a [Code Retreat with Corey Haines](http://coderetreat.com/history.html)
