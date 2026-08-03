---
title: "&#x1f680; Introduction to Amazon EC2 (Elastic Compute Cloud)"
slug: introduction-to-amazon-ec2-elastic-compute-cloud
date: 2025-08-26T13:53:41
categories: ["AWS", "Cloud"]
---
**Amazon EC2 (Elastic Compute Cloud)** is one of the most popular and foundational services in AWS. It allows you to **create and manage virtual machines (instances)** in the cloud with ease, scalability, and full control over the operating system, storage, and networking.

With EC2, you can run anything from small web applications to high-performance systems, all with **pay-as-you-go pricing** and **elasticity** to scale according to your needs.

![](https://natancode.com/wp-content/uploads/2025/05/giphy.gif)

## 🧠 Types of EC2 Instances

AWS provides a wide range of **EC2 instance types**, each optimized for specific workloads. Here are the main categories:

*   **General Purpose**
    *   Examples: `t3`, `t4g`, `m6i`
    *   Balanced CPU, memory, and networking resources. Great for web servers, small databases, and general applications.
*   **Compute Optimized**
    *   Examples: `c6a`, `c7g`
    *   Designed for CPU-intensive workloads like gaming, batch processing, API servers...
*   **Memory Optimized**
    *   Examples: `r6i`, `x2idn`
    *   Ideal for memory-heavy applications such as in-memory databases and large caches.
*   **Storage Optimized**
    *   Examples: `i4i`, `d3en`
    *   High IOPS and throughput for NoSQL databases, data warehouses, and log processing.
*   **Accelerated Computing**
    *   Examples: `p4`, `inf2`
    *   Equipped with GPUs, great for ML training, inference, and rendering tasks.

## 💳 EC2 Pricing Models

Amazon EC2 offers **flexible pricing options** to suit different use cases:

1.  **On-Demand**
    *   Pay by the hour or second, no long-term commitments.
    *   Ideal for testing or temporary workloads.
2.  **Spot Instances**
    *   Use unused EC2 capacity at up to **90% discount** off compared to On-Demand pricing.
    *   Great for fault-tolerant, flexible jobs like big data or rendering.
    *   Takes instances that AWS is not using but may use the instance in the future.
3.  **Savings Plans**
    *   Commit to consistent hourly usage (EC2 or compute-based).
    *   Offers flexibility and cost savings over time.
    *   Long term contract that provides discount (for a 1 or 3 year term).
4.  **Hosts Dedicados**
    *   They are quite expensive because you pay for the host (physical server). Examples of those who use them are the financial sector and governments.

## 💰 How Much Does EC2 Cost?

EC2 pricing varies based on:

*   Instance type (e.g., `t3.micro` vs `r6i.8xlarge`)
*   Region (São Paulo is often more expensive than N. Virginia)
*   Pricing model (on-demand, reserved, etc.)
*   Operating system (Linux is typically cheaper than Windows)

💡 To estimate costs, AWS provides the **AWS Pricing Calculator**. It helps you simulate:

*   Uptime hours per month
*   Instance type
*   Network traffic
*   Storage (EBS)
*   And more...

📌 _Tip:_ A `t3.micro` instance is eligible for **750 free hours/month for 12 months** under the AWS Free Tier.

## ✅ Conclusion

**Amazon EC2** is a powerful, flexible, and scalable service to run your applications in the cloud. With a variety of instance types, adaptable pricing models, and deep integration with the AWS ecosystem, it’s ideal for developers, startups, and enterprises.

However, it’s crucial to understand **which instance type to choose**, **which pricing plan to apply**, and **how to estimate your monthly cost** to avoid billing surprises.

Always take advantage of the **AWS Pricing Calculator**, and if you're just starting, explore the **Free Tier** to experiment at no cost.
