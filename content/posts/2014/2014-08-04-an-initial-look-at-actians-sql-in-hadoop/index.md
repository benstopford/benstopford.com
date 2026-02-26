---
title: "An initial look at Actian's 'SQL in Hadoop'"
date: 2014-08-04
categories: 
  - "blogroll"
  - "products"
slug: "an-initial-look-at-actians-sql-in-hadoop"
---

I had an exploratory chat with Actian today about their new product ‘SQL in Hadoop’.

In short it’s a distributed database which runs on HDFS. The company are bundling their DataFlow technology alongside this. DataFlow is a GUI-driven integration and analytics tool (think suite of connectors, some distributed functions and a GUI to sew it all together).

Neglecting the DataFlow piece for now, SQL in Hadoop has some obvious strengths. The engine is essentially Vectorwise (recently renamed ‘Vector’). A clever, single node columnar database which evolved from [MonetDB](https://www.monetdb.org/) and touts the use of vectorisation as a key part of its secret sauce. Along with the usual columnar benefits comes the use of [positional delta trees](http://www.cs.cornell.edu/~guoz/Guozhang%20Wang%20slides/Positional%20Update%20Handling%20in%20Column%20Stores.pdf?bcsi_scan_58b06d4cf2669fa1=0&bcsi_scan_filename=Positional%20Update%20Handling%20in%20Column%20Stores.pdf) which improve on the poor update performance seen in most columnar databases, some clever [cooperative scan](http://arxiv.org/pdf/1208.4170.pdf) technology which was presented at VLDB a couple of years back, but they don’t seem to tout this one directly. Most notably Vector has always had quite [impressive benchmarks](http://www.tpc.org/tpch/results/tpch_price_perf_results.asp) both in absolute and price-performance terms. I’ve always thought of it as the relational analytics tool I’d look to if I were picking up the tab.<!--more-->

The Vector engine (termed x100) is the core of Actian’s SQL in Hadoop platform. It’s been reengineered to use HDFS as its storage layer, which one has to assume will allow it to operate better price performance when compared to storage-array based MPPs. It has also had a distribution layer placed above it to manage distributed queries. This appears to leverage parts of the DataFlow cluster as well as Yarn and some other elements of the standard Hadoop stack. It inherits some aspects of traditional MPPs such as the use of replication to improve data locality over declared, foreign key, join conditions. The file format in HDFS is wholly proprietary though so it can’t be introspected directly by other tools.

So whilst it can be deployed inside an existing Hadoop ecosystem, the only benefit gained from the Hadoop stack, from a user’s perspective, is the use of HDFS and YARN. There is no mechanism for integrating MR or other tools directly with the x100 engines. As the file format is opaque the database must be addressed as an SQL appliance even from elsewhere within the Hadoop ecosystem.

Another oddity is that, by branching Vector into the distributed world the product directly competes with its sister product Matrix (a.k.a. ParAccel); another fairly accomplished MPP which Actian acquired a couple of years ago. If nothing else this leads to a website that is pretty confusing.

So is the product worth consideration?

It’s most natural competition must be Impala. Impala however abstracts itself away from the storage format, meaning it can theoretically operate on any data in HDFS, although from what I can tell all practical applications appear to transform source files to something better tuned for navigation (most notably parquet). Impala thus has the benefit that it will play nicely with other areas of the Hadoop stack, Hive, Hbase etc. You won’t get any of this with the Actian SQL in Hadoop product although nothing is to stop you running these tools alongside Actian on Hadoop, inside the same HDFS cluster.

Actian’s answer to this may be the use of its DataFlow product to widen the appeal to non-sql workloads as well as data ingestion and transformation tasks. The DataFlow UI does provide a high level abstraction for sewing together flows. I’ve always been suspicious of how effective this is for non-trivial workflows which generally involve the authoring of custom adapters and functions, but it obviously has some traction.

A more natural comparison might come from other MPPs such as Greenplum, which offers a HDFS integrated version and ties in to the Pivotal Hadoop distribution. Comparisons with other MPPs, Paraccel, Netazza, Vertica etc are also reasonable if you are not restricted to HDFS.

So we may really be looking at a tradeoff between the breadth of the OS Hadoop stack vs. SQL compliance & raw performance. The benefits of playing entirely in a single ecosystem, like the Cloudera offering, is a better integration between the tools, an open source stack which has a rich developer community,  less issues of vendor lock-in and a much broader ecosystem of players (Drill, Storm, Spark, Skoop and many more).

Actian on the other hand can leverage its database heritage; efficient support the full SQL spec, ACID transactions and a performance baseline that comes from a relatively mature data warehousing offering where aggregation performance was the dominant driver.

As a full ecosystem it is probably fair to say it lacks maturity at this time, certainly when compared to Hadoop’s. In the absence of native connectivity with other Hadoop products it is really a database appliance on HDFS rather than a 1st class entity in the Hadoop world. But there are certainly companies pushing for better performance than they can currently get on existing HDFS infrastructure with the core Hadoop ecosystem. For this Actian’s product could provide a useful tool.

In reality the proof will be in the benchmarks. Actian claim order of magnitude performance improvements over Impala. Hadapt, an SQL on Hadoop startup which was backed by and ex-Vertica/academic partnership was hammered by market pressure from Impala and was eventually bought by Teradata. The point being that the performance needs to justify breaking out of the core ecosystem.

There may be a different market though in companies with relationally-centric users looking to capitalise on the cheaper storage offered by HDFS. This would also aid, or possibly form a strategy away from siloed, SAN based products and towards the broadly touted (if somewhat overhyped) merits of Big Data and commodity hardware on the Cloud. Hype aside that could have considerable financial benefit.

Edit: Peter Boncz, who is one of the academics behind the original vector product, published an informative review with benchmarks [here](http://databasearchitects.blogspot.co.uk/2014/08/tpc-ds-with-vector-hadoop-edition.html). There is also an academic overview of (a precursor to) the system [here](http://homepages.cwi.nl/~boncz/msc/2012-AndreiCosteaAdrianIonescu.pdf). Worth a read.
