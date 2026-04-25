---
title: "How to paginate using Spring Data JPA?"
slug: how-to-paginate-using-spring-data-jpa
date: 2025-05-29T17:20:55
categories: ["Java", "Spring"]
tags: ["Java", "Spring"]
---
Spring Data JPA enables pagination through the `JpaRepository` interface, which extends `PagingAndSortingRepository` — an interface that provides pagination support.

Let’s go over more details with a practical example. If you need to understand how Spring Data JPA works, there are more articles on the blog explaining that.

Hands on

![](https://natancode.com/wp-content/uploads/2024/12/giphy-2.webp)

You’ll need some of the following dependencies in your `pom.xml`.

```
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>

		<dependency>
			<groupId>com.mysql</groupId>
			<artifactId>mysql-connector-j</artifactId>
		</dependency>
```

In the `application.properties`, you must specify the database connection.

```
spring.datasource.url=jdbc:mysql://localhost/nutri_dev?createDatabaseIfNotExist=true&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=
```

You’ll also need proper **object-relational mapping**.

```
import jakarta.persistence.*;
import lombok.Data;
import lombok.EqualsAndHashCode;

@Entity
@Data
@EqualsAndHashCode(onlyExplicitlyIncluded = true)
public class Food {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @EqualsAndHashCode.Include
    private Long id;

    private String name;

    private Integer quantity;

    @Enumerated(EnumType.STRING)
    private Unity unity;

    private Integer kcal;

    private Double fat;

    private Double carb;

    private Double protein;

    private Double sodium;

    private Double fiber;

    @Enumerated(EnumType.STRING)
    private Origin origin;
}
```

```
public enum Origin {
    ANIMAL,
    VEGETABLE
}
```

```
public enum Unity {
    G,
    ML
}
```

Now, you can create the interface that extends `JpaRepository`.

```
import com.natancode.nutri.domain.model.Food;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface FoodRepository extends JpaRepository<Food, Long> {

}
```

![](https://natancode.com/wp-content/uploads/2025/05/giphy.gif)

Next, we need a **service class**.

```
import com.natancode.nutri.domain.model.Food;
import com.natancode.nutri.domain.model.Origin;
import com.natancode.nutri.domain.repository.FoodRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class FoodService {

    @Autowired
    private FoodRepository foodRepository;


    public Page<Food> search(Pageable pageable) {
        return foodRepository.findAll(pageable);
    }
}
```

And then, a **controller**.

```
import com.natancode.nutri.domain.model.Origin;
import com.natancode.nutri.domain.services.FoodService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Pageable;
import org.springframework.data.web.PageableDefault;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping(path = "/v1/foods")
public class FoodController {

    @Autowired
    private FoodService foodService;


    @GetMapping
    public ResponseEntity<?> search(@PageableDefault Pageable pageable) {
        return ResponseEntity.ok(foodService.search(pageable));
    }
  
}
```

Notice that we need a `Pageable` object, because in the request we can specify the `page` and `size` fields to make pagination work.

The method `foodRepository.findAll(pageable)` takes the pageable object — this method comes from the `PagingAndSortingRepository` interface and returns a `Page` object.

```
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;

@NoRepositoryBean
public interface PagingAndSortingRepository<T, ID> extends Repository<T, ID> {
    Iterable<T> findAll(Sort sort);

    Page<T> findAll(Pageable pageable);
}
```

Here is an example of the response.

```
{
    "content": [
        {
            "id": 1,
            "name": "Chicken Breast",
            "quantity": 100,
            "unity": "G",
            "kcal": 165,
            "fat": 3.6,
            "carb": 0.0,
            "protein": 31.0,
            "sodium": 74.0,
            "fiber": 0.0,
            "origin": "ANIMAL"
        },
        {
            "id": 2,
            "name": "Salmon",
            "quantity": 100,
            "unity": "G",
            "kcal": 208,
            "fat": 13.0,
            "carb": 0.0,
            "protein": 20.0,
            "sodium": 59.0,
            "fiber": 0.0,
            "origin": "ANIMAL"
        }
    ],
    "pageable": {
        "pageNumber": 0,
        "pageSize": 2,
        "sort": {
            "empty": true,
            "sorted": false,
            "unsorted": true
        },
        "offset": 0,
        "paged": true,
        "unpaged": false
    },
    "last": false,
    "totalElements": 10,
    "totalPages": 5,
    "size": 2,
    "number": 0,
    "sort": {
        "empty": true,
        "sorted": false,
        "unsorted": true
    },
    "numberOfElements": 2,
    "first": true,
    "empty": false
}
```

You can pass `page`, `size`, and `sort` as query parameters like this:

*   `page`: the page number;
*   `size`: the size of each page;
*   `sort`: sorts by the specified field.

```
curl --location 'http://localhost:8080/v1/foods?page=0&size=2&sort=name'
```

Conclusion

Paginating data with **Spring Data JPA** is an efficient way to handle large volumes of information, improving application performance and user experience. Using the `Pageable` and `Page` interfaces, the framework offers an elegant and ready-to-use solution, allowing you to implement pagination with just a few lines of code. Additionally, you can customize sorting, filtering, and even adapt the response according to your API's needs. Mastering this feature is essential for applications that require **scalability and structured data access**.
