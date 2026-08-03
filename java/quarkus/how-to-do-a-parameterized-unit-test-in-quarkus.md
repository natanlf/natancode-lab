---
title: "How to do a Parameterized Unit Test in Quarkus?"
slug: how-to-do-a-parameterized-unit-test-in-quarkus
date: 2023-11-09T16:22:40
categories: ["Java", "JUnit", "Quarkus", "Unit Test"]
tags: ["Java", "Quarkus", "Unit Test"]
---
Parameterized unit tests are beneficial for applications as they simplify the process of writing tests, allowing us to **run a single test multiple times with different parameters.**

![](https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExaTl3NXJsaThya3MybWFtcTB4dG1ieTdtc3ZhbmRnZHZsZWRqZ2Z5YyZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/3oKIPlLZEbEbacWqOc/giphy.gif)

Requirements

I’m using JDK 17, Intellij and Quarkus Version 3.4 on the project.

![](https://natancode.com/wp-content/uploads/2023/10/image.png)

JDK17

Practice

I created this class:

```
package com.natancode.dto;

import jakarta.validation.constraints.*;

public class Task {

    @Pattern(regexp = "^[0-9]*$")
    @Size(max = 20)
    @NotNull(message = "Cannot be null")
    @NotBlank(message = "Cannot be empty")
    private String id;

    @Pattern(regexp = "[\\w\\W\\s]*")
    @Size(min=5, max = 20)
    @NotNull(message = "Cannot be null")
    @NotBlank(message = "Cannot be empty")
    private String name;

    private StatusTask status;

    public Task(String id, String name, StatusTask status) {
        this.id = id;
        this.name = name;
        this.status = status;
    }
}
```

These notations represent validations applied to the fields. For instance, in the '**id**' field, we use the '**@Pattern**' notation to verify if the '**id**' value conforms to the given regular expression. In this case, it only allows numeric values.

My current objective is to test the '**id**' field, so let's proceed with a **unit test** to validate it.

Unit Test

We have multiple validations to perform on the '**id**' field. I aim to validate four aspects of the '**id**' value:

*   Whether it is null.
*   Whether it is empty.
*   Whether it is numeric.
*   Whether it adheres to the maximum value allowed.

To streamline this process, we don't need to create four separate tests; we can utilize parameterized tests.

![](https://media.giphy.com/media/a5viI92PAF89q/giphy.gif)

We require the **Hibernate Validator** library to conduct the validation, and I've developed a generic method for this purpose.

```
package com.natancode.util;

import com.natancode.dto.CustomException;
import com.natancode.dto.Error;
import jakarta.validation.ConstraintViolation;
import jakarta.validation.Validator;

import java.util.ArrayList;
import java.util.Set;

public class ValidationFields {

    public static void ValidateFiels(Object object, Validator validator) throws CustomException {
        Set<ConstraintViolation<Object>> violations = validator.validate(object);
        if(!violations.isEmpty()) {
            var errors = new ArrayList< Error >();
            violations.forEach(error -> {
                errors.add( new Error(500, error.getMessage(),
                        object.getClass().getName(), error.getPropertyPath().toString()) );
            });
            throw new CustomException(errors);
        }
    }
}
```

Now we can do the unit test.

```
package com.natancode.dto;

import com.natancode.util.ValidationFields;
import io.quarkus.test.junit.QuarkusTest;
import jakarta.inject.Inject;
import jakarta.validation.Validator;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.NullAndEmptySource;
import org.junit.jupiter.params.provider.ValueSource;

import static org.junit.jupiter.api.Assertions.assertThrows;

@QuarkusTest
public class TaskTest {

    @Inject
    Validator validator;

    @ParameterizedTest
    @NullAndEmptySource
    @ValueSource( strings = {"b", "111111111111111111111"})
    void shouldValidateTaskId(String id) {
        var task = new Task(id, "watch the course", StatusTask.BACKLOG);
        assertThrows(CustomException.class, () -> ValidationFields.ValidateFiels(task, validator) );
    }
}
```

We utilize `@ParameterizedTest` to indicate that it's a parameterized test.

`@NullAndEmptySource` is employed to validate null and empty values.

For `@ValueSource`, we specify the required values. In this case, since the field type is String, we use strings within `@ValueSource`.

It's worth noting that `@ValueSource` can also accommodate other field types such as integers and more.

The test method accepts these parameters and runs the test for each of them.

![ParameterizedTest ](https://natancode.com/wp-content/uploads/2023/11/image-2.png)

Conclusion

We achieved all four validations with a shorter and simpler code, resulting in cleaner code.

The code is available here:

[https://github.com/natanlf/quarkus-unit-tests/tree/ParameterizedTest](https://github.com/natanlf/quarkus-unit-tests/tree/ParameterizedTest)
