---
title: "How to Grouping and restricting constraints using Bean Validation?"
slug: how-to-grouping-and-restricting-constraints-using-bean-validation
date: 2025-09-19T19:26:54
categories: ["Java", "Quarkus", "Spring"]
tags: ["Java", "Quarkus", "Spring"]
---
We already know that the Bean Validation specification is very helpful for performing validations. There is also the possibility of validating using groups. Imagine the following scenario: a DTO for products and another one for categories, as in the following example:

```
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.NotNull;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class Category {

    @NotNull
    private Long id;

    @NotBlank
    private String name;

}
```

```
import jakarta.validation.Valid;
import jakarta.validation.constraints.*;
import lombok.Getter;
import lombok.Setter;

import java.math.BigDecimal;

@Getter
@Setter
public class ProductInput {

    private Long id;

    @NotBlank
    private String name;

    @NotBlank
    private String description;

    @PositiveOrZero
    private BigDecimal price;

    @NotNull
    private Boolean active;

    @Valid
    @NotNull
    Category category;

}
```

When trying to register a product by specifying the category id, we encounter an error because the name must also be provided, as we specified it that way in the DTO. However, in this case, the category is already registered in the database, and we only need to provide the id.

Another issue is that when trying to register a category, we need to provide an id to avoid a validation error, but in reality, the correct approach is to only provide the name. Notice that we do not have database integration here; this is just a study with hypothetical scenarios that could occur.

**POST v1/products**

```
{
    "name": "keyboard",
    "description": "keyboard 123",
    "price": 99.99,
    "active": true,
    "category": {
        "id": 1
    }
}
```

So, we receive the following error:

```
{
    "status": 400,
    "type": "http://localhost:8080/invalid-data",
    "title": "Invalid Data",
    "detail": "One or more fields are invalid.",
    "userMessage": "One or more fields are invalid.",
    "timestamp": "2025-08-29T09:57:29.6871416-03:00",
    "errors": [
        {
            "name": "category.name",
            "userMessage": "must not be blank"
        }
    ]
}
```

**POST v1/categories**

```
{
    "name": "electronic"
}
```

So, we receive the following error:

```
{
    "status": 400,
    "type": "http://localhost:8080/invalid-data",
    "title": "Invalid Data",
    "detail": "One or more fields are invalid.",
    "userMessage": "One or more fields are invalid.",
    "timestamp": "2025-08-29T09:38:45.5752121-03:00",
    "errors": [
        {
            "name": "id",
            "userMessage": "must not be null"
        }
    ]
}
```

It is possible to continue using these classes by applying restrictions through groups.

When we don’t specify a group, the default group is used:

```
    @Valid
    @NotNull(groups = Default.class)
    Category category;
```

When nothing is specified, this group called _Default_ is the one that gets applied.

Let’s create the following interface.

```
public interface Groups {

    public interface InsertProduct {}

}
```

This interface, _Groups_, contains other interfaces, such as one for product registration. We will use this group in the entities.

```
import jakarta.validation.Valid;
import jakarta.validation.constraints.*;
import lombok.Getter;
import lombok.Setter;

import java.math.BigDecimal;

@Getter
@Setter
public class ProductInput {

    @PositiveOrZero
    private Long id;

    @NotBlank(groups = Groups.InsertProduct.class)
    private String name;

    @NotBlank(groups = Groups.InsertProduct.class)
    private String description;

    @PositiveOrZero(groups = Groups.InsertProduct.class)
    private BigDecimal price;

    @NotNull(groups = Groups.InsertProduct.class)
    private Boolean active;

    @Valid
    @NotNull(groups = Groups.InsertProduct.class)
    Category category;

}
```

```
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.NotNull;
import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class Category {

    @NotNull(groups = Groups.InsertProduct.class)
    private Long id;

    @NotBlank
    private String name;

}
```

Now, in the Controller, we must specify the group in the @Validated annotation.

```
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping(value = "/v1/products")
public class ProductController {

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public ResponseEntity<?> insert(@RequestBody @Validated(Groups.InsertProduct.class) ProductInput productInput) {
        return ResponseEntity.ok(productInput);
    }

}
```

In the category controller, we continue to use @Valid without specifying a group, because we are using the Default group.

```
import jakarta.validation.Valid;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping(value = "v1/categories")
public class CategoryController {

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public ResponseEntity<?> insert(@RequestBody @Valid Category category) {
        return ResponseEntity.ok(category);
    }

}
```

This way, we can perform validations using groups.

![](https://natancode.com/wp-content/uploads/2024/10/image-1.webp)

Converting constraint groups for cascading validation with @ConvertGroup

To reduce code writing, we can remove the use of groups in ProductInput.

In the interface we will rename it to CategoryId.

```
public interface Groups {

    public interface CategoryId {}

}
```

```
import jakarta.validation.Valid;
import jakarta.validation.constraints.*;
import jakarta.validation.groups.ConvertGroup;
import jakarta.validation.groups.Default;
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

    @Valid
    @ConvertGroup(from = Default.class, to = Groups.CategoryId.class)
    @NotNull
    Category category;

}

```

In ProductController use @Valid.

```
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

Conclusion

In this post we saw how to do validation by groups, so we can use the same class in different places, this brings more flexibility and code reuse.

![](https://natancode.com/wp-content/uploads/2024/11/giphy.webp)
