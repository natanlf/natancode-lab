---
title: "&#x1f680; Topics, Partitions, and Offsets in Kafka — How do they work?"
slug: topics-partitions-and-offsets-in-kafka-how-do-they-work
date: 2025-12-16T15:03:49
categories: ["Kafka"]
tags: ["Kafka"]
---
Kafka is currently one of the most widely used streaming platforms in the world.  
But to use it effectively, you need to master three essential concepts:

*   **Topics**
*   **Partitions**
*   **Offsets**

![](https://natancode.com/wp-content/uploads/2025/12/Kafka-topic-partition-and-offsets.jpg)

These three elements form the foundation of how Kafka’s entire architecture works. Let’s break each one down with simple examples, real-world scenarios, and analogies.

# **1\. What Is a Topic in Kafka?**

A topic is like the name of the channel where messages will be published.

It is similar to:

*   a table in a database
*   a queue
*   a specific subject

For example, if you have an e-commerce system, you might have topics such as:

*   payments
*   orders
*   sent-emails
*   stock-updated

A topic is just a name.

Through Kafka producers, data is placed into the topic and can be read by Kafka consumers.

# **2\. What Are Partitions in Kafka?**

A topic can have multiple partitions.  
Think of the topic as a file, and each partition as a portion of that file organized in blocks.

Messages produced are sent to the topic, and Kafka automatically routes them to a specific partition.

### 📌 Why do partitions exist?

They provide:

### ✔ Scalability

Multiple partitions can be distributed across brokers.

### ✔ Guaranteed Ordering

Within each partition, message order is always guaranteed.  
But across different partitions? There is NO global ordering.

## 🔍 Visual Example

Topic: payments  
Partitions: 3

```
payments
 ├── Partition 0: msg0, msg1, msg2, msg3...
 ├── Partition 1: msg0, msg1, msg2...
 └── Partition 2: msg0, msg1, msg2, msg3, msg4...
```

About the messages:

*   messages are only appended at the end
*   they are never modified
*   they are never reordered

# **3\. What Is an Offset?**

Within each partition, when a message is written, an ID is created with an incremental number starting from zero.  
Each number is an offset.

Example — Partition 0:  
Offset 0 -> message A  
Offset 1 -> message B  
Offset 2 -> message C

The data written into partitions is immutable, meaning it cannot be changed.

### ✔ The offset represents the position of the message within the partition

It is not a global ID.  
It is not unique across the entire topic.  
It is unique per partition.

### Important Observations

Offset 2 in partition 1 is not the same as offset 2 in partition 2.

Concept

What it is

Why it matters

Topic

Category of messages

Organizes information

Partition

Subdivision of the topic

Parallelism, scalability, and ordering

Offset

Message position within the partition

Reading control and reprocessing

# ⭐ Golden Rules for Understanding Kafka

#### 🔸 Ordering is guaranteed within a partition, not across the topic.  
🔸 Data in Kafka is stored for a retention period (after one week the data will disappear), by default one week, but this period can be modified.  
🔸 Data goes to a partition randomly unless a key is used.  
🔸 You can have as many partitions as you want.  
🔸 Writes are distributed across partitions.  
🔸 Partitions determine the system’s scalability.

![](https://natancode.com/wp-content/uploads/2025/12/giphy.gif)
