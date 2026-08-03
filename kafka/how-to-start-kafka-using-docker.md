---
title: "How to start Kafka using Docker?"
slug: how-to-start-kafka-using-docker
date: 2025-05-22T14:52:20
categories: ["Docker", "Kafka"]
tags: ["Docker", "Kafka"]
---
It’s possible to use Kafka on various operating systems, but here we’ll use **Docker on Ubuntu Linux**. We need Docker Desktop installed in order to proceed. You can follow this tutorial to install it:  
👉 [tutorial](https://docs.docker.com/desktop/setup/install/linux/) .

Go to **Docker Hub** and in the search bar, look for **“apache kafka”**. This will allow us to download the image needed to create a container.

Hands on

![](https://natancode.com/wp-content/uploads/2025/04/giphy.gif)

![](https://natancode.com/wp-content/uploads/2025/05/image-1024x527.png)

After clicking on the **Apache Kafka** image, download it by clicking **Pull**, then go to the **Run** option.

![](https://natancode.com/wp-content/uploads/2025/05/image-1-1024x527.png)

Set the **container name** and **port**, then click **Run**.

![](https://natancode.com/wp-content/uploads/2025/05/image-2.png)

The log should indicate that everything is running properly.

![](https://natancode.com/wp-content/uploads/2025/05/image-3-1024x541.png)

Now, in the **Exec** tab, we can run commands inside the `/opt/kafka` directory.

```
cd opt
```

```
cd kafka
```

```
bin/kafka-topics.sh --create --topic my-topic --bootstrap-server localhost:9092 --partitions 3 --replication-factor 1
```

This way, we can create a **topic**.

```
bin/kafka-topics.sh --list --bootstrap-server localhost:9092
```

Use the following command to **list topics**.

![](https://natancode.com/wp-content/uploads/2025/05/image-4-1024x541.png)

Conclusion

This is a practical way to run **Kafka locally** without installing it directly on your operating system.
