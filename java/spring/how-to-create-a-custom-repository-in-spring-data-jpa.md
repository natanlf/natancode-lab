---
title: "How to create a custom repository in Spring Data JPA?"
slug: how-to-create-a-custom-repository-in-spring-data-jpa
date: 2025-04-28T15:14:46
categories: ["Java", "Spring"]
tags: ["Java", "Spring"]
---
Spring Data JPA is great for abstracting away boilerplate code when accessing the database. It allows us to create query methods by simply defining method signatures. But sometimes, we need more complex or dynamic queries — and that’s where **custom repositories** come into play.

In this post, I’ll show how to create a custom repository for a `Food` entity and explain why and when you should consider this approach.

This post is an extension of a previous post: [https://natancode.com/2025/04/16/what-is-spring-data-jpa/](https://natancode.com/2025/04/16/what-is-spring-data-jpa/)

When Should You Use a Custom Repository?

You should consider a custom repository when:

*   You need to write a query using multiple optional filters.
*   You want to reuse custom query logic across your application.
*   You need finer control over how queries are executed with `EntityManager`.

Hands on

The following example is based on an existing Repository.

```
@Repository
public interface FoodRepository extends JpaRepository<Food, Long> {

    Optional<Food> findFirstByNameContaining(String name);
    List<Food> findByNameContaining(String name);

    @Query("from Food where name like %:name%")
    List<Food> search(String name);

}
```

Now that we have the main repository defined, we can create the customized one.

![](https://natancode.com/wp-content/uploads/2025/04/giphy.gif)

```
import com.natancode.nutri.domain.model.Food;
import com.natancode.nutri.domain.model.Origin;

import java.util.List;

public interface FoodRepositoryQueries {

    List<Food> find(String name, Origin origin);
    List<Food> findWithoutFiber();

}
```

A customized interface that will be used in the custom repository implementation.

```
import com.natancode.nutri.domain.model.Food;
import com.natancode.nutri.domain.model.Origin;
import jakarta.persistence.EntityManager;
import jakarta.persistence.PersistenceContext;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public class FoodRepositoryImpl implements FoodRepositoryQueries {

    @PersistenceContext
    private EntityManager manager;

    @Override
    public List<Food> find(String name, Origin origin) {
        StringBuilder stringBuilder = new StringBuilder();
        stringBuilder.append("from Food where name like :name ");
        stringBuilder.append("and origin = :origin ");

        return manager.createQuery(stringBuilder.toString(), Food.class)
                .setParameter("name", "%" + name + "%")
                .setParameter("origin", origin)
                .getResultList();
    }

    @Override
    public List<Food> findWithoutFiber() {
        StringBuilder stringBuilder = new StringBuilder();
        stringBuilder.append("from Food where fiber = 0");

        return manager.createQuery(stringBuilder.toString(), Food.class)
                .getResultList();
    }
}
```

`@PersistenceContext`: injects the `EntityManager`, which allows low-level query execution.

`find`: dynamically builds a JPQL query to search foods by partial name and origin.

`findWithoutFiber`: returns foods where the fiber value is exactly zero.

```
import com.natancode.nutri.domain.model.Food;
import com.natancode.nutri.domain.model.Origin;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Query;
import org.springframework.stereotype.Repository;

import java.util.List;
import java.util.Optional;

@Repository
public interface FoodRepository extends JpaRepository<Food, Long>, FoodRepositoryQueries {

    Optional<Food> findFirstByNameContaining(String name);
    List<Food> findByNameContaining(String name);

    @Query("from Food where name like %:name%")
    List<Food> search(String name);

    List<Food> findTop3ByOrigin(Origin name);
}
```

FoodRepository extends the custom interface, so when using FoodRepository, we can call the custom repository methods.

Conclusion

Custom repositories give you the flexibility to write more advanced queries while still taking advantage of Spring Data JPA. They are ideal when method naming conventions are not enough to express what you need.

If you're already using Spring Data JPA and find yourself writing dynamic or reusable queries, custom repositories are definitely worth exploring.
