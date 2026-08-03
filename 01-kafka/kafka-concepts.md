## Kafka
Kafka is durable, reliable, distributed event-streaming platform.
- producer, publish event to a topic
- consumer, read records from partitions using offsets
- partition, the unit of parallelism and ordering. ordering is gurateed within one partition, not across partitions.
- message key, determine the partition and keep related entities together.
- consumer group, a partition is assigned to at most one consumer at a time.
- If consumers no. > partition no., extra consumers remain idle.
- Offset, record consumer progress. Lag is the difference between committed offset and latest offset.
- Replication, provide kafka fault-tolerance. 
- At-least-once delivery is common, so downstream processing should be idempotent.
- A rebalance distributes partitions can cause pause, duplicate processing and cache warm-up.

### How kafka scale?
Kafka devide a topic into multiple partitions. Each partition could be consumed independently, allowing producer and consumer process in parallel. 
When data volume increase, just increase partitions and scale up consumer and producer horizontally without changing app architecture.

### How kafka keep order?
Kafka reserve order only in partition level. To maintain the order of related events, producer should use a stable partition key so that all events with same business routed to the same partition.

### Kafka rebalance
A rebalance happened when consumer members change -- new consumer added, old consumer deleted, consumer crash, new partition added. 
During rebalance, consumer stop processing until partitions reassigned. Once done, consumer continue processing with lag.

In a spark structured streaming job I worked with which was consuming kafka topics, scaling executors or restarting app could trigger consumer group rebalance.
- Spark executors are not kafka consumers. Spark driver creates kafka consumer for each streaming query. When spark driver failed, old consumers left, after restarting, new consumers join which trigger a rebalance.
- Note that a new partition key does not mean a rebalance happen. Partition key != kafka key. For example, we use airline as parition key -- AA, UA, DL, BA. The actual kafka key is hash(partition key) % partitionCount.

We monitor lag and latency in datadog. Due to the spark checkpoint for offset management, we could recover the streaming job clearly without losing progress.
