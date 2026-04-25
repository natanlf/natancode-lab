---
title: "Fault Tolerance in Quarkus"
slug: fault-tolerance-in-quarkus
date: 2026-02-06T19:45:39
categories: ["Fault Tolerance", "Java", "Quarkus"]
tags: ["Fault Tolerance", "Java", "Quarkus"]
---
Systems can face issues, including failures in one or more components. For example, external APIs and databases may fail. This can cause a major negative impact on the system, which is why there are ways to handle these failures. Quarkus uses the **SmallRye Fault Tolerance** specification to make the system more resilient. These techniques are widely used in distributed systems (microservices).

Let’s look at some ways to handle these problems.

To make this work, the following dependency is required:

```
<dependency>
    <groupId>io.quarkus</groupId>
    <artifactId>quarkus-smallrye-fault-tolerance</artifactId>
</dependency>
```

Timeout

Imagine a REST API that consumes another REST API. We don’t know how long it will take to receive a response — it could be 100ms, 600ms, 3000ms, 10000ms… Since we can’t predict the response time, we need to define a maximum waiting time. That’s why **Timeout** exists.

```
package com.natancode;

import jakarta.ws.rs.Produces;
import jakarta.ws.rs.core.MediaType;
import org.eclipse.microprofile.faulttolerance.Timeout;
import org.eclipse.microprofile.rest.client.inject.RegisterRestClient;

import jakarta.ws.rs.GET;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.QueryParam;

@RegisterRestClient(baseUri = "https://stage.code.quarkus.io/api")
public interface MyRemoteService {

    @GET
    @Produces(MediaType.APPLICATION_JSON)
    @Path("/extensions")
    @Timeout(value = 3000L)
    String getExtensionsById(@QueryParam("id") String id);

}
```

We need to use the `@Timeout` annotation and specify a value. By default, the value is in milliseconds, but we can define a different time unit, as shown in the example below where seconds are used.

```
@Timeout(value = 3, unit = ChronoUnit.SECONDS)
```

In this example, the client application waits up to 3 seconds for the API response. After that time, a timeout error occurs.

Fallback

To avoid showing an error in case of a timeout, we can define a default API response in case something goes wrong when calling the external REST API.

```
package com.natancode;

import jakarta.ws.rs.Produces;
import jakarta.ws.rs.core.MediaType;
import org.eclipse.microprofile.faulttolerance.Fallback;
import org.eclipse.microprofile.faulttolerance.Timeout;
import org.eclipse.microprofile.rest.client.inject.RegisterRestClient;

import jakarta.ws.rs.GET;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.QueryParam;

@RegisterRestClient(baseUri = "https://stage.code.quarkus.io/api")
public interface MyRemoteService {

    public static final String FALLBACK_MESSAGE = "Fallback Message";

    @GET
    @Produces(MediaType.APPLICATION_JSON)
    @Path("/extensions")
    @Timeout(value = 3000L)
    @Fallback( fallbackMethod = "getMyFallbackMessage")
    String getExtensionsById(@QueryParam("id") String id);

    default String getMyFallbackMessage(String id) {
        return FALLBACK_MESSAGE;
    }

}
```

In this way, if an error occurs, the `@Fallback` annotation calls the specified method to return the fallback response. There are other options besides `fallbackMethod` for this annotation, but we will use this approach in the example.

Circuit Breaker

If a service fails many times, we stop calling it for a period of time.

There is a “circuit” that stays closed while things are working properly. If a problem occurs, based on the configured settings, the circuit opens.

Why is this important?

In a microservices world where we call external services, proper handling is essential. If one service calls another service, which calls yet another service… and one of them goes down, it can affect multiple services. This can create a chain reaction and impact the entire system.

Imagine the following example:

Service A calls Service B and gets a timeout. It tries a second and third time and still gets timeouts. Something must be wrong, so we can assume there is a problem and stop making requests for a while. Later, we try again. This reduces the request queue and allows the system to keep running while handling the failure gracefully.

```
package com.natancode;

import jakarta.ws.rs.Produces;
import jakarta.ws.rs.core.MediaType;
import org.eclipse.microprofile.faulttolerance.CircuitBreaker;
import org.eclipse.microprofile.faulttolerance.Fallback;
import org.eclipse.microprofile.faulttolerance.Timeout;
import org.eclipse.microprofile.rest.client.inject.RegisterRestClient;

import jakarta.ws.rs.GET;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.QueryParam;

@RegisterRestClient(baseUri = "https://stage.code.quarkus.io/api")
public interface MyRemoteService {

    public static final String FALLBACK_MESSAGE = "Fallback Message";

    @GET
    @Produces(MediaType.APPLICATION_JSON)
    @Path("/extensions")
    @Timeout(value = 3000L)
    @Fallback( fallbackMethod = "getMyFallbackMessage")
    @CircuitBreaker(
            requestVolumeThreshold = 4,
            failureRatio = .2,
            delay = 5000L,
            successThreshold = 2
    )
    String getExtensionsById(@QueryParam("id") String id);

    default String getMyFallbackMessage(String id) {
        return FALLBACK_MESSAGE;
    }

}
```

To use this, we need the `@CircuitBreaker` annotation and must configure its properties. Let’s look at some of them:

*   **requestVolumeThreshold**: The number of requests that will be considered when evaluating whether there is a failure.
*   **failureRatio**: The percentage of failures required to open the circuit.
*   **delay**: The time the circuit remains open before checking if the service has recovered. At intervals defined by this delay, the system tests if the service is working again.
*   **successThreshold**: The number of successful requests required to consider the service healthy again.

Conclusion

**Fault Tolerance in Quarkus** allows us to handle failures in a controlled way by using different mechanisms such as **Timeout**, **Fallback**, and others. This way, we can significantly increase system resilience while keeping the system operating in a predictable manner.
