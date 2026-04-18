---
title: "How to create a Custom Annotation in Quarkus Framework?"
slug: how-to-create-a-custom-annotation-in-quarkus-framework
date: 2024-03-29T21:28:37
categories: ["Java", "Quarkus"]
tags: ["Java", "Quarkus"]
---
It's important to perform validations in the system. There are dependencies available for this with a lot of ready-made solutions. For example, in the case of Quarkus, we can use [Hibernate Validator](https://quarkus.io/guides/validation), which provides us with some annotations to solve common problems. However, if it's necessary to perform a validation for which there is no annotation to solve the problem, we can also create our own validation. Next, let's see an example of how to create a custom annotation.

Creating the Project

The project is an API created using the Quarkus Framework. A very simple way to create it is through the Quarkus website: [https://code.quarkus.io/](https://code.quarkus.io/).

It's also possible to create it via the terminal by following the necessary installations: [https://quarkus.io/get-started/](https://quarkus.io/get-started/)

All dependencies used

```
    <dependencies>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-arc</artifactId>
        </dependency>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-rest</artifactId>
        </dependency>
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.32</version>
        </dependency>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-hibernate-validator</artifactId>
        </dependency>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-rest-jackson</artifactId>
        </dependency>
        <dependency>
            <groupId>io.quarkus</groupId>
            <artifactId>quarkus-junit5</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>io.rest-assured</groupId>
            <artifactId>rest-assured</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
```

Code of the Project

The validation we are going to perform is quite simple. I have created a class `User` with the fields `name` and `email`. We have a collection of names that are prohibited. If a person enters a name contained in the collection, they will receive an error.

```
package com.natancode.domain;

import lombok.*;

import java.util.Set;

@AllArgsConstructor
@NoArgsConstructor
@Getter
@Setter
@ToString
public class User {

    public static final Set<String> FORBIDDEN_NAMES = Set.of("Walter White");

    private String name;
    private String email;
}

```

This is the endpoint that receives the user:

```
package com.natancode.api.resources;

import com.natancode.domain.User;
import jakarta.ws.rs.Consumes;
import jakarta.ws.rs.POST;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.core.MediaType;
import jakarta.ws.rs.core.Response;

@Path("users")
public class UserResource {

    @POST
    @Consumes({MediaType.APPLICATION_JSON})
    public Response createUser(User user){
        return Response.noContent().build();
    }
}
```

At the moment, we are not validating anything. Next, we will create the validator so that we can use it in the endpoint.

Custom Annotation

We need to create the interface and its implementation.

```
package com.natancode.validators;

import com.natancode.validators.impl.UserValidatorImpl;
import jakarta.validation.Constraint;
import jakarta.validation.Payload;

import java.lang.annotation.*;

@Documented
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.PARAMETER})
@Constraint(validatedBy = UserValidatorImpl.class)
public @interface UserValidator {
    String message() default "Sorry, this name is forbidden";
    Class<?>[] groups() default {};
    Class<? extends Payload>[] payload() default {};
}
```

*   **@Documented**: Indicates that annotations with a type are to be documented by javadoc and similar tools by default.
*   **@Retention(RetentionPolicy.RUNTIME)**: Specifies that this annotation should be retained at runtime.
*   **@Target({ElementType.PARAMETER})**: Indicates that this annotation can only be applied to parameters.
*   **@Constraint(validatedBy = UserValidatorImpl.class)**: It is the implementation of the annotation that will perform the validation.
*   **String message() default "Sorry, this name is forbidden"**: It is a default message that will be displayed if there is an invalid parameter.
*   **Class\[\] groups() default {}**: Specifies the validation groups to which this constraint belongs.
*   **Class\[\] payload() default {}**: Specifies the payload type associated with the constraint.

Let's see the implementation:

```
package com.natancode.validators.impl;

import com.natancode.domain.User;
import com.natancode.validators.UserValidator;
import jakarta.validation.ConstraintValidator;
import jakarta.validation.ConstraintValidatorContext;

import static com.natancode.domain.User.FORBIDDEN_NAMES;

public class UserValidatorImpl implements ConstraintValidator<UserValidator, User> {
    @Override
    public void initialize(UserValidator constraintAnnotation) {
        ConstraintValidator.super.initialize(constraintAnnotation);
    }

    @Override
    public boolean isValid(User user, ConstraintValidatorContext constraintValidatorContext) {
        return !FORBIDDEN_NAMES.contains(user.getName());
    }
}
```

When implementing _**ConstraintValidator**_, we need to implement its methods. In the isValid method, we put the validation logic. In this case, it checks if the provided name is not contained in the collection. If it's not, then it's considered valid; otherwise, it will throw an error.

For the validation to occur, we need to use the annotation in the Endpoint.

```
package com.natancode.api.resources;

import com.natancode.domain.User;
import com.natancode.validators.UserValidator;
import jakarta.ws.rs.Consumes;
import jakarta.ws.rs.POST;
import jakarta.ws.rs.Path;
import jakarta.ws.rs.core.MediaType;
import jakarta.ws.rs.core.Response;

@Path("users")
public class UserResource {

    @POST
    @Consumes({MediaType.APPLICATION_JSON})
    public Response createUser(@UserValidator User user){
        return Response.noContent().build();
    }
}
```

We use the _**@UserValidator**_ annotation that we created.

Let's see it working. To run the API, we need to enter the following command:

```
mvn quarkus:dev
```

I used postman to make the request.

![](https://natancode.com/wp-content/uploads/2024/03/image-300x186.png)

![](https://natancode.com/wp-content/uploads/2024/03/image-1-300x129.png)

That was a simple example. However, before creating a custom annotation, check if the validation you need already exists in the validation dependency you're using. This helps avoid unnecessary work.
