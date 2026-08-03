---
title: "What are Specifications in Spring Data Jpa?"
slug: what-are-specifications-in-spring-data-jpa
date: 2024-10-10T00:37:27
categories: ["Java", "Spring"]
tags: ["Java", "Spring"]
---
**_Specifications_** allows the creation of **_dynamic queries programmatically_** using the provided filters in a flexible way.

![](https://natancode.com/wp-content/uploads/2024/10/image.webp)

"JPA 2 introduces a criteria API that you can use to build _**queries programmatically**_. By writing a `criteria`, you define the where clause of a query for a domain class. Taking another step back, these criteria can be regarded as a predicate over the entity that is described by the JPA criteria API constraints."

![](https://natancode.com/wp-content/uploads/2024/10/Specification-Interface-1024x537.png)

Specification Interface

To be able to use Specification, it is necessary to make use of an interface called "**_JpaSpecificationExecutor_**".

![](https://natancode.com/wp-content/uploads/2024/10/JpaSpecificationExecutor-1024x517.png)

This way, we can perform the searches. For it to work, we need to use it in the Repository. I'll use an example.

"**_Prescription_**" is the Entity.

```
import jakarta.persistence.*;
import lombok.Data;
import lombok.EqualsAndHashCode;
import org.hibernate.annotations.CreationTimestamp;
import org.hibernate.annotations.UpdateTimestamp;

import java.time.OffsetDateTime;
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

@Entity
@Data
@EqualsAndHashCode(onlyExplicitlyIncluded = true)
public class Prescription {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @EqualsAndHashCode.Include
    private Long id;

    private String code;

    @Column(name = "creation_date")
    @CreationTimestamp
    private OffsetDateTime creationDate;

    private String addition;

    @Column(name = "due_date")
    @UpdateTimestamp
    private OffsetDateTime dueDate;

    @Column(name="status", nullable=false)
    @Enumerated(EnumType.STRING)
    private StatusPrescription status = StatusPrescription.CURRENT;

    @ManyToOne
    @JoinColumn(name = "doctor_id", nullable = false)
    private Doctor doctor;

    @OneToMany(mappedBy = "prescription", cascade = CascadeType.ALL)
    @Column(nullable = false)
    private List<Measure> measures = new ArrayList<>();

    @ManyToOne
    @JoinColumn(name = "user_id", nullable = false)
    private User client;

    @ManyToOne
    @JoinColumn(name = "order_id", nullable = false)
    private Order order;

    @PrePersist
    private void generateCode() {
        setCode(UUID.randomUUID().toString());
    }

}
```

I have a Repository that needs to make use of the "_**JpaSpecificationExecutor**_" interface.

```
import com.natancode.otica.domain.model.Prescription;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.JpaSpecificationExecutor;
import org.springframework.stereotype.Repository;

import java.util.Optional;

@Repository
public interface PrescriptionRepository extends JpaRepository<Prescription, Long>,
        JpaSpecificationExecutor<Prescription> {

    Optional<Prescription> findByCode(String code);

}
```

This way, when using the "_**PrescriptionRepository**_", we can use the methods provided by the "**_JpaSpecificationExecutor_**" interface.

We need to create a filter and Spec class.

```
import com.natancode.otica.domain.model.StatusPrescription;
import lombok.Getter;
import lombok.Setter;

import java.time.OffsetDateTime;

@Getter
@Setter
public class PrescriptionFilter {

    private Long clientId;
    private Long orderId;
    private Long doctorId;
    private StatusPrescription status;
    private OffsetDateTime creationDate;
    private OffsetDateTime dueDate;

}
```

* * *

```
import com.natancode.otica.domain.filter.PrescriptionFilter;
import com.natancode.otica.domain.model.Prescription;
import jakarta.persistence.criteria.Predicate;
import org.springframework.data.jpa.domain.Specification;

import java.util.ArrayList;

public class PrescriptionSpecs {

    public static Specification<Prescription> usingFilter(PrescriptionFilter filter) {
        return (root, query, criteriaBuilder) -> {
          if (Prescription.class.equals(query.getResultType())) {
              root.fetch("order").fetch("client");
              root.fetch("doctor");
          }

          var predicates = new ArrayList<Predicate>();

          if (filter.getClientId() != null) {
              predicates.add(criteriaBuilder.equal(root.get("client").get("id"), filter.getClientId()));
          }

          if (filter.getOrderId() != null) {
              predicates.add(criteriaBuilder.equal(root.get("order").get("id"), filter.getOrderId()));
          }

          if (filter.getDoctorId() != null) {
              predicates.add(criteriaBuilder.equal(root.get("doctor").get("id"), filter.getDoctorId()));
          }

          if (filter.getStatus() != null) {
              predicates.add(criteriaBuilder.equal(root.get("status"), filter.getStatus()));
          }

          if (filter.getDueDate() != null) {
              predicates.add(criteriaBuilder.equal(root.get("dueDate"), filter.getDueDate()));
          }

          if (filter.getCreationDate() != null) {
              predicates.add(criteriaBuilder.equal(root.get("creationDate"), filter.getCreationDate()));
          }

          return criteriaBuilder.and(predicates.toArray(new Predicate[0]));
        };
    }

}
```

Inside the if block, we have "_**root.fetch**_", so when performing the query, it's not necessary to run separate queries. We make a single query with joins.

We use a list of predicates to add what we want to filter, according to what was provided in the "**_PrescriptionFilter_**". Then, we add the predicates to the "_**criteriaBuilder**_".

Now let's take a look at the usage of the "_**PrescriptionSpecs**_" class.

In the search endpoint, we have the filter.

```
@GetMapping(produces = MediaType.APPLICATION_JSON_VALUE)
    public Page<PrescriptionSummaryModel> search(PrescriptionFilter filter,
                                                 @PageableDefault(size = 10) Pageable pageable) {

        pageable  = translatePageable(pageable);
        Page<Prescription> prescriptionsPage = prescriptionRepository
                .findAll(PrescriptionSpecs.usingFilter(filter), pageable);

        List<PrescriptionSummaryModel> prescriptionsSummaryModel = prescriptionSummaryModelAssembler
                .toCollectionModel(prescriptionsPage.getContent());

        return new PageImpl<>(prescriptionsSummaryModel, pageable, prescriptionsPage.getTotalElements());
    }
```

In this example, we are using the method with paginated search, but as mentioned earlier, the "**_JpaSpecificationExecutor_**" interface has several methods; it doesn't have to be paginated—use whichever you prefer. We provide the result of the "_**usingFilter**_" method from the "_**PrescriptionSpecs**_" class to the "**_findAll_**" method of the "_**JpaSpecificationExecutor**_" interface. This way, we get the filtered result according to the filters specified in the request of the endpoint **dynamically**.

I have some records in the database; let's make a request without filtering.

![](https://natancode.com/wp-content/uploads/2024/10/Specification-postman-request.png)

Now let's filter by doctor.

![](https://natancode.com/wp-content/uploads/2024/10/Specification-postman-request-filter-1.png)

Let's filter by status as well.

![](https://natancode.com/wp-content/uploads/2024/10/Specification-postman-request-filter-2.png)

![](https://natancode.com/wp-content/uploads/2024/10/image-1.webp)

This way, we can perform **advanced searches** **dynamically** by specifying only what we want to filter, as seen in the previous example, which provides **great flexibility** for the API.
