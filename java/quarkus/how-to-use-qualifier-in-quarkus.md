---
title: "How to use Qualifier in Quarkus?"
slug: how-to-use-qualifier-in-quarkus
date: 2024-05-07T00:07:19
categories: ["Java", "Quarkus"]
tags: ["Java", "Quarkus"]
---
Imagine the following scenario where there are multiple implementations for the same interface, and when using dependency injection, the software needs to understand which implementation to use. Let's illustrate with an example to understand what the problem is and how to solve it.

![](https://natancode.com/wp-content/uploads/2024/04/image.png)

Website Diagram: [https://refactoring.guru/](https://refactoring.guru/)

In the example, we will have a delivery system that can be done by truck or ship, where there is a service that requests the delivery of a product to a customer.

Let’s start with the classes Client and Product.

```
package com.natancode.model;

public class Client {

    private String name;
    private String email;

    public Client(String name, String email) {
        this.name = name;
        this.email = email;
    }

    public String getName() {
        return name;
    }

    public String getEmail() {
        return email;
    }

    @Override
    public String toString() {
        return "Client{" +
                "name='" + name + '\'' +
                ", email='" + email + '\'' +
                '}';
    }
}
```

* * *

```
package com.natancode.model;

import java.math.BigDecimal;

public class Product {

    private String name;
    private BigDecimal value;

    public Product(String name, BigDecimal value) {
        this.name = name;
        this.value = value;
    }

    public String getName() {
        return name;
    }

    public BigDecimal getValue() {
        return value;
    }

    @Override
    public String toString() {
        return "Product{" +
                "name='" + name + '\'' +
                ", value=" + value +
                '}';
    }
}
```

Now let’s create the Transport interface and its implementations.

```
package com.natancode.transportation;

import com.natancode.model.Client;
import com.natancode.model.Product;

public interface Transport {
    String deliver(Client client, Product product);
}
```

* * *

```
package com.natancode.transportation;

import com.natancode.model.Client;
import com.natancode.model.Product;
import jakarta.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class Truck implements Transport {
    @Override
    public String deliver(Client client, Product product) {
        return "Deliver by land in a box the product " + product.getName() + " to " + client.getName();
    }
}
```

* * *

```
package com.natancode.transportation;

import com.natancode.model.Client;
import com.natancode.model.Product;
import jakarta.enterprise.context.ApplicationScoped;

@ApplicationScoped
public class Ship implements Transport {
    @Override
    public String deliver(Client client, Product product) {
        return "Deliver by sea in a container the product " + product.getName() + " to " + client.getName();
    }
}
```

* * *

**_Annotation_**

_**Description**_

ApplicationScoped

A single bean instance is used for the application and shared among all injection points. The instance is created lazily, i.e. once a method is invoked upon the [client proxy](https://quarkus.io/guides/cdi#client_proxies).

Let's create OrderService.

```
package com.natancode.service;

import com.natancode.model.Client;
import com.natancode.model.Product;
import com.natancode.transportation.Transport;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;

@ApplicationScoped
public class OrderService {

    @Inject
    Transport transport;

    public String requestDelivery(Client client, Product product) {
        return this.transport.deliver(client, product);
    }
}
```

* * *

The `@Inject` annotation serves to perform _**dependency injection**_.

Let's create a simple endpoint just to make the request to the `OrderService`.

```
package com.natancode.resources;

import com.natancode.model.Client;
import com.natancode.model.Product;
import com.natancode.service.OrderService;
import jakarta.inject.Inject;
import jakarta.ws.rs.GET;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.Produces;
import jakarta.ws.rs.core.MediaType;

import java.math.BigDecimal;

@Path("/order")
public class OrderResource {

    @Inject
    OrderService orderService;

    @GET
    @Produces(MediaType.TEXT_PLAIN)
    public String requestOrder(){

        Client nick = new Client("Nick", "nick@fakedomain.com");
        Product galaxy24 = new Product("Samsung Galaxy S24 Ultra", new BigDecimal("1149.99"));

        return orderService.requestDelivery(nick, galaxy24);
    }
}
```

* * *

We use Dependency Injection in the `OrderService` with the `@Inject` annotation.

When running `clean install`, we get the following result.

[![](https://natancode.com/wp-content/uploads/2024/05/Qualifier-300x47.jpg)](https://natancode.com/wp-content/uploads/2024/05/Qualifier.jpg)

Click to Zoom

This means that it was not possible to determine which implementation to use; it's an Ambiguous Dependency for the Transport interface.

We need to qualify which implementation will be used, so the **_@Qualifier_** will help us with that.

Solving with the @Named Annotation

We should use the **_@Named annotation_** in the implementations, specifying a value for each one, as shown in the examples:

```
@Named("ship")
@ApplicationScoped
public class Ship implements Transport {
    @Override
    public String deliver(Client client, Product product) {
        return "Deliver by sea in a container the product " + product.getName() + " to " + client.getName();
    }
}
```

* * *

```
@Named("truck")
@ApplicationScoped
public class Truck implements Transport {
    @Override
    public String deliver(Client client, Product product) {
        return "Deliver by land in a box the product " + product.getName() + " to " + client.getName();
    }
}
```

* * *

Now what we need to do is specify in the OrderService, as that's where we perform the dependency injection.

```
@Named("ship")
@Inject
Transport transport;
```

* * *

This way, we specify which implementation we want to use and solve the problem.

![](https://natancode.com/wp-content/uploads/2024/05/image.png)

Solving with Custom Annotation

First, let's create an Enum with the possible values.

```
package com.natancode.transportation;

public enum TypeOfTransportEnum {
    SHIP,
    TRUCK
}
```

* * *

Let's create an annotation that specifies the type of transport.

```
package com.natancode.transportation;

import jakarta.inject.Qualifier;

import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;

@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface TypeOfTransport {
    TypeOfTransportEnum value();
}
```

* * *

It needs to run at Runtime, and we need to specify the **_@Qualifier annotation_**. The value is one of the enums we created, such as SHIP or TRUCK.

Now we need to specify the annotation we created and the value for each implementation. For example, in the Ship implementation, we add:

```
@TypeOfTransport(TypeOfTransportEnum.SHIP)
```

* * *

And for Truck:

```
@TypeOfTransport(TypeOfTransportEnum.TRUCK)
```

* * *

```
@TypeOfTransport(TypeOfTransportEnum.SHIP)
@ApplicationScoped
public class Ship implements Transport {
    @Override
    public String deliver(Client client, Product product) {
        return "Deliver by sea in a container the product " + product.getName() + " to " + client.getName();
    }
}
```

* * *

```
@TypeOfTransport(TypeOfTransportEnum.TRUCK)
@ApplicationScoped
public class Truck implements Transport {
    @Override
    public String deliver(Client client, Product product) {
        return "Deliver by land in a box the product " + product.getName() + " to " + client.getName();
    }
}
```

* * *

In the OrderService, when injecting the dependency, let's also specify which implementation to use.

For example, if you want to make the delivery by ship, simply specify it as follows.

```
    @TypeOfTransport(TypeOfTransportEnum.SHIP)
    @Inject
    Transport transport;
```

* * *

Now each implementation has its own qualifier, and the OrderService knows which implementation to use.

![](https://natancode.com/wp-content/uploads/2023/11/image-10.gif)

* * *

Default Annotation

In the service class when injecting the transport dependency, we can not specify which implementation we are going to use if in one of the implementations we use **_@Default_**, so one implementation will be the default if you do not inform which one you want to use in the dependency injection.

Using **_@Default_** in Truck implementation.

```
@TypeOfTransport(TypeOfTransportEnum.TRUCK)
@Default
@ApplicationScoped
public class Truck implements Transport {
    @Override
    public String deliver(Client client, Product product) {
        return "Deliver by land in a box the product " + product.getName() + " to " + client.getName();
    }
}
```

* * *

Injecting dependency without specifying the implementation.

```
@ApplicationScoped
public class OrderService {

    @Inject
    Transport transport;

    public String requestDelivery(Client client, Product product) {
        return this.transport.deliver(client, product);
    }
}
```

* * *

![](https://natancode.com/wp-content/uploads/2023/11/image-12.gif)
