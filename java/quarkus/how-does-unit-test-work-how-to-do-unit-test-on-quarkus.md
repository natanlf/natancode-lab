---
title: "How does unit test work ? How to do unit test on Quarkus ?"
slug: how-does-unit-test-work-how-to-do-unit-test-on-quarkus
date: 2023-10-16T22:56:07
categories: ["Java", "Quarkus", "Unit Test"]
tags: ["Java", "JUnit", "Quarkus", "Unit Test"]
---
Testing a system is very important to maintain quality. There are some types of tests.

![](https://natancode.com/wp-content/uploads/2023/10/Testing_Pyramid-4.webp)

Closer to the base of the pyramid, less cost and time are required.  
Let's focus on the base of the pyramid.

Unit test serves to validate the smallest code units. Generally a system accesses some external service such as a database, an API, but unit test does not test this integration. When there is an external dependency on the unit to be tested, we use mock. It is a fake call to the external service.

Unit test doesn't care about integration, it is **independent**.

![](https://natancode.com/wp-content/uploads/2023/10/image.gif)

Got confused ? Don't worry, below I'll show you in practice how to do it. The purpose is to have an understanding of how it works. We have a success / failure scenario and I will show you how to mock it.

Requirements

I'm using JDK 17, Intellij and Quarkus Version 3.4 on the project.

![](https://natancode.com/wp-content/uploads/2023/10/image.png)

I used these dependencies:

```
  <dependencies>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-arc</artifactId>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-resteasy-reactive</artifactId>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-resteasy-reactive-jackson</artifactId>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-rest-client-reactive-jackson</artifactId>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-junit5</artifactId>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-junit5-mockito</artifactId>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>io.rest-assured</groupId>
      <artifactId>rest-assured</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>
```

I created the project on Quarkus Web Site using this link: [https://code.quarkus.io](https://code.quarkus.io)

Practice

I'm not going to talk about TDD this time.  
In the following example, I will show a service and the objective is to test it.

```
package com.natancode.services;

import com.fasterxml.jackson.core.JsonProcessingException;
import com.fasterxml.jackson.core.type.TypeReference;
import com.fasterxml.jackson.databind.ObjectMapper;
import com.natancode.rest.ExtensionsService;
import jakarta.enterprise.context.ApplicationScoped;
import jakarta.inject.Inject;
import org.eclipse.microprofile.rest.client.inject.RestClient;

import java.util.Set;

@ApplicationScoped
public class CalculatorService {

    @Inject
    @RestClient
    ExtensionsService extensionsService;
    
    @Inject
    ObjectMapper objectMapper;

    public Integer sum(Integer n1, Integer n2) throws Exception {
        checkIfValuesAreNull(n1, n2);
        return n1 + n2;
    }

    public Integer decrease(Integer n1, Integer n2) throws Exception {
        checkIfValuesAreNull(n1, n2);
        checkIsForbiddenNumber(n1, n2);
        return n1 - n2;
    }

    private static void checkIfValuesAreNull(Integer n1, Integer n2) throws Exception {
        if(n1 == null || n2 == null) {
            throw new Exception("The values cannot be null");
        }
    }

    private void checkIsForbiddenNumber(Integer n1, Integer n2) throws Exception {
        var forbiddenNumbers = getForbiddenNumbers();
        if(forbiddenNumbers.stream().anyMatch(e -> e.equals(n1) || e.equals(n2) )) {
            throw new Exception("The values cannot be forbidden");
        }
    }

    private Set<Integer> getForbiddenNumbers() throws JsonProcessingException {
        var response = extensionsService.getForbiddenNumbers();
        var forbiddenNumbers = response.getJsonArray("forbiddenNumbers");
        return objectMapper.readValue(forbiddenNumbers.toString(), new TypeReference<Set<Integer>>(){});
    }
}
```

Let's start with the **sum method**. It receives two values, checks if any of them are null, if any of them are null it throws an exception, if none of them are null, they do the sum and return the result.

Sum Method:

```
    public Integer sum(Integer n1, Integer n2) throws Exception {
        checkIfValuesAreNull(n1, n2);
        return n1 + n2;
    }
```

Check if any value is null:

```
    private static void checkIfValuesAreNull(Integer n1, Integer n2) throws Exception {
        if(n1 == null || n2 == null) {
            throw new Exception("The values cannot be null");
        }
    }
```

The tests need to be inside the test folder.

```
@QuarkusTest
public class CalculatorServiceTest {

    @Inject
    CalculatorService calculatorService;


    @Test
    public void shouldSum() throws Exception {
        //scenario
        Integer n1 = 10;
        Integer n2 = 10;

        //action
        var sumResponse = calculatorService.sum(n1, n2);

        //verification
        Assertions.assertEquals(20, sumResponse);
    }
    
}
```

Testing without mock

**quarkus-junit5** is required for testing, as it provides the **@QuarkusTest** annotation that controls the testing framework.

**@Inject** is for access the service's methods.

```
    @Test
    public void shouldSum() throws Exception {
        //scenario
        Integer n1 = 10;
        Integer n2 = 10;

        //action
        var sumResponse = calculatorService.sum(n1, n2);

        //verification
        Assertions.assertEquals(20, sumResponse);
    }
```

We need to inform the name of the method as a scenario to be tested. In this case, the sum must be successful.

**Important structure to understand the test**.  
**scenario**: we prepare what should be tested;  
**action**: this is what we are going to test;  
**verification**: we check whether the result of the action is as expected. In this case, the sum must be returned successfully.

Now let's look at a failure scenario.

```
    @Test
    public void shouldNotSum() {
        //scenario
        Integer n1 = null;
        Integer n2 = 10;

        //verification
        assertThrows(Exception.class, () -> calculatorService.sum(n1,n2));
    }
```

The sum method does not call an external service, so there was no need for a mock. We will see an example with mock below.

Testing with mock

![](https://natancode.com/wp-content/uploads/2023/10/image-2.gif)

As I said before, unit tests are independent, they do not test integration with external services.  
Let's test the decrease method, as it makes a call to an external service.

Decrease Method:

```
    public Integer decrease(Integer n1, Integer n2) throws Exception {
        checkIfValuesAreNull(n1, n2);
        checkIsForbiddenNumber(n1, n2);
        return n1 - n2;
    }
```

checkIsForbiddenNumber is a method that calls an external API.

```
 private Set<Integer> getForbiddenNumbers() throws JsonProcessingException {
        var response = extensionsService.getForbiddenNumbers();
        var forbiddenNumbers = response.getJsonArray("forbiddenNumbers");
        return objectMapper.readValue(forbiddenNumbers.toString(), new TypeReference<Set<Integer>>(){});
    }
```

```
@Path("/forbidden-numbers")
@RegisterRestClient
public interface ExtensionsService {
    @GET
    JsonObject getForbiddenNumbers();
}
```

ExtensionService makes the request to an api that returns a json with the prohibited numbers.

**As unit tests are independent, we cannot make the real call, we have to simulate, make a fake call.**

```
    @Test
    public void shouldDecrease() throws Exception {
        //scenario
        Integer n1 = 10;
        Integer n2 = 10;

        ExtensionsService customMock = new ExtensionsService() {
            @Override
            public JsonObject getForbiddenNumbers() {
                return new JsonObject("{\"forbiddenNumbers\":[5,7,23]}");
            }
        };
        QuarkusMock.installMockForType(customMock, ExtensionsService.class, RestClient.LITERAL);

        //action
        var decreaseResponse = calculatorService.decrease(n1, n2);

        //verification
        Assertions.assertEquals(0, decreaseResponse);
    }
```

In this example I used QuarkusMock for this but we can use mockito.

We need **@InjectMock** to use mockito. Use this import: **_import_ _io.quarkus.test.InjectMock;_** because there is another import that in more recent versions of Quarkus is deprecated.

```
@InjectMock
@RestClient
ExtensionsService extensionsService;
```

```
    @Test
    public void shouldDecrease() throws Exception {
        //scenario
        Integer n1 = 10;
        Integer n2 = 10;

        Mockito.when(extensionsService.getForbiddenNumbers())
                .thenReturn(new JsonObject("{\"forbiddenNumbers\":[5,7,23]}"));

        //action
        var decreaseResponse = calculatorService.decrease(n1, n2);

        //verification
        Assertions.assertEquals(0, decreaseResponse);
    }
```

![](https://natancode.com/wp-content/uploads/2023/10/image-2.png)

Conclusion

After writing the unit test, we can run it quickly, unlike manual testing, thus its saves time and money.  
When we test a specific part of the code, we are able to validate all of its logic, which helps to avoid and find bugs. We also do not depend on external services. We have a lot of benefits from doing unit test.

The code is available here: [https://github.com/natanlf/quarkus-unit-tests.git](https://github.com/natanlf/quarkus-unit-tests.git)

![](https://natancode.com/wp-content/uploads/2023/10/image-1.gif)
