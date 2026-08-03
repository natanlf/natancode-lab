---
title: "What's the difference between Concurrency and Parallelism?"
slug: whats-the-difference-between-concurrency-and-parallelism
date: 2025-05-14T17:29:02
categories: ["Java", "Quarkus", "Spring"]
tags: ["Java", "Quarkus", "Spring"]
---
**Concurrency** and **Parallelism** are important concepts for bringing more efficiency to software, but they do not mean the same thing. A software application can handle multiple tasks, and the way these tasks are processed differs in each concept.

Concurrency

Imagine the scenario where we have a software that needs to process multiple tasks, such as processing user inputs, making HTTP requests, etc. **We have a single core in the CPU to process, so the CPU quickly switches between tasks to process them for a period of time**. However, this is so fast that it appears everything is being processed simultaneously, but it's not; it just seems that way. This switching is called **context switching**. It is important to be cautious with context switching, as excessive switching can hinder performance.

![](https://natancode.com/wp-content/uploads/2025/05/concurrency-w.png)

Let's look at a simpler example.

![](https://natancode.com/wp-content/uploads/2025/01/giphy.webp)

A worker doing several tasks, but they are not simultaneous, needs to switch between them. Even if they quickly perform a bit of each, it is still not simultaneous.

![](https://natancode.com/wp-content/uploads/2025/02/image.png)

Parallelism

**With multiple CPU cores, we can execute tasks simultaneously. Each core handles a different task independently at the same time.**

![](https://natancode.com/wp-content/uploads/2025/05/parallelism-white.png)

Imagine in the previous example, now the worker is no longer alone; there are others working with him, and each one is doing a different task at the same time.

Conclusion

While concurrency focuses on handling multiple tasks efficiently, parallelism aims to execute several tasks at the same time to speed up processing. In real-world applications, these concepts often combine to optimize performance and scalability.
