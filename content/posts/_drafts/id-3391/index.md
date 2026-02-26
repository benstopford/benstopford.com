---
title: "Stuff I'm reading on databases"
draft: true
---

- Original Log Structured Merge Tree paper [here](http://staff.ustc.edu.cn/~jpq/paper/flash/1996-The%20Log-Structured%20Merge-Tree%20%28LSM-Tree%29.pdf).
- Yahoo's 2012 Sigmod adaptation of LSMT [here](http://www.eecs.harvard.edu/~margo/cs165/papers/gp-lsm.pdf).
- Rereading Bigtable [here](file:///Users/benji/BensWorld/To%20Read/Papers/Data%20Storage/bigtable-osdi06%20\(1\).pdf).

Integration of Ingres and Vectorwise Sigmod paper [here](http://www.sigmod.org/publications/sigmod-record/1109/pdfs/08.industry.inkster.pdf).

Masters Thesis covering query optimisation in Vectorwise [here](http://homepages.cwi.nl/~boncz/msc/2012-AndreiCosteaAdrianIonescu.pdf).

MonetDB's X100 [here](http://www.cidrdb.org/cidr2005/papers/P19.pdf).

Rereading Alabdi's work on query optimisation in columar databases [here](Query%20Execution in Column-Oriented Database Systems) and [here](http://www.cs.yale.edu/homes/dna/papers/abadi-column-stores.pdf).

Amazon dynamo [paper](http://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf).

Rose - LSM engine designed for replication  [here](http://www.vldb.org/pvldb/1/1453914.pdf).

Nice exert from this:

LSM Trees have different asymptotic performance to tree structures. In particular, the amortized cost of insertion is O(√n log n) in the size of the data and is proportional to the cost of sequential I/O. In a B-tree, this cost is O(log n) but is proportional to thecost of random I/O.

but how can this be one disk seek, even as a btree? there is no in memory component???

another good one [http://dailyjs.com/2013/04/19/leveldb-and-node-1/](http://dailyjs.com/2013/04/19/leveldb-and-node-1/)
