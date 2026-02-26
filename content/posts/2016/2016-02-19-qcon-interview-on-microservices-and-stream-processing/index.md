---
title: "QCon Interview on Microservices and Stream Processing"
date: 2016-02-19
categories: 
  - "blogroll"
slug: "qcon-interview-on-microservices-and-stream-processing"
---

This is a transcript from an interview I did for QCon (delivered verbally):

_QCon: What is your main focus right now at Confluent?_

_Ben_: I work as an engineer in the Apache Kafka Core Team. I do some system architecture work too. At the moment, I am working on automatic data balancing within Kafka. Auto data balancing is basically expanding, contracting and balancing resources within the cluster as you add/remove machines or add some other kind of constraint or invariant. Basically, I’m working on making the cluster grow and shrink dynamically.

_QCon: Is stream processing new?_

_Ben_: Stream processing, as we know it, has really come from the background of batch analytics (around Hadoop) and that has kind of evolved into this stream processing thing as people needed to get things done faster.Although to be honest, stream processing has been around for 30 years in one form or another, but it has just always been quite niche. It’s only recently that it’s moved mainstream. That’s important because if you look at the stream processing technology from a decade ago, it was just a bit more specialist, less scalable, less available and less accessible (though, certainly not simple). Now that stream processing is more mainstream, it comes with a lot of quite powerful tooling and the ecosystem is just much bigger.

_QCon: Why do you feel streaming data is an important consideration for Microservice architectures?_

_Ben_: So you don’t see people talking about stream processing and Microservices together all that much. This is largely because they came from different places. But stream processing turns out to be pretty interesting from the Microservice perspective because there’s a bunch of overlap in the problems they need to solve as data scales out and business workflows cross service and data boundaries.

As you move from a monolithic application to a set of distributed services, you end up with much more complicated systems to plan and build (whether you like it or not). People typically have ReST for Request/Response, but most of the projects we see have moved their fact distribution to some sort of brokered approach, meaning they end up with some combination of request/response and event-based processing. So if ReST is at one side of the spectrum, then Kafka is at the other and the two end up being pretty complimentary. But there is actually a cool interplay between these two when you start thinking about it. Synchronous communication works well for a bunch of use cases, particularly GUIs or external services that are inherently RPC. Event-driven methods tend to work better for business processes, particularly as they get larger and more complex. This leads to patterns that end up looking a lot like event-driven architectures.

So when we actually build these things a bunch of problems pop up because no longer do we have a single shared database. We have no global bag of state in the sky to lean on. Sure, we have all the benefits of bounded contexts, nicely decoupled from the teams around them and this is pretty great for the most part. Database couplings have always been a bit troublesome and hard to manage. But now we hit all the pains of a distributed system and this means we end up having to be really careful about how we sew data together so we don’t screw it up along the way. Relying on a persistent, distributed log helps with some of these problems. You can blend the good parts of shared statefulness and reliability without the tight centralised couplings that come with a shared database. That’s actually pretty useful from a microservices perspective because you can lean on the backing layer for a bunch of stuff around durability, availability, recovery, concurrent processing and the like.

But it isn’t just durability and history that helps. Services end up having to tackle a whole bunch of other problems that share similarities with stream processing systems. Scaling out, providing redundancy at a service level, dealing with sources that can come and go, where data may arrive on time or may be held up. Combining data from a bunch of different places. Quite often this ends up being solved by pushing everything into a database, inside the service, and querying it there, but that comes with a bunch of problems in its own right.

So a lot of the functions you end up building to do processing in these services, overlap with what stream processing engines do: join tables and streams from different places, create views that match your own domain model. Filter, aggregate, window these things further. Put this alongside a highly available distributed log and you start to get a pretty compelling toolset for building services that scale simply and efficiently.

_QCon: What’s the core message of your talk?_

_Ben_: So the core message is pretty simple. There’s a bunch of stuff going on over there, there’s a bunch of stuff going on over here. Some people are mashing this stuff together and some pretty interesting things are popping out. It’s about bringing these parts of industry together. So utilizing a distributed log as a backbone has some pretty cool side effects. Add a bit of stream processing into the mix and it all gets a little more interesting still.

So say you’re replacing a mainframe with a distributed service architecture, running on the cloud, you actually end up hitting a bunch of the same problems you hit in the analytic space as you try to get away from the Lambda Architecture.

The talk dives into some of these problems and tries to spell out a different way of approaching them from a services perspective, but using a stream processing toolset. Interacting with external sources, slicing and dicing data, dealing with windowing, dealing with exactly once processing, and not just from the point of view of web logs or social data. We’ll be thinking business systems, payment processing and the like.

[Catch the full talk at QCon London.](https://qconlondon.com/presentation/microservices-streaming-world)
