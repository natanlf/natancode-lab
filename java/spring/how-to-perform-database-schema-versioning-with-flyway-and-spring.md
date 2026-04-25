---
title: "How to perform database schema versioning with Flyway and Spring?"
slug: how-to-perform-database-schema-versioning-with-flyway-and-spring
date: 2024-09-19T22:30:10
categories: ["database", "Java", "Spring"]
tags: ["Flyway", "Java"]
---
Flyway is a tool used for database migration control. Migrations are executed automatically, and we also have a history of database modifications.

Flyway supports many databases, but in this example, we will use MySQL.

Requirements

*   JDK 17;
*   Spring 3;
*   SpringDataJpa;
*   Mysql;
*   Flyway;

Project

```
<dependencies>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>

		<dependency>
			<groupId>com.mysql</groupId>
			<artifactId>mysql-connector-j</artifactId>
		</dependency>

		<dependency>
			<groupId>org.flywaydb</groupId>
			<artifactId>flyway-core</artifactId>
		</dependency>
		<dependency>
			<groupId>org.flywaydb</groupId>
			<artifactId>flyway-mysql</artifactId>
		</dependency>

		<dependency>
			<groupId>org.projectlombok</groupId>
			<artifactId>lombok</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>
```

With this site, you can create your Spring project: [https://start.spring.io](https://start.spring.io)

To establish a connection with the database, we need to specify some variables in the properties file:

```
spring.datasource.url=jdbc:mysql://localhost/company?createDatabaseIfNotExist=true&serverTimezone=UTC
spring.datasource.username=root
spring.datasource.password=
```

I am using MySQL through a Docker container, with the root user and no password.

I created two entities that will represent two tables in the database, and the mapping was done using annotations.

```
package natan.code.company.domain.model;

import jakarta.persistence.*;
import lombok.Data;
import lombok.EqualsAndHashCode;

@Data
@Entity
@EqualsAndHashCode(onlyExplicitlyIncluded = true)
public class Branch {

    @EqualsAndHashCode.Include
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

}
```

* * *

```
package natan.code.company.domain.model;

import jakarta.persistence.*;
import lombok.Data;
import lombok.EqualsAndHashCode;

import java.time.OffsetDateTime;

@Data
@Entity
@EqualsAndHashCode(onlyExplicitlyIncluded = true)
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @EqualsAndHashCode.Include
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(nullable = false)
    private String email;

    @Column(nullable = false, columnDefinition = "datetime", name = "birth_datetime")
    private OffsetDateTime dateOfBirth;

    @ManyToOne
    @JoinColumn(name = "branch_id", nullable = false)
    private Branch branch;

}
```

* * *

Let's observe what happens when the project starts.

![](https://natancode.com/wp-content/uploads/2024/09/Starting-the-Spring-Project-1024x359.png)

* * *

There are no migrations, and a table called "flyway\_schema\_history" was created in our database. What does it do?

![](https://natancode.com/wp-content/uploads/2023/11/image-1.gif)

* * *

Its purpose is to store the version history. When a migration is executed, it is stored in this table. This is how Flyway keeps track of the history to manage it.

And where is the migration?

Inside the `resources` folder, we need to create two more folders: `db/migration`.

Inside the `migration` folder, we can create the migration file. We need to follow this structure: "V001\_\_create\_initial\_tables.sql".

![](https://natancode.com/wp-content/uploads/2024/09/resources.png)

* * *

We can also have version variations, such as 1.1, 1.2, 2.1.

We will use this pattern: 001, 002, 003, and so on.

In the migration, we will specify the following script:

```
CREATE TABLE `branch` (
  `id` bigint NOT NULL AUTO_INCREMENT,
  `name` varchar(255) NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

CREATE TABLE `employee` (
  `id` bigint NOT NULL AUTO_INCREMENT,
  `name` varchar(255) NOT NULL,
  `email` varchar(255) NOT NULL,
  `birth_datetime` datetime(6) DEFAULT NULL,
  `branch_id` bigint NOT NULL,
  PRIMARY KEY (`id`),
  KEY `FK1ppr8greedyrey8nchpr0v4dn` (`branch_id`),
  CONSTRAINT `FK1ppr8greedyrey8nchpr0v4dn` FOREIGN KEY (`branch_id`) REFERENCES `branch` (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```

* * *

When running the application, we get the following result:

![](https://natancode.com/wp-content/uploads/2024/09/first-migration-1024x275.png)

* * *

The migration was executed, and we can see in the _**"flyway\_schema\_history"**_ table the history of migrations.

![](https://natancode.com/wp-content/uploads/2024/09/flyway_schema_history.png)

* * *

![](https://media3.giphy.com/media/v1.Y2lkPTc5MGI3NjExZWN6YnZuNWM0eW9jc2dldnVtZzhqYWRmbHR5cDN5cWNyeDZ2bmV2bSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/Ph05xuYgrX5te/giphy.webp)

* * *

Now we need to add the `cellphone` field to the **_"employee"_** table.

I created the migration _**"V002\_\_adding\_cellphone\_in\_employee.sql"**_ with the following script.

```
alter table `employee` add cellphone varchar(15);
```

* * *

Upon execution, we get the following result:

![](https://natancode.com/wp-content/uploads/2024/09/second-migration-1024x420.png)

* * *

![](https://natancode.com/wp-content/uploads/2024/09/flyway_schema_history-2.png)

* * *

![](https://natancode.com/wp-content/uploads/2023/11/image-11.gif)
