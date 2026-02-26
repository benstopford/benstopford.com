---
title: "Delete Arbitrary Messages from a Kafka"
date: 2017-10-06
categories: 
  - "blogroll"
  - "kafkaconfluent"
slug: "delete-kafka-arbitrary-messages-trick"
---

I've been asked a few times about how you can delete messages from a topic in Kafka. So for example, if you work for a company and you have a central Kafka instance, you might want to ensure that you can delete any arbitrary message due to say regulatory or data protection requirements or maybe simple in case something gets corrupted.

A potential trick to do this is to use a combination of (a) a compacted topic and (b) a custom [partitioner](https://kafka.apache.org/0110/javadoc/org/apache/kafka/clients/producer/Partitioner.html) (c) a pair of [interceptor](https://kafka.apache.org/0110/javadoc/org/apache/kafka/clients/producer/ProducerInterceptor.html)s.

The process would follow:

- Use a producer [interceptor](https://kafka.apache.org/0110/javadoc/org/apache/kafka/clients/producer/ProducerInterceptor.html) to add a GUID to the end of the key before it is written.
- Use a custom [partitioner](https://kafka.apache.org/0110/javadoc/org/apache/kafka/clients/producer/Partitioner.html) to ignore the GUID for the purposes of partitioning
- Use a compacted topic so you can then delete any individual message you need via producer.send(key+GUID, null)
- Use a consumer [interceptor](https://kafka.apache.org/0110/javadoc/index.html?org/apache/kafka/clients/consumer/ConsumerInterceptor.html) to remove the GUID on read.

Two caveats: (1) Log compaction does not touch the most recent segment, so values will only be deleted once the first segment rolls. This essentially means it may take some time for the 'delete' to actually occur. (2) I haven't tested this!
