---
title: "Singleton Service"
date: 2011-11-05
categories: 
  - "coherence"
coverImage: "singleton2.png"
slug: "coherence-the-singleton-service"
---

<div style="text-align: center;"><a href="images/singleton2.png"><img src="images/singleton2-300x80.png" alt=""></a></div>

_Being a data grid, Coherence is very good at doing things in a distributed way across all nodes in the cluster. However it doesn’t offer any functionality (currently) for running a service just the once, in a reliable manner. Most applications solve this problem by simply running another process, for example you might start a second process that reads data off some queue and keeps your cluster up to date. It’d be nice however if you could leverage Coherence’s fault tolerance to ensure that, if the cluster was running, your QueueListener was always running too. In fact this is fairly simple to do and can be used for a host of common applications including loading data, keeping it up to date, adding indexes and regulating a cluster wide time stamp (article to follow)._

_What we want is a service that will always run on one of our Coherence nodes no matter what happens to the cluster._

This solution is conceptually simple. You have lots of processes in your cluster. When each node starts it simply checks whether the service has already been started elsewhere by attempting to lock a fictitious, well-known key:

```
lockCache.lock(“SingletonLockKey”);
```

Only one of the processes in the cluster will attain the lock. If it does attain it then it starts the Singleton Service, adds indexes, loads data or whatever. Simple. If the node running the service dies then the lock is released and another process will acquire it and start the singleton service there.

```
//Run in a new thread on a wrapped DefaultCacheServer i.e. should run on every node
int blockUntilLockAquired = -1;
lockCache.lock(“SingetonLockKey”);
while(true){
   boolean locked = lockCache.lock("singletonLockKey", blockUntilLockAquired);
   if(locked){
      //start singletons here
      wait();
   }
}
```
