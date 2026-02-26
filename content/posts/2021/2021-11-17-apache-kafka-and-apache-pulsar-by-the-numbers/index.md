---
title: "Apache Kafka and Apache Pulsar By The Numbers"
date: 2021-11-17
slug: "apache-kafka-and-apache-pulsar-by-the-numbers"
draft: true
---

Redmonk has a [recent post](https://redmonk.com/sogrady/2021/11/04/kafka-vs-pulsar/) analyzing Apache Kafka and Apache Pulsar. The question they raise is whether Apache Pulsar is emerging as a viable alternative to Kafka. This question is common enough these days that I thought it would be worth highlighting the most relevant data set on adoption for the two systems.

There is no perfect way of measuring the adoption of open source projects. Redmonk does a reasonable job, aggregating stats on stack overflow questions, github stars, and other measures. The general picture is that Kafka usage seems to be about 20x by most of these measures. But the analysis isn’t that satisfying because none of these metrics measure actual use of the software. After all, Stack Overflow questions might correlate as much with bad documentation as with usage; and Google Trends tracks interest not adoption.

A better approach would be a more direct measurement of the usage of the software. A perfect measure doesn’t exist. Indeed Apache doesn’t even produce stats on downloads of project artefacts. However, it turns out that there is something pretty close, however. Sonatype, the company that hosts virtually all Java libraries, captures aggregate statistics on downloads, including the monthly number of unique IP addresses downloading a given library. When an application is built, that integrates with the client libraries for Kafka or Pulsar it will download the respective library, driving up this count.

This is a good measure of usage, though not without flaws. One could build the libraries from scratch or use them behind a firewall in which case there would be some undercounting. It also doesn’t capture use in languages other than Java (though that omission would likely disadvantage Kafka, with its larger ecosystem of clients outside Java, the most). In addition, a unique IP address can represent varying amounts of usage. A company behind a VPN might count as only one IP whether they have 1 app using the system or 1,000 apps using the system.

Despite these limitations, I think this is the best measure of active usage and vastly better than the common measures one sees like Q&A forums and Github stars. It has some under and overcounting, so we can’t say that the count is exactly the number of organizations using the software, but clearly it correlates to this, and the relative ratio between systems should be quite meaningful. A further advantage is that it tends to measure _active_ usage. A single one-time trial will bump up the stat in one month, but to sustain that increase requires ongoing builds and rebuilds that re-download the library (whether by developers or a CI).null

Here is the data on Apache Kafka and Apache Pulsar from Sonatype:

<div style="text-align: center;"><img src="images/hiBghhHBEa32GEevVNFPePqXjA4ZJYIvTvRw8I1zOvq_gXwoZYdMIIpi8vwoeI25ow68cMTVasg72YchMAw74WYowU4aqZVfe_Mmnxu3ihqdJ0WnwhUSAnxSc6K4t3S79xFrHsYo" alt=""></div>

This seems inline with some of the data Redmonk produced which tended to show Kafka usage about 20x that of Pulsar for Google Trends and Stack Overflow. However, it also clarifies a trend that is visible but quite noisy in the Redmonk data: despite being larger in absolute terms, Kafka adoption is also growing much faster. Pulsar had a bump in early 2020 corresponding to some popular blog posts, and we spent some time looking into them then, but that usage hasn't sustained. Indeed, if we look at the data over the last year, Pulsar usage has shrunk slightly.

This is obviously not positive news for Pulsar as a challenger system. In the last year, Pulsar has dropped in usage while Kafka's has nearly doubled.  

Why has Pulsar not caught on? I’m biased so feel free to disregard my take and substitute your own. Pulsar largely follows the same paradigm as Kafka, there isn’t really a big new idea, but its promise is to be more operable at a larger scale. Indeed, as a distributed system it has several interesting ideas and I think the Kafka community clearly learned a few ideas from this (just as the Pulsar folks clearly learned from Kafka). I think the issue Pulsar faces is that, in real-world use, the operability advantage doesn’t quite come to fruition:

1. Pulsar has some interesting ideas (really!), but is a very complex concatenation of different distributed systems, and not easy to run. The removal of ZooKeeper in Kafka makes this complexity differential even larger.
2. Pulsar did have interesting ideas around splitting storage and compute, but as soon as the tiered storage work happened in Apache Kafka it became mute. S3 etc. make a much better backing layer than BookKeeper as a cost-effective way of offloading storage. With the trend going firmly in that direction, Kafka has two tiers, Pulsar is stuck with three (and Zookeeper of course).
3. The largest, most complex users in the world are using Kafka, at a scale of trillions of messages per day. At large scale, all distributed data systems are complex to manage, but Kafka is the only system that has supported this.
4. Benchmarks always depend on the workload, but in pretty common scenarios Pulsar is about 2x slower. This is not surprising and is more or less a direct implication of their dual-write architecture.

Going forward, I think a purely operational value proposition is even weaker. Increasingly organizations are using hosted Kafka offerings rather than running it themselves. There are lots of tradeoffs in distributed systems implementation, but no silver bullet. They are all complex to run. However, a good cloud service can offload this problem to a company that can invest at hundreds of times the scale to do it really well. In my opinion Confluent has done this well with Confluent Cloud and I have to say that I'm at least a little proud of the step-change in functionality it offers, particularly the real-time elasticity, serverless pricing model and inherent load balancing.  

Perhaps Pulsar can reignite its growth but it does now look like an uphill battle. The streaming space differs from other infrastructure-software because streams are used to connect different systems together. Streaming connects to things: to apps in different programming languages, to data systems, SaaS layers, and programming frameworks. This is a classic network effect and no surprise that there is one since this is literally about connectivity. As a result, within an organization, there tends to be standardization on a single technology over time.  

Thoughtworks [noted this recently](https://www.thoughtworks.com/content/dam/thoughtworks/documents/radar/2021/10/tr_technology_radar_vol_25_en.pdf), “Kafka continues toward its status as a de facto standard" and noting that interest in competing platforms seemed to have waned. As it turns out, this isn’t just their impression, it is backed up by the data as well.  

So is Kafka unbeatable? No, of course not, but to overcome a strong network effect a competitor needs to come up with something truly new---a new paradigm or way of solving the problem that is 10x better across the board. A slightly different implementation of the same thing just isn’t sufficient.
