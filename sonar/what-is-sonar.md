---
title: "What is Sonar?"
slug: what-is-sonar
date: 2024-01-26T22:58:20
categories: ["Clean Code", "Secure", "Sonar"]
tags: ["Clean Code", "Secure"]
---
It's a code analysis tool that helps in writing clean and secure code.

Writing clean and secure code is a challenge. While manual code reviews are an option, leveraging a code analysis tool like Sonar is an excellent idea. This allows us to maintain clean and secure code, gaining several advantages:

*   Lower maintenance;
*   Rework less;
*   Minimize Risks;

Some companies that use it include

![](https://natancode.com/wp-content/uploads/2024/01/image.png)

Support

![](https://natancode.com/wp-content/uploads/2024/01/image-1.png)

Sonar products

There are three Sonar products that we can use, and they all share the same goal of identifying issues to keep the code clean and secure:

![](https://natancode.com/wp-content/uploads/2024/01/image-4.png)

It is installed in the IDE and performs real-time code analysis directly within the IDE to identify issues.

![](https://natancode.com/wp-content/uploads/2024/01/image-5.png)

It can be installed on the company's server for teams to use and can be integrated into the pipeline.

![](https://natancode.com/wp-content/uploads/2024/01/image-6.png)

It's a cloud-based solution that can be integrated into the pipeline.

In this article, we will explore SonarLint and SonarQube.

Quality Gate

It indicates whether the code has reached a standard good enough for release. Green means it's good, and red means it needs adjustments because it's not good enough for release.

![](https://natancode.com/wp-content/uploads/2024/01/image-7.png)

Image representing code ready for release.

![](https://natancode.com/wp-content/uploads/2024/01/image-16.png)

Sonar Flow

![](https://natancode.com/wp-content/uploads/2024/01/image-3.png)

I'll summarize the workflow.

While coding, the code can be analyzed in real-time within the IDE, as shown in the flow. After pushing to the remote repository, the pipeline runs, and the code is analyzed in SonarQube or SonarCloud. The Quality Gate checks the code quality. If it doesn't meet the quality standard, the task goes back to the developer for necessary adjustments. After the adjustment and another push to the remote repository, another analysis is performed. If it now meets the quality standard, the code can proceed to production.

Sonar Lint

In the following example, I demonstrate how to install SonarLint in IntelliJ.

![](https://natancode.com/wp-content/uploads/2024/01/image-8.png)

In my case, I already have it installed.

It resides in the upper right corner and also underlines the code when it detects any issue.

![](https://natancode.com/wp-content/uploads/2024/01/image-9.png)

Clicking on the icon provides more information about the issue, including examples of how to resolve it.

![](https://natancode.com/wp-content/uploads/2024/01/image-10.png)

After resolving the issue, the icon turns green, indicating that everything is fine in this file.

![](https://natancode.com/wp-content/uploads/2024/01/image-11.png)

Sonar Qube

The installation is simple, and for testing purposes, it can be done using Docker. After installation, it's necessary to create a project, and through the command line, we can send the code for analysis.

![](https://natancode.com/wp-content/uploads/2024/01/image-15.png)

Example of usage in SonarQube

After the code analysis, we can identify the issues.

![](https://natancode.com/wp-content/uploads/2024/01/image-12.png)

Clicking on the issue provides more details.

![](https://natancode.com/wp-content/uploads/2024/01/image-13.png)

![](https://natancode.com/wp-content/uploads/2024/01/image-14.png)

Monitoring

We can also monitor the quality.

![](https://natancode.com/wp-content/uploads/2024/01/image-17-300x148.png)

Security

It's essential to note that the OWASP project is present in Sonar, which is crucial for promoting secure code.

![](https://natancode.com/wp-content/uploads/2024/01/image-18-300x152.png)

Updates

In recent versions, there have been changes in issue types and severity.

![](https://natancode.com/wp-content/uploads/2024/01/image-19-300x103.png)

![](https://natancode.com/wp-content/uploads/2024/01/image-20-300x191.png)

Conclusion

The use of Sonar greatly helps in building clean and secure code. This brings many advantages such as less maintenance, rework less, minimizes risks, and ensures code security and readability. Another advantage is that we can monitor quality.
