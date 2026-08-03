---
title: "What is Java Bean Validation and how to use it?"
slug: what-is-java-bean-validation-and-how-to-use-it
date: 2025-09-08T13:04:42
categories: ["Java", "Quarkus", "Spring"]
tags: ["Java", "Quarkus", "Spring"]
---
**Bean Validation** is a specification for performing validations in Java and can have multiple implementations. To avoid writing repetitive validation code, Java provides Bean Validation (standard specified by JSR 380, also known as Jakarta Bean Validation), which allows applying rules directly to class attributes using annotations. When developing Java applications, it is common to validate input data, whether from a REST API, a web form, or even an internal application process. A widely used implementation is Hibernate Validator, which is used in Quarkus and Spring Boot.

_Bean Validation Constraints_

Spring Boot, for example, uses the package `jakarta.validation.constraints` when importing annotations. Below we can see the list of available annotations.

Constraint

Description

Example

`@AssertFalse`

The value of the field or property must be `false`.

`@AssertFalse boolean isUnsupported;`

`@AssertTrue`

The value of the field or property must be `true`.

`@AssertTrue boolean isActive;`

`@DecimalMax`

The value of the field or property must be a decimal value lower than or equal to the number in the value element.

`@DecimalMax("30.00") BigDecimal discount;`

`@DecimalMin`

The value of the field or property must be a decimal value greater than or equal to the number in the value element.

`@DecimalMin("5.00") BigDecimal discount;`

`@Digits`

The value of the field or property must be a number within a specified range. The `integer` element specifies the maximum integral digits for the number, and the `fraction` element specifies the maximum fractional digits for the number.

`@Digits(integer=6, fraction=2) BigDecimal price;`

`@Email`

The value of the field or property must be a valid email address.

`@Email String emailaddress;`

`@Future`

The value of the field or property must be a date in the future.

`@Future Date eventDate;`

`@FutureOrPresent`

The value of the field or property must be a date or time in present or future.

`@FutureOrPresent Time travelTime;`

`@Max`

The value of the field or property must be an integer value lower than or equal to the number in the value element.

`@Max(10) int quantity;`

`@Min`

The value of the field or property must be an integer value greater than or equal to the number in the value element.

`@Min(5) int quantity;`

`@Negative`

The value of the field or property must be a negative number.

`@Negative int basementFloor;`

`@NegativeOrZero`

The value of the field or property must be negative or zero.

`@NegativeOrZero int debtValue;`

`@NotBlank`

The value of the field or property must contain atleast one non-white space character.

`@NotBlank String message;`

`@NotEmpty`

The value of the field or property must not be empty. The length of the characters or array, and the size of a collection or map are evaluated.

`@NotEmpty String message;`

`@NotNull`

The value of the field or property must not be null.

`@NotNull String username;`

`@Null`

The value of the field or property must be null.

`@Null String unusedString;`

`@Past`

The value of the field or property must be a date in the past.

`@Past Date birthday;`

`@PastOrPresent`

The value of the field or property must be a date or time in the past or present.

`@PastOrPresent Date travelDate;`

`@Pattern`

The value of the field or property must match the regular expression defined in the `regexp` element.

`@Pattern(regexp="\\(\\d{3}\\)\\d{3}-\\d{4}") String phoneNumber;`

`@Positive`

The value of the field or property must be a positive number.

`@Positive BigDecimal area;`

`@PositiveOrZero`

The value of the field or property must be a positive number or zero.

`@PositiveOrZero int totalGoals;`

`@Size`

The size of the field or property is evaluated and must match the specified boundaries. If the field or property is a `String`, the size of the string is evaluated. If the field or property is a `Collection`, the size of the `Collection` is evaluated. If the field or property is a `Map`, the size of the `Map` is evaluated. If the field or property is an array, the size of the array is evaluated. Use one of the optional `max` or `min` elements to specify the boundaries.

`@Size(min=2, max=240) String briefMessage;`

Source: [https://jakarta.ee/learn/docs/jakartaee-tutorial/current/beanvalidation/bean-validation/bean-validation.html](https://jakarta.ee/learn/docs/jakartaee-tutorial/current/beanvalidation/bean-validation/bean-validation.html)

Hands on

The following example is a Spring Boot project with JDK 21 and the following dependencies:

```
	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-validation</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>
```

A domain class called `ProductInput`.

```
import jakarta.validation.constraints.*;
import lombok.Getter;
import lombok.Setter;

import java.math.BigDecimal;

@Getter
@Setter
public class ProductInput {

    @PositiveOrZero
    private Long id;

    @NotBlank
    private String name;

    @NotBlank
    private String description;

    @PositiveOrZero
    private BigDecimal price;

    @NotNull
    private Boolean active;

}
```

An Exception Handler to format errors and the entities used in this formatting.

```
import lombok.Getter;

@Getter
public enum ProblemType {

    INVALID_DATA("/invalid-data", "Invalid Data");

    private String title;
    private String uri;

    ProblemType(String path, String title) {
        this.uri = "http://localhost:8080" + path;
        this.title =  title;
    }

}
```

```
import com.fasterxml.jackson.annotation.JsonInclude;
import lombok.Builder;
import lombok.Getter;

import java.time.OffsetDateTime;
import java.util.List;

@JsonInclude(JsonInclude.Include.NON_NULL)
@Getter
@Builder
public class Problem {

    private Integer status;
    private String type;
    private String title;
    private String detail;
    private String userMessage;
    private OffsetDateTime timestamp;
    private List<Object> errors;

    @Getter
    @Builder
    public static class Object {

        private String name;
        private String userMessage;

    }
}
```

```
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.MessageSource;
import org.springframework.context.i18n.LocaleContextHolder;
import org.springframework.http.HttpHeaders;
import org.springframework.http.HttpStatus;
import org.springframework.http.HttpStatusCode;
import org.springframework.http.ResponseEntity;
import org.springframework.lang.Nullable;
import org.springframework.validation.BindingResult;
import org.springframework.validation.FieldError;
import org.springframework.web.bind.MethodArgumentNotValidException;
import org.springframework.web.bind.annotation.ControllerAdvice;
import org.springframework.web.context.request.WebRequest;
import org.springframework.web.servlet.mvc.method.annotation.ResponseEntityExceptionHandler;

import java.time.OffsetDateTime;
import java.util.List;

@ControllerAdvice
public class ApiExceptionHandler extends ResponseEntityExceptionHandler {

    @Autowired
    private MessageSource messageSource;

    @Override
    @Nullable
    protected ResponseEntity<Object> handleMethodArgumentNotValid(MethodArgumentNotValidException ex, HttpHeaders headers, HttpStatusCode status, WebRequest request) {
        return handleValidationInternal(ex, headers, HttpStatus.resolve(status.value()), request, ex.getBindingResult());
    }

    private ResponseEntity<Object> handleValidationInternal(Exception ex, HttpHeaders headers,
                                                            HttpStatus status, WebRequest request, BindingResult bindingResult) {

        ProblemType problemType = ProblemType.INVALID_DATA;
        String detail = "One or more fields are invalid.";

        List<Problem.Object> errors = bindingResult.getAllErrors().stream()
                .map(objectError -> {
                    String message = messageSource.getMessage(objectError, LocaleContextHolder.getLocale());

                    String name = objectError.getObjectName();

                    if (objectError instanceof FieldError) {
                        name = ((FieldError) objectError).getField();
                    }

                    return Problem.Object.builder()
                            .name(name)
                            .userMessage(message)
                            .build();
                })
                .toList();

        Problem problem = createProblemBuilder(status, problemType, detail)
                .userMessage(detail)
                .errors(errors)
                .build();

        return handleExceptionInternal(ex, problem, headers, status, request);
    }

    private Problem.ProblemBuilder createProblemBuilder(HttpStatus status, ProblemType problemType, String detail) {

        return Problem.builder()
                .status(status.value())
                .type(problemType.getUri())
                .title(problemType.getTitle())
                .timestamp(OffsetDateTime.now())
                .detail(detail);
    }

}
```

A controller containing the endpoint and the class with the annotations.

```
import com.natancode.validation.api.v1.model.input.ProductInput;
import jakarta.validation.Valid;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping(value = "/v1/products")
public class ProductController {

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public ResponseEntity<?> insert(@RequestBody @Valid ProductInput productInput) {
        return ResponseEntity.ok(productInput);
    }

}
```

For validation to occur, the `@Valid` annotation must be used.

And what if we send this JSON in the request?

```
{
    "id": -1,
    "description": "keyboard 123",
    "price": 99.99,
    "active": true
}
```

![](https://natancode.com/wp-content/uploads/2023/11/image-9.gif)

We get the following error.

```
{
    "status": 400,
    "type": "http://localhost:8080/invalid-data",
    "title": "Invalid Data",
    "detail": "One or more fields are invalid.",
    "userMessage": "One or more fields are invalid.",
    "timestamp": "2025-08-26T15:12:31.599317-03:00",
    "errors": [
        {
            "name": "id",
            "userMessage": "must be greater than or equal to 0"
        },
        {
            "name": "name",
            "userMessage": "must not be blank"
        }
    ]
}
```

Conclusion

In this article, we explored how to validate data using the various annotations provided by Hibernate Validator. This makes the process much easier, since it eliminates the need to create multiple different validation types, helping us write less code and saving development time. When working with REST APIs, Bean Validation is widely used to validate input data, as demonstrated in the example. Another interesting point is that this knowledge can be applied both in Spring Boot and Quarkus.
