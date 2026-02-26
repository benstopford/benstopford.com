---
title: "Handling GDPR: How to make Kafka Forget"
date: 2017-12-04
categories: 
  - "blogroll"
slug: "handling-gdpr-make-kafka-forget"
---

If you follow the press around Kafka you’ll probably know it’s pretty good at tracking and retaining messages, but sometimes removing messages is important too. [GDPR](http://www.itpro.co.uk/it-legislation/27814/what-is-gdpr-everything-you-need-to-know-8) is a good example of this as, amongst other things, it includes [the right to be forgotten](https://en.wikipedia.org/wiki/Right_to_be_forgotten). This begs a very obvious question: how do you delete arbitrary data from Kafka? It’s an immutable log after all.<!--more-->

As it happens Kafka is a pretty good fit for GDPR as, along with the right to be forgotten, users also have the right to request a copy of their personal data. Companies are also required to keep detailed records of what data is used for — a requirement where recording and tracking the messages that move from application to application is a boon.

### **How do you delete (or redact) data from Kafka?**

The simplest way to remove messages from Kafka is to simply let them expire. By default Kafka will keep data for two weeks and you can tune this as required. There is also an Admin API that lets you [delete messages explicitly](https://cwiki.apache.org/confluence/display/KAFKA/KIP-107%3A+Add+purgeDataBefore%28%29+API+in+AdminClient) if they are older than some specified time or offset. But what if we are keeping data in the log for a longer period of time, say for [Event Sourcing](https://www.confluent.io/blog/messaging-single-source-truth/) use cases or as a [source of truth](https://www.thoughtworks.com/radar/techniques/event-streaming-as-the-source-of-truth)? For this you can make use of  [Compacted Topics](https://kafka.apache.org/documentation/#compaction), which allow messages to be explicitly deleted or replaced by key.

Data isn’t removed from Compacted Topics in the same way as say a relational database. Instead Kafka uses a mechanism closer to those used by Cassandra and HBase where records are marked for removal then later deleted when the compaction process runs.

To make use of this you configure the topic to be compacted and then send a delete event (by sending a null message, with the key of the message you want to delete). When compaction runs the message will be deleted forever.

```
//Create a record in a compacted topic in kafka
producer.send(new ProducerRecord(CUSTOMERS_TOPIC, “Donald Trump”, “Job: Head of the Free World, Address: The White House”));
//Mark that record for deletion when compaction runs
producer.send(new ProducerRecord(CUSTOMERS_TOPIC, “Donald Trump”, null));
```

If the key of the topic is something other than the CustomerId then you need some process to map the two. So for example if you have a topic of Orders, then you need a mapping of Customer->OrderId held somewhere. Then to 'forget' a customer simply lookup their Orders and either explicitly delete them, or alternatively redact any customer information they contain. You can do this in a KStreams job with a State Store or alternatively roll your own.

There is a more unusual case where the key (which Kafka uses for ordering) is completely different to the key you want to be able to delete by. Let’s say that, for some reason, you need to key your Orders by ProductId. This wouldn’t be fine-grained enough to let you delete Orders for individual customers so the simple method above wouldn't work. You can still achieve this by using a key that is a composite of the two: \[ProductId\]\[CustomerId\] then using a custom partitioner in the Producer (see the [Producer Config](https://kafka.apache.org/documentation/#producerconfigs): “partitioner.class”) which extracts the ProductId and uses only that subsection for partitioning. Then you can delete messages using the mechanism discussed earlier using the \[ProductId\]\[CustomerId\] pair as the key.

### **What about the databases that I read data from or push data to?**

Quite often you’ll be in a pipeline where Kafka is moving data from one database to another using [Kafka Connectors](https://www.confluent.io/product/connectors/). In this case you need to delete the record in the originating database and have that propagate through Kafka to any Connect Sinks you have downstream. If you’re using CDC this will just work: the delete will be picked up by the source Connector, propagated through Kafka and deleted in the sinks. If you’re not using a CDC enabled connector you’ll need some custom mechanism for managing deletes.

### **How long does Compaction take to delete a message?**

By default compaction will run periodically and won't give you a clear indication of when a message will be deleted. Fortunately you can tweak the settings for stricter guarantees. The best way to do this is to configure the compaction process to run continuously, then add a rate limit so that it doesn't doesn't affect the rest of the system unduly:

```
# Ensure compaction runs continuously with a very low cleanable ratio
log.cleaner.min.cleanable.ratio = 0.00001 
# Set a limit on compaction so there is bandwidth for regular activities
log.cleaner.io.max.bytes.per.second=1000000 

```

Setting the cleanable ratio to 0 would make compaction run continuously. A small, positive value is used here, so the cleaner doesn’t execute if there is nothing to clean, but will kick in quickly as soon as there is. A sensible value for the log cleaner max I/O is \[max I/O of disk subsystem\] x 0.1 / \[number of compacted partitions\]. So say this computes to 1MB/s then a topic of 100GB will clean removed entries within 28 hours. Obviously you can tune this value to get the desired guarantees.

One final consideration is that partitions in Kafka are made from a set of files, called segments, and the latest segment (the one being written to) isn't considered for compaction. This means that a low throughput topic might accumulate messages in the latest segment for quite some time before rolling, and compaction kicking in. To address this we can force the segment to roll after a defined period of time. For example log.roll.hours=24 would force segments to roll every day if it hasn't already met its size limit. 

### **Tuning and Monitoring**

There are a number of configurations for tuning the compactor (see properties log.cleaner.\* in the [docs](https://kafka.apache.org/documentation/#brokerconfigs)) and the compaction process publishes [JMX metrics](https://issues.apache.org/jira/browse/KAFKA-1327?focusedCommentId=13956822&page=com.atlassian.jira.plugin.system.issuetabpanels:comment-tabpanel#comment-13956822) regarding its progress. Finally you can actually set a topic to be both compacted and have an expiry (an undocumented [feature](https://cwiki.apache.org/confluence/display/KAFKA/KIP-71%3A+Enable+log+compaction+and+deletion+to+co-exist)) so data is never held longer than the expiry time.

### **In Summary**

Kafka provides immutable topics where entries are expired after some configured time, compacted topics where messages with specific keys can be flagged for deletion and the ability to propagate deletes from database to database with CDC enabled Connectors.
