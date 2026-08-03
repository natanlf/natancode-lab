---
title: "How to use Kafka with Quarkus?"
slug: how-to-use-kafka-with-quarkus
date: 2025-06-12T15:14:12
categories: ["Java", "Kafka", "Quarkus"]
tags: ["Java", "Kafka", "Quarkus"]
---
**Apache Kafka** is one of the most popular solutions for large-scale distributed event streaming. Its ability to handle high volumes of messages makes it ideal for event-driven architectures. In this post, you’ll learn how to use Kafka with **Quarkus**, a modern framework for cloud-native Java applications.

Best of all: Quarkus' Kafka support also works with services like **Azure Event Hub**, making it even more flexible for enterprise environments.

What is Kafka?

Kafka is a distributed messaging platform based on the _publish-subscribe_ model. In short:

*   **Publisher**: produces (sends) messages to a topic.
*   **Subscriber**: consumes (reads) messages from these topics.

Messages are stored in the queue until they are read. This ensures reliability and decouples producers from consumers.

Hands on

![](https://natancode.com/wp-content/uploads/2025/05/giphy.gif)

Kafka needs to be running, and in a previous post I explained how to do that using Docker. If you’re unsure, I recommend checking out the following post:

https://natancode.com/2025/05/22/how-to-start-kafka-using-docker/

Adding the Dependency to Quarkus

In your `pom.xml`, add the following dependency for Kafka:

```
<dependency>
    <groupId>io.quarkus</groupId>
    <artifactId>quarkus-messaging-kafka</artifactId>
</dependency>
```

Configuring `application.properties`

Here’s an example configuration:

```
# Kafka bootstrap
kafka.bootstrap.servers=localhost:9092

# Producer
mp.messaging.outgoing.my-topic-out.connector=smallrye-kafka
mp.messaging.outgoing.my-topic-out.topic=my-topic
mp.messaging.outgoing.my-topic-out.value.serializer=org.apache.kafka.common.serialization.StringSerializer

# Consumer
mp.messaging.incoming.my-topic-in.connector=smallrye-kafka
mp.messaging.incoming.my-topic-in.topic=my-topic
mp.messaging.incoming.my-topic-in.value.deserializer=org.apache.kafka.common.serialization.StringDeserializer
```

In addition to specifying the Kafka host and port, we also define settings for the **producer**, which emits messages, and the **consumer**, which reads them.

*   `outgoing` defines the producer.
*   `incoming` defines the consumer.
*   `my-topic` is the Kafka topic name.
*   Using `StringSerializer` is handy for sending simple strings, JSON, or UUIDs.

With these configurations in place, we can proceed.

```
import jakarta.enterprise.context.ApplicationScoped;
import org.eclipse.microprofile.reactive.messaging.Channel;
import org.eclipse.microprofile.reactive.messaging.Emitter;

@ApplicationScoped
public class KafkaProducerService {

    @Channel("my-topic-out")
    Emitter<String> emitter;

    public void sendMessage(String message) {
        emitter.send(message);
    }
}
```

`@ApplicationScoped` is used to declare a Bean in Quarkus.

`@Channel("my-topic-out")` specifies the name of the topic — matching the producer property configuration. We use this along with an `Emitter<String>` to send messages. Calling the `send()` method allows us to produce and send messages to Kafka.

```
import jakarta.enterprise.context.ApplicationScoped;
import org.eclipse.microprofile.reactive.messaging.Incoming;

@ApplicationScoped
public class KafkaConsumerService {

    @Incoming("my-topic-in")
    public void consumer(String message) {
        System.out.println("Received message: " + message);
    }
}

```

`@Incoming("my-topic-in")` is used to define the consumer. The value provided in the annotation must match the one in the properties file.

This is the basic setup required to get it working. There are also advanced configuration options available to further optimize performance, for example.

Conclusion

Integrating Kafka with Quarkus is surprisingly straightforward. With just a few lines of configuration and code, you can enable robust, efficient, and scalable asynchronous communication. This approach is ideal for modern architectures built around microservices and events. Plus, extended support for Azure Event Hub offers added flexibility for enterprise applications.

By mastering this integration, you’ll be well-equipped to build resilient, decoupled, and high-performance applications.
