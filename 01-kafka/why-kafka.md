 ### Why use kafka
Given below senario, why use a kafka rather than direct write to iceberg table?
```mermaid
%%{init: {
  "theme": "dark",
  "flowchart": {
    "curve": "linear",
    "nodeSpacing": 40,
    "rankSpacing": 55
  }
}}%%

flowchart LR

%% =========================
%% Data Sources
%% =========================

subgraph SOURCES["Data Sources"]
    direction TB

    VALKEY["Data Source A<br/>Valkey / Redis PubSub"]

    REST["Data Source B<br/>REST API"]
end

%% =========================
%% Transformation Layer
%% =========================

subgraph TRANSFORMERS["Transformation Layer"]
    direction TB

    T1["PubSub Transformer<br/><br/>Valkey Message<br/>↓<br/>Unified Protobuf"]

    T2["REST Transformer<br/><br/>REST Response<br/>↓<br/>Unified Protobuf"]
end

%% =========================
%% Unified Event Stream
%% =========================

PROTO["Unified Protobuf Format<br/><br/>Common schema for both sources"]

KAFKA[("Kafka / Amazon MSK<br/><br/>Unified Event Topic")]

%% =========================
%% Streaming Processing
%% =========================

STREAM["Spark Structured Streaming<br/><br/>Consume Protobuf<br/>Validate and transform<br/>Convert to Avro"]

%% =========================
%% Data Lake
%% =========================

subgraph LAKEHOUSE["Iceberg Data Lakehouse"]
    direction TB

    ICEBERG[("Apache Iceberg Table<br/><br/>Unified Avro Format")]

    NESSIE["Project Nessie<br/><br/>Catalog and version control<br/>Branches, commits, rollback"]
end

%% =========================
%% Data Flow
%% =========================

VALKEY --> T1
REST --> T2

T1 --> PROTO
T2 --> PROTO

PROTO --> KAFKA

KAFKA --> STREAM

STREAM --> ICEBERG

NESSIE -. manages metadata .-> ICEBERG

%% =========================
%% Styling
%% =========================

classDef source fill:#20242b,stroke:#d7dce2,color:#ffffff,stroke-width:1.5px;
classDef transformer fill:#252a32,stroke:#d7dce2,color:#ffffff,stroke-width:1.5px;
classDef schema fill:#2b3038,stroke:#d7dce2,color:#ffffff,stroke-width:2px;
classDef kafka fill:#252a32,stroke:#d7dce2,color:#ffffff,stroke-width:2px;
classDef stream fill:#20242b,stroke:#d7dce2,color:#ffffff,stroke-width:2px;
classDef storage fill:#2b3038,stroke:#d7dce2,color:#ffffff,stroke-width:2px;
classDef catalog fill:#20242b,stroke:#d7dce2,color:#ffffff,stroke-width:1.5px;

class VALKEY,REST source;
class T1,T2 transformer;
class PROTO schema;
class KAFKA kafka;
class STREAM stream;
class ICEBERG storage;
class NESSIE catalog;

style SOURCES fill:#181b20,stroke:#d7dce2,color:#ffffff
style TRANSFORMERS fill:#181b20,stroke:#d7dce2,color:#ffffff
style LAKEHOUSE fill:#181b20,stroke:#d7dce2,color:#ffffff
```
1. Before answer the question, we should ask another question why use pubsub not kafka?
  - Kafka persists and replay records and track consumer progress, it is fault tolerance but slow. The pubsub transformer consumes data from a system could tolerate data loss fault but not latency. That is the reason we choosed pubsub over kafka. 
  - Now you know producer (pubsub) fast, consumer (spark) slow. We need kafka abosrb the throughput mismtach. Iceberg write is so heavy due to stateful spark operations such as snapshots commits, manifest file management, s3 multiuploads etc. Direct write will definitely slow down the ingestion progress. A big chance producer will shutdown their produce process due to your slow ingestion job.
2. Fan-in for multiple sources, both source publishes to the same format. Or you will need to create 2 separate iceberg writer.
3. Replay and reprocessing. 

### Debug a lag issue
1. First I would check if the lag belongs to which level. If it is not a partition level, the producer rate is 150000 msg/esc and consumer rate is 100000 msg/sec. Then I would start with the following cases:
  - caseA: If consumer reaches 95% CPU usage, start with increase consumer CPU before adding any partitions.
  - caseB: If consumer count < partition count, add consumers. 
  - caseC: If consumer count == partition count, optimize or scale up consumer vertically, then add partitions.
  - caseD: If CPU low but processing is slow. go check downstream system, batch db writes, increase concurrency, use async I/O etc.
2. Then if the lag comes from specific partition, such as other partition rate is 100000 msg/sec, but one partition is 5000 msg/sec. Might be a partition key skew issue. You need to redesign your partition key.
For example, in an airsearch system we use hash(search request) as the key and each message is one search result. Same request same partition. The design is to balance ordering and scalability.

  The pros are:
  - we keep per-search order, same search in same partition in order
  - easy to dedup, same search lands in same partition
  - different search in parallel
  - idempotent friendly

  Tradeoffs:
  - one big search in one partition
  - no order across searches but fine since consumers are idempotent.