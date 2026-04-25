---
title: "How does work Hibernate ORM with Panache in Quarkus?"
slug: how-does-work-hibernate-orm-with-panache-in-quarkus
date: 2026-03-10T14:38:29
categories: ["database", "Java", "Quarkus"]
tags: ["Java", "Quarkus"]
---
Hibernate ORM is used to perform object-relational mapping for Java. This means there is a mapping between the database and Java, which facilitates all interaction with the database by automating many things. As a result, we can develop software with more practicality and agility. Hibernate ORM with Panache focuses on making entities trivial and enjoyable to write in Quarkus.

Initial Configuration

Let’s look at some usage examples. For this, it is necessary to have a Quarkus project with the following dependencies:

```
 <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-hibernate-orm-rest-data-panache</artifactId>
  </dependency>
  <dependency>
      <groupId>io.quarkus</groupId>
      <artifactId>quarkus-jdbc-postgresql</artifactId>
  </dependency>
```

It is also necessary to specify some properties in the `application.properties` file:

```
# configure your datasource
quarkus.datasource.db-kind = postgresql

# drop and create the database at startup (use `update` to only update the schema)
quarkus.hibernate-orm.schema-management.strategy = drop-and-create
```

Note that the database URL, username, and password were not specified. In Quarkus development mode, there is Dev Services. When we specify the use of PostgreSQL but do not specify the remaining properties, Quarkus Dev Services takes care of starting a PostgreSQL container and automatically using the properties from that container. This way, when we are only developing, we do not need to worry about having a PostgreSQL container running.

If you do not want to let Dev Services handle this management, you can specify the properties as shown in the example:

```
# configure your datasource
quarkus.datasource.db-kind = postgresql
quarkus.datasource.username = sarah
quarkus.datasource.password = connor
quarkus.datasource.jdbc.url = jdbc:postgresql://localhost:5432/mydatabase
```

Do not forget to set the values according to the PostgreSQL you are using; this is just an example.

Now that we have the initial configuration ready, we can start mapping the Entity.

Entity

The Entity class will have an enum.

```
public enum Status {
    ACTIVE, INACTIVE
}
```

Now let’s define the Entity class:

```
import io.quarkus.hibernate.orm.panache.PanacheEntity;
import jakarta.persistence.*;

@Entity
public class Customer extends PanacheEntity {

    public String name;
    public String email;
    @Enumerated(EnumType.STRING)
    public Status status;

    @Override
    public String toString() {
        return "Customer{" +
                "name='" + name + '\'' +
                ", email='" + email + '\'' +
                ", status=" + status +
                '}';
    }
}
```

We have the option to leave the attributes as public, so we do not need to worry about getters and setters, for example. This will be handled automatically, but according to your business needs, you should choose what makes sense.

*   `@PanacheEntity`: defines that it is a Panache entity;
*   `@Entity`: defines that it is an entity;
*   `@Enumerated(EnumType.STRING)`: ensures that the database stores the String values instead of the enum numeric values.

Script

Inside the `resources` folder, there is the `import.sql` file, which will be executed when the project runs.

```
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 1', 'customer1@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 2', 'customer2@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 3', 'customer3@example.com', 'INACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 4', 'customer4@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 5', 'customer5@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 6', 'customer6@example.com', 'INACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 7', 'customer7@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 8', 'customer8@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 9', 'customer9@example.com', 'INACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 10', 'customer10@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 11', 'customer11@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 12', 'customer12@example.com', 'INACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 13', 'customer13@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 14', 'customer14@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 15', 'customer15@example.com', 'INACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 16', 'customer16@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 17', 'customer17@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 18', 'customer18@example.com', 'INACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 19', 'customer19@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 20', 'customer20@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 21', 'customer21@example.com', 'INACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 22', 'customer22@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 23', 'customer23@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 24', 'customer24@example.com', 'INACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 25', 'customer25@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 26', 'customer26@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 27', 'customer27@example.com', 'INACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 28', 'customer28@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 29', 'customer29@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 30', 'customer30@example.com', 'INACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 31', 'customer31@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 32', 'customer32@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 33', 'customer33@example.com', 'INACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 34', 'customer34@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 35', 'customer35@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 36', 'customer36@example.com', 'INACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 37', 'customer37@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 38', 'customer38@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 39', 'customer39@example.com', 'INACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 40', 'customer40@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 41', 'customer41@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 42', 'customer42@example.com', 'INACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 43', 'customer43@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 44', 'customer44@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 45', 'customer45@example.com', 'INACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 46', 'customer46@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 47', 'customer47@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 48', 'customer48@example.com', 'INACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 49', 'customer49@example.com', 'ACTIVE');
INSERT INTO Customer (id, name, email, status) VALUES (nextval('customer_seq'), 'Customer 50', 'customer50@example.com', 'ACTIVE');
```

Crud

Let’s look at some useful methods. For this purpose, an interface and its implementation were created to keep the examples more organized.

```
public interface CustomerService {

    List<Customer> getAllCustomers();
    void create(Customer customer);
    Customer findById(Long id);
    Customer update(Long id, Customer customer);
    void deleteById(Long id);
}
```

Below is the interface implementation.

```
@ApplicationScoped
public class CustomerServiceImpl implements CustomerService {
    @Override
    @Transactional
    public void create(Customer customer) {
        customer.persist();
    }
    
    @Override
    public Customer findById(Long id) {
        return Customer.findById(id);
    }
    
    @Override
    public List<Customer> getAllCustomers() {
        return Customer.listAll();
    }
    
    @Override
    @Transactional
    public Customer update(Long id, Customer customer) {
        Customer customerFound = Customer.findById(id);

        if (customerFound == null) {
            throw new RuntimeException("Customer not found");
        }

        customerFound.name = customer.name;
        customerFound.email = customer.email;
        customerFound.status = customer.status;

        return customerFound;
    }

    @Override
    @Transactional
    public void deleteById(Long id) {
        Customer.deleteById(id);
    }
}
```

Named queries

To use this feature, it is necessary to modify the entity class.

```
package com.natancode.domain.entity;

import com.natancode.domain.Status;
import io.quarkus.hibernate.orm.panache.PanacheEntity;
import jakarta.persistence.*;

@Entity
@NamedQueries({
        @NamedQuery(name = "Customer.getByEmail", query = "FROM Customer WHERE email = ?1")
})
public class Customer extends PanacheEntity {

    public String name;
    public String email;
    @Enumerated(EnumType.STRING)
    public Status status;

    public static Customer findByEmail(String email){
        return find("#Customer.getByEmail", email).firstResult();
    }

    @Override
    public String toString() {
        return "Customer{" +
                "name='" + name + '\'' +
                ", email='" + email + '\'' +
                ", status=" + status +
                '}';
    }
}
```

The `NamedQueries` annotation can contain multiple queries, and each query needs to be specified in the `@NamedQuery` annotation as shown in the example. We also have the `findByEmail` method, which is linked to the query by the name `#Customer.getByEmail`.

Just add it to the interface and the implementation.

```
Customer findByEmail(String email);
```

```
@Override
public Customer findByEmail(String email) {
    return Customer.findByEmail(email);
}
```

Pagination

Pagination is also simplified. To maintain good organization, some classes were created.

The class below was created to maintain the navigation links related to pagination.

```
package com.natancode.domain;

public class Links {

    private String next;
    private String prev;
    private String self;


    public String getNext() {
        return next;
    }

    public void setNext(String next) {
        this.next = next;
    }

    public String getPrev() {
        return prev;
    }

    public void setPrev(String prev) {
        this.prev = prev;
    }

    public String getSelf() {
        return self;
    }

    public void setSelf(String self) {
        this.self = self;
    }
}
```

```
package com.natancode.domain;

import java.util.List;

public class ResponsePaginated {

    private List<?> data;
    private Links links;

    public ResponsePaginated(List<?> data, Links links) {
        this.data = data;
        this.links = links;
    }

    public List<?> getData() {
        return data;
    }

    public void setData(List<?> data) {
        this.data = data;
    }

    public Links getLinks() {
        return links;
    }

    public void setLinks(Links links) {
        this.links = links;
    }
}
```

Now we can define the contract in the interface.

```
ResponsePaginated getActivatedPagedCustomers(int page, int size);
```

The implementation is as follows.

```
@Override
public ResponsePaginated getActivatedPagedCustomers(int page, int size) {
    PanacheQuery<Customer> activeCustomers = Customer.find("status", Status.ACTIVE);
    activeCustomers.page(page, size);

    String urlBase = "http://localhost:8080/customers/page?page=";
    Links links = new Links();

    var currentPage = activeCustomers.list();

    var hasNextPage = activeCustomers.hasNextPage();
    var hasPrevPage = activeCustomers.hasPreviousPage();

    if (hasNextPage) {
        links.setNext(urlBase + (page + 1));
    }

    if (hasPrevPage) {
        links.setPrev(urlBase + (page - 1));
    }

    links.setSelf(urlBase + page);

    return new ResponsePaginated(currentPage, links);
}
```

Notice the simplicity: after fetching the records with ACTIVE status, you just call the `page` method, providing the page number and its size. To verify whether there is a next or previous page, it is also simple using the `hasNextPage()` and `hasPreviousPage()` methods. It is also possible to know the total number of records using the `count()` method.

Endpoints

To use the endpoints, the `@ApplicationPath` annotation was used to define part of the route by default, so the route always contains `api/v1`. It is not mandatory to do it this way; it is just another organizational option.

```
package com.natancode;

import jakarta.ws.rs.ApplicationPath;
import jakarta.ws.rs.core.Application;

@ApplicationPath("api/v1")
public class ApplicationDefault extends Application {
}
```

Finally, let’s look at the endpoints below.

```
package com.natancode.resources;

import com.natancode.domain.Status;
import com.natancode.domain.entity.Customer;
import com.natancode.services.CustomerService;
import jakarta.inject.Inject;
import jakarta.ws.rs.*;
import jakarta.ws.rs.core.MediaType;
import jakarta.ws.rs.core.Response;

import java.net.URI;

@Path("/customers")
@Produces(MediaType.APPLICATION_JSON)
@Consumes(MediaType.APPLICATION_JSON)
public class CustomerResource {

    @Inject
    CustomerService customerService;

    @GET
    public Response getAllCustomers(){
        return Response
                .ok(customerService.getAllCustomers())
                .build();
    }

    @POST
    public Response create(Customer customer){
        customerService.create(customer);
        return Response.created(URI.create("/customers/" + customer.id)).build();
    }

    @GET
    @Path("/{id}")
    public Response findById(@PathParam("id") Long id){
        var customer = customerService.findById(id);
        return Response.ok(customer).build();
    }

    @PUT
    @Path("/{id}")
    public Response update(@PathParam("id") Long id, Customer customer) {
        Customer updatedCustomer = customerService.update(id, customer);
        return Response.ok(updatedCustomer).build();
    }

    @DELETE
    @Path("/{id}")
    public Response deleteById(@PathParam("id") Long id) {
        customerService.deleteById(id);
        return Response.noContent().build();
    }

    @GET
    @Path("/page")
    public Response getActivatedPagedCustomers(@DefaultValue("0") @QueryParam("page") int page, @DefaultValue("5") @QueryParam("size") int size){
        var response = customerService.getActivatedPagedCustomers(page, size);
        return Response
                .ok(response)
                .build();
    }

    @GET
    @Path("/email")
    public Response findByEmail(@QueryParam("email") String email){
        return Response
                .ok(customerService.findByEmail(email))
                .build();
    }
}
```

Conclusion

These were some examples of how to use Panache, but there is more to learn. Panache brings simplicity together with good performance, keeps the code simple and elegant, and is an excellent option for using ORM in Quarkus. We can also leverage the knowledge acquired from Hibernate.
