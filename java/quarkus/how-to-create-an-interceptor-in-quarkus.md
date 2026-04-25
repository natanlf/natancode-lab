---
title: "How to create an interceptor in Quarkus?"
slug: how-to-create-an-interceptor-in-quarkus
date: 2025-07-17T17:27:52
categories: ["Java", "Quarkus"]
tags: ["Java", "Quarkus"]
---
When developing REST APIs, it’s common to want to intercept requests and responses to log information, validate data, or even apply business rules and security checks. In **JAX-RS**\-based applications, like those using **Quarkus**, this is possible using **request and response filters**.

In this post, we’ll explore how to implement a **simple interceptor in Quarkus** that allows us to observe and manipulate the HTTP request/response lifecycle transparently.

Hands on

![](https://natancode.com/wp-content/uploads/2024/12/giphy.webp)

The example below demonstrates a filter that intercepts **incoming HTTP requests** and **outgoing responses**. We use the `@Provider` annotation to register the filter in the Quarkus context:

```
import jakarta.ws.rs.container.ContainerRequestContext;
import jakarta.ws.rs.container.ContainerRequestFilter;
import jakarta.ws.rs.container.ContainerResponseContext;
import jakarta.ws.rs.container.ContainerResponseFilter;
import jakarta.ws.rs.ext.Provider;

import java.io.IOException;

@Provider
public class InterceptorFilter implements ContainerRequestFilter, ContainerResponseFilter {

    @Override
    public void filter(ContainerRequestContext containerRequestContext) throws IOException {
        System.out.println(">>> Base URI: " + containerRequestContext.getUriInfo().getBaseUri());
        System.out.println(">>> Request Headers: " + containerRequestContext.getHeaders().values());
    }

    @Override
    public void filter(ContainerRequestContext containerRequestContext, ContainerResponseContext containerResponseContext) throws IOException {
        System.out.println(">>> Response Status: " + containerResponseContext.getStatus());
        if (containerResponseContext.getEntity() != null) {
            System.out.println(">>> Response Body: " + containerResponseContext.getEntity().toString());
        }
    }
}
```

### ✅ Key points:

*   `@Provider`: Registers the class as a JAX-RS component that is automatically discovered.
*   `ContainerRequestFilter`: Interface used to intercept requests **before** they reach the endpoint.
*   `ContainerResponseFilter`: Interface used to intercept responses **before** they are sent to the client.

With this, you can:

*   Log HTTP headers.
*   Add custom response headers.
*   Block access to routes (e.g., authentication/authorization logic).
*   Modify the response body if needed.

Conclusion

Interceptors like the one we implemented here are powerful tools for observing or modifying the request/response lifecycle in RESTful APIs with Quarkus. By using `@Provider` and implementing `ContainerRequestFilter` and `ContainerResponseFilter`, you can create reusable and centralized logic for common request/response handling.

This approach improves the **maintainability**, **observability**, and **security** of your application.

If you want to take this further, you could integrate it with tools like **OpenTelemetry**, **JWT validation**, **tracing**, or store logs for audit purposes.
