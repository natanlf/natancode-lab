---
title: "What is Dependency Injection?"
slug: what-is-dependency-injection
date: 2024-04-12T01:07:38
categories: ["Java"]
tags: ["Java"]
---
To understand, I believe the best way is through an example. Let's take a look at a simple delivery system. It's not a complete system; it's something didactic and simple to illustrate.

![https://refactoring.guru/images/patterns/diagrams/factory-method/solution2-en.png](https://natancode.com/wp-content/uploads/2024/04/image.png)

Website Diagram: [https://refactoring.guru/](https://refactoring.guru/)

In the example, we will have a delivery system that can be done by truck or ship, where there is a service that requests the delivery of a product to a customer.

Let's start with the classes Client and Product.

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

Now let's create the Transport interface and its implementations.

```
package com.natancode.transportation;

import com.natancode.model.Client;
import com.natancode.model.Product;

public interface Transport {
    void deliver(Client client, Product product);
}
```

* * *

```
package com.natancode.transportation;

import com.natancode.model.Client;
import com.natancode.model.Product;

public class Truck implements Transport {
    @Override
    public void deliver(Client client, Product product) {
        System.out.println("Deliver by land in a box the product " + product.getName() + " to " + client.getName());
    }
}
```

* * *

```
package com.natancode.transportation;

import com.natancode.model.Client;
import com.natancode.model.Product;

public class Ship implements Transport {
    @Override
    public void deliver(Client client, Product product) {
        System.out.println("Deliver by sea in a container the product " + product.getName() + " to " + client.getName());
    }
}
```

Now we need to create the delivery service. This service is responsible for requesting a delivery of a product to a specific client.

```
package com.natancode.service;

import com.natancode.model.Client;
import com.natancode.model.Product;
import com.natancode.transportation.Transport;

public class OrderService {

    private Transport transport;

    public OrderService() {
        this.transport = new Truck();
    }

    public void requestDelivery(Client client, Product product) {
        this.transport.deliver(client, product);
    }
}
```

In the example, the responsibility of instantiating a transport is in the OrderService class, which brings high coupling and we cannot use polymorphism. How can we improve this? Let's see in the following example.

```
package com.natancode.service;

import com.natancode.model.Client;
import com.natancode.model.Product;
import com.natancode.transportation.Transport;
import com.natancode.transportation.Truck;

public class OrderService {

    private Transport transport;

    public OrderService(Transport transport) {
        this.transport = transport;
    }

    public void requestDelivery(Client client, Product product) {
        this.transport.deliver(client, product);
    }
}
```

* * *

The service class (OrderService) will not instantiate a type of transport; we leave this responsibility to the Main class. This is **Inversion of Control** **(IoC)**, because in the previous example the class OrderService was responsible for instantiating a type of transport. Receiving the transport in the constructor of the OrderService class is **Dependency Injection (DI)** because Transport is a dependency of the service class. This way, we also reduce coupling and can use polymorphism. Let's see how the Main class looks because it will call the service.

```
package com.natancode;

import com.natancode.model.Client;
import com.natancode.model.Product;
import com.natancode.service.OrderService;
import com.natancode.transportation.Transport;
import com.natancode.transportation.Truck;

import java.math.BigDecimal;

public class Main {
    public static void main(String[] args) {

        Client nick = new Client("Nick", "nick@fakedomain.com");
        Client yecenia = new Client("Yecenia", "yecenia@fakedomain.com");

        Product galaxy24 = new Product("Samsung Galaxy S24 Ultra", new BigDecimal("1149.99"));

        Transport transport = new Truck();

        OrderService orderService = new OrderService(transport);
        orderService.requestDelivery(yecenia, galaxy24);
        orderService.requestDelivery(nick, galaxy24);
    }
}
```

Let's run the code and see the result.

![](https://natancode.com/wp-content/uploads/2024/04/image-1.png)

We can change it to "ship" in the main class.

```
Transport transport = new Ship();
```

* * *

![](https://natancode.com/wp-content/uploads/2024/04/image-2.png)

![](https://natancode.com/wp-content/uploads/2023/11/image-6.gif)

We learned about inversion of control and dependency injection, and the benefits of flexibility, as we reduced coupling and were able to use polymorphism. It's a very important concept that many frameworks like Spring, Quarkus, and others utilize.
