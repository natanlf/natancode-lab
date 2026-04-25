---
title: "How to Manage Profiles in Spring Boot (with Docker)?"
slug: how-to-manage-profiles-in-spring-boot-with-docker
date: 2025-07-31T13:17:39
categories: ["Docker", "Java", "Spring"]
tags: ["Docker"]
---
Spring Boot allows you to use **profiles** to separate configurations by environment, such as development (`dev`), staging (`staging`), and production (`prod`). This makes it easier to manage settings like database URLs, feature flags, and other environment-specific behaviors.

In this post, you'll learn how to:

*   Create profiles in Spring Boot
*   Use `docker-compose.yml` to define the active profile

📁 File Structure

```
.
├── Dockerfile
├── docker-compose.yml
├── src
│   └── main
│       └── resources
│           ├── application.properties
│           ├── application-dev.properties
│           └── application-prod.properties
└── pom.xml
```

🔧 Configuration Files

🔧 `application.properties` (common)

```
spring.application.name=profiles
```

🔧 `application-dev.properties`

```
spring.datasource.url=jdbc:h2:mem:devdb
spring.datasource.username=dev
spring.datasource.password=dev123
```

🔧 `application-prod.properties`

```
spring.datasource.url=jdbc:mysql://mysql:3306/proddb
spring.datasource.username=root
spring.datasource.password=prod123
```

Each file contains specific configurations for its corresponding profile.

🧠 Reading the Active Profile in Code

You can access the active profile in your code using `@Value`. For example:

```
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.CommandLineRunner;
import org.springframework.stereotype.Component;

@Component
public class StartupLogger implements CommandLineRunner {
    @Value("${spring.profiles.active:default}")
    private String profile;

    public void run(String... args) {
        System.out.println(" Active Profile : " + profile);
    }
}
```

docker-compose.yml

```
version: "3.9"

services:
  app:
    build:
      context: .
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=dev
    depends_on:
      - mysql

  mysql:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: prod123
      MYSQL_DATABASE: proddb
    ports:
      - "3306:3306"
```

🏗️ Creating a Docker Image

Now we need to build a Docker image for the application. How do we do that?

![](https://natancode.com/wp-content/uploads/2023/11/image-1.gif)

First, package your application into a `.jar`:

```
clean install
```

Then build the Docker image:

```
docker image build -t profile .
```

This command creates a Docker image named `profile`.

▶️ Running the Container

Now you can run the container using the image we created:

```
docker run -e SPRING_PROFILES_ACTIVE=dev profile
```

By using the `-e` flag, we define the **active Spring profile** as `dev`.

🐳 Using Docker Compose

You can also use Docker Compose with the following command:

```
docker compose up
```

Inside your `docker-compose.yml`, you can set the active profile like this:

```
environment:
  - SPRING_PROFILES_ACTIVE=prod
```

Conclusion

Using **Spring Boot profiles** with **Docker Compose** gives you better control over how your application behaves across different environments. With minimal configuration, you can isolate responsibilities and achieve greater deployment flexibility.

The key is to keep your environments decoupled and parameterized — exactly what Spring Boot + Docker Compose provides in a clean and powerful way.
