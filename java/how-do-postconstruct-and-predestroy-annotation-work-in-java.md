---
title: "How do @PostConstruct and @PreDestroy annotation work in java?"
slug: how-do-postconstruct-and-predestroy-annotation-work-in-java
date: 2025-03-12T14:43:02
categories: ["Java", "Quarkus", "Spring"]
tags: ["Java", "Quarkus", "Spring"]
---
Methods annotated with **@PostConstruct** and **@PreDestroy** are commonly used in the lifecycle of beans managed by the container, such as in frameworks like Spring or Quarkus. An example of their use is opening and closing database connections.

If you want to know more about Bean management in Spring, don't hesitate to read this [post](https://natancode.com/2024/11/05/how-does-work-spring-ioc-and-dependency-injection/).

@PostConstruct

Indicates that the annotated method should be executed after dependency injection and bean initialization.

```
@PostConstruct
public void init() {
    log.info("Bean is initialized and ready to use");
}
```

@PreDestroy

Indicates that the annotated method should be executed before the bean is destroyed by the container.

```
@PreDestroy
public void destroy() {
    log.info("Bean is about to be destroyed");
}
```

![](https://natancode.com/wp-content/uploads/2024/12/giphy.webp)

**Functioning in the Bean Lifecycle**

```
import jakarta.annotation.PostConstruct;
import jakarta.annotation.PreDestroy;
import lombok.extern.slf4j.Slf4j;
import natan.code.company.domain.BranchRepository;
import natan.code.company.domain.exception.BranchNotFoundException;
import natan.code.company.domain.exception.EntityInUseException;
import natan.code.company.domain.model.Branch;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.dao.DataIntegrityViolationException;
import org.springframework.dao.EmptyResultDataAccessException;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

@Slf4j
@Service
public class BranchService {

    private static final String MSG_BRANCH_IN_USE  =
            "Branch with id %d cannot be removed, because is in use";

    @Autowired
    private BranchRepository branchRepository;

    public BranchService() {
        log.info("BranchService constructor");
    }

    @PostConstruct
    public void init() {
        log.info("Bean is initialized and ready to use");
        log.info("Dependency: {}", branchRepository);
    }

    @PreDestroy
    public void destroy() {
        log.info("Bean is about to be destroyed");
    }

    public List<Branch> findAll() {
        return branchRepository.findAll();
    }

    public Branch findById(Long id) {
        return branchRepository.findById(id).orElseThrow(() -> new BranchNotFoundException(id));
    }

    @Transactional
    public Branch save(Branch branch) {
        return branchRepository.save(branch);
    }

    @Transactional
    public void deleteById(Long id) {
        try {
            branchRepository.deleteById(id);
            branchRepository.flush();
        } catch (EmptyResultDataAccessException e) {
            throw new BranchNotFoundException(id);
        } catch (DataIntegrityViolationException e) {
            throw new EntityInUseException(String.format(MSG_BRANCH_IN_USE, id));
        }
    }

}
```

The **_@PostConstruct_** method is only executed after the bean has been created with its dependencies. Therefore, we first have the following order of logs.

```
BranchService constructor
Bean is initialized and ready to use
Dependency: org.springframework.data.jpa.repository.support.SimpleJpaRepository@55ff64fd
```

When the container needs to destroy the bean, the **_@PreDestroy_** method is called, and only after this method is executed is the bean removed. The log below is displayed, and the bean is destroyed.

```
Bean is about to be destroyed
```

The container manages when these methods are called, reducing manual errors.

Conclusion

The **@PostConstruct** and **@PreDestroy** annotations are powerful tools for managing the **lifecycle of beans** in Spring. They ensure that necessary **initializations and shutdowns** are executed automatically, improving **code organization and reliability**.
