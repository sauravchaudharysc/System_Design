## Kafka 

Apache Kafka is a distributed **event streaming platform** used to build real-time data pipelines and streaming applications that stores data as an immutable log and allows multiple consumers to read it independently, unlike traditional message queues.

An **event streaming platform** is a system designed to **capture, store, and process streams of events in real time**.

Unlike traditional queues, Kafka:

- Stores data **persistently**
- Allows **multiple consumers to read independently**
- Handles **massive scale (millions of messages/sec)**

Think of **Apache Kafka** as:

> A **distributed append-only log system** where data is written once and read many times independently.

Everything (topics, partitions, consumers) revolves around this idea.



## 1. How Kafka Works (Step-by-Step Flow)

### Step 1: Producer sends message

- Producer sends a record to a **topic**
- It can specify a **key**, which Kafka uses to decide the partition
- If no key → partitioning is typically round-robin (or custom partitioner)

### Step 2: Message is appended

- Message is written to the **end of a partition (append-only log)**
- Each message gets a **unique offset within that partition**

This is why Kafka is fast:
 **Sequential disk writes (O(1))**, not random I/O

### Step 3: Replication happens

- Each partition has:

  - **1 leader**
  - **multiple followers**

- Producers write only to the **leader**.

- Followers replicate data from the leader.

  > Replication is **asynchronous by default**, but durability depends on:
  >
  > - `acks=all`
  > - in-sync replicas (ISR)
  >
  > ISR is the set of replicas that are fully caught up with the leader. When a producer uses `acks=all`, the leader waits for all ISR replicas to replicate the message before acknowledging. This ensures durability, because if the leader fails, another in-sync replica can take over without data loss.

### Step 4: Consumer reads

- Reads (subscribes to) data from topics
- Consumers **pull** messages from partitions
- They track their own **offset**
- Multiple consumers (consumer group) split partitions among themselves

>
>  Consumers don’t delete data—they just move their offset forward

##  2. Key Components

### Topic

A **topic** is a **logical namespace** that groups a stream of records. In simple word, it is just a **logical category of data**.

Example : Order, Payment, Logs etc. 

Producers write to a topic, consumers read from a topic. But topics don’t store data directly — **partitions do**.

### Partition

A **partition** is a **physically stored, ordered log file**. This is the actual unit of storage inside a topic.

- Each topic is split into multiple partitions
- Each partition:
  - Is **append-only**
  - Has **strict ordering** (Ordering is guaranteed **within a partition**)
  - Is stored/distributed across broker/machines (Provides Scalability - Enables **horizontal scaling** )
  - Each partition can be consumed independently
- Each message has an **offset** - (Consumers track offsets themselves)

```cpp
Topic: orders
Partitions:
  P0 → [msg1, msg2, msg3]
  P1 → [msgA, msgB]
```

### Broker

A **broker/machine** is a Kafka server (node in cluster). Cluster : Group of multiple brokers working together. To Provide Scalability and Fault Tolerance. 

- Stores partitions
- Handles reads/writes

## Example

### Topics + Partitions + Consumer Groups (Clean Structure)

#### Setup

- **Topic:** `orders`
- **Partitions:** P0, P1, P2
- **Consumer Groups:** We have two consumer groups, one for performing analytics other for fraud detection.
  - Group A → *Analytics*
  - Group B → *Fraud Detection*

#### **Partition Distribution (conceptual)**

```
orders topic
 ├── P0
 ├── P1
 └── P2
```

Each partition is Independently consumed

#### **Consumer Group A (Analytics)**

| Consumer | Partition |
| -------- | --------- |
| C1       | P0        |
| C2       | P1        |
| C3       | P2        |

Each consumer handles **one partition**
No overlap within the group

#### **Consumer Group B (Fraud Detection)**

| Consumer | Partition |
| -------- | --------- |
| C4       | P0        |
| C5       | P1        |
| C6       | P2        |

Using Same partitions, but **different consumer group**
Completely independent processing

### Key Observations

#### 1. Within a consumer group

- Each partition is consumed by **only one consumer**
- Ensures:
  - no duplicate processing
  - parallelism

#### 2. Across consumer groups

- Each group gets **full data**

 So:

- Group A reads: P0, P1, P2
- Group B also reads: P0, P1, P2

But:

- They maintain **separate offsets**

#### 3. Why this design?

- Analytics and fraud detection are **independent use cases**
- Kafka allows:
  - multiple systems to consume same data
  - without interfering with each other

> “A topic is divided into partitions, and within a consumer group, each partition is consumed by only one consumer. However, multiple consumer groups can independently consume the same topic, each maintaining its own offsets, enabling different applications to process the same data in parallel.”

### Common Follow-up

 *What if consumers < partitions?*

- Some consumers will handle **multiple partitions**

 *What if consumers > partitions?*

- Extra consumers will stay **idle**

Here are **clean, interview-ready points** for all three — with the ACK concept properly reflected:

# Message/Task Queue/Event Stream Comparison

## 🔴 Redis (+ Celery)

- In-memory → **very fast, low latency**
- Used as a **task queue** with Celery for background jobs
- Limited durability (unless persistence like RDB/AOF is enabled)
- No strong built-in ACK model like RabbitMQ (Celery handles retries)
- Best for **lightweight async tasks** (emails, caching, short jobs)

------

## 🐰 RabbitMQ

> “Do this task and tell me when it's done”

- Traditional **message broker (queue-based)**
- **Consumer sends ACK** after processing (core reliability feature)
- Message is **deleted only after ACK** → ensures at-least-once delivery
- Supports retries, re-queuing, and complex routing
- Best for **reliable task processing & work queues**

------

## 🚀 Apache Kafka

> “This event happened, whoever cares can read it”

- **Distributed event streaming platform (log-based system)**
- **Producer ACK controls durability** (`acks=0,1,all`)
- Consumers use **offset commit (not traditional ACK)**
- Messages are **retained (not deleted after consumption)** → supports replay
- Best for **high-throughput pipelines, analytics, event-driven systems**

------

## Decision Tree: When to Use What

### What is your primary use case?

#### “I need to process background tasks (jobs)”

→ Go to **Task Queue**

#### “I need to stream events / data pipelines / analytics”

→ Go to **Event Streaming**

------

### If Task Queue → Choose between Redis vs RabbitMQ

#### Need ultra-fast, simple, lightweight jobs?

Use **Redis + Celery**

- Fire-and-forget tasks
- Short-lived jobs
- Can tolerate occasional loss / retry via app logic
- Example: emails, notifications, small async jobs

#### Need strong reliability & guaranteed processing?

 Use **RabbitMQ**

- Critical tasks (payments, orders)
- Need **consumer ACK**
- Want retries, dead-letter queues
- Complex routing (fanout, topic exchange)

#### Need high throughput + replay + multiple consumers?

→ Use **Apache Kafka**

- Real-time analytics
- Event-driven microservices
- Log pipelines
- Data ingestion systems

### Quick Decision Summary

#### Use Redis (+ Celery) if:

- Speed > reliability
- Simple async jobs
- Low complexity

#### Use RabbitMQ if:

- Reliability is critical
- Need **consumer ACK-based processing**
- Task must not be lost

#### Use Kafka if:

- Need **streaming + persistence + replay**
- Multiple independent consumers
- Massive scale (big data / real-time pipelines)

