---
title: "Talk Proposal: Managing Normalised Data in a Distributed Store"
date: 2010-11-14
categories: 
  - "blogroll"
slug: "talk-proposal-managing-normalised-data-in-a-distributed-store"
---

I've been working on a medium sized data store (around half a TB) that provides high bandwidth and low latency access to data.

Caching and Warehousing techniques push you towards denormalisation but this becomes increasingly problematic when you move to a highly distributed environment (certainly if the data is long lived). We've worked on a model that is semi normalised whilst retaining the performance benefits associated with denormalisation.

The other somewhat novel attribute of the system is its use of Messaging as a system of record.

I did a talk abstract, which [David Felcey](http://blogs.oracle.com/felcey/) from Oracle very kindly helped with, which describes the work in brief. You can find it [here](/uploads/Managing-Normalised-Data-in-a-Distributed-Store.pdf).

I'll also be adding some more posts in the near future to flesh out how this all works.
