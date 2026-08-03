---
title: "How to use Domain Events in Spring?"
slug: domain-events-no-spring
date: 2025-07-24T16:42:03
categories: ["Java", "Spring"]
tags: ["Java", "Spring"]
---
In the world of Domain-Driven Design (DDD), **Domain Events** are a powerful way to notify different parts of a system that something meaningful has occurred within the core domain.

Within the Spring ecosystem, event support is simple yet highly effective for **decoupling components**, improving cohesion, and enabling systems to evolve gracefully. In this post, you'll understand what Domain Events are, why to use them, and how to implement them in Spring with clarity and structure.

What are Domain Events?

![](https://natancode.com/wp-content/uploads/2023/10/image.gif)

A _Domain Event_ represents a significant occurrence in the domain model, such as:

*   An order was placed
*   A payment was processed
*   A user was registered

Instead of calling other components directly, a service can **publish an event**, and other parts of the system can **listen and react** to it — promoting loose coupling and clean architecture.

Benefits of Domain Events

🔁 **Loose coupling**: the event publisher doesn’t need to know about the listener

♻️ **Reusable and extendable**: easy to add new behaviors without touching existing code

📦 **Separation of concerns**: keeps business logic and side effects in separate places

Hands on

Create the Order class

```
public class Order {

    private Long id;

    public Order(Long id) {
        this.id = id;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }
}
```

Create the event record, can be a class.

```
public record OrderCreatedEvent(Order order) {

}
```

Publish the Event

```
import org.springframework.context.ApplicationEventPublisher;
import org.springframework.stereotype.Service;

@Service
public class OrderService {

    private final ApplicationEventPublisher publisher;

    public OrderService(ApplicationEventPublisher publisher) {
        this.publisher = publisher;
    }

    public void createOrder(Order order) {
        publisher.publishEvent(new OrderCreatedEvent(order));
    }
}
```

This is a service class that receives the ApplicationEventPublisher through dependency injection, so it is possible to publish the event.

Listen for the Event

```
import org.springframework.context.event.EventListener;
import org.springframework.stereotype.Component;

@Component
public class OrderCreatedListener {

    @EventListener
    public void handleOrderCreated(OrderCreatedEvent event) {
        System.out.println("Order generated successfully: " + event.order().getId());
    }
}
```

Use `@EventListener` to handle the event.

Conclusion

**Domain Events in Spring** offer a clean and effective way to apply DDD principles in practice, promoting **loose coupling** and improving the **maintainability** of your system. They are perfect for triggering **secondary actions** without cluttering the core business logic.

Use Domain Events to **broadcast meaningful state changes** in a scalable and elegant way. And remember: separation is the key to evolution. 🧠✅
