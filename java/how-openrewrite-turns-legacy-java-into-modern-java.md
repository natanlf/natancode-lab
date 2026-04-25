---
title: "How OpenRewrite Turns Legacy Java into Modern Java?"
slug: how-openrewrite-turns-legacy-java-into-modern-java
date: 2026-01-14T15:14:02
categories: ["API", "Clean Code", "Java", "Refactoring", "Secure"]
tags: ["API", "Clean Code", "Java", "Refactoring", "Secure"]
---
Maintaining a Java system up to date is not just a matter of performance or access to new features — it is a matter of security, support, and software longevity. However, migrating a project from an older Java version to a newer one usually involves a significant manual effort: refactoring code, adapting APIs, reviewing thousands of files, and still running the risk of introducing bugs.

That is exactly where OpenRewrite comes in.

What is OpenRewrite?

> _"OpenRewrite is an open-source automated refactoring ecosystem for source code, enabling developers to effectively eliminate technical debt within their repositories."_

As of January 2025, OpenRewrite supports the languages:

*   Java
*   Kotlin
*   Groovy
*   JavaScript
*   TypeScript

Data formats

*   HCL
*   JSON
*   Properties
*   Protobuf
*   TOML
*   XML
*   YAML

Build tools

*   Maven
*   Gradle
*   Bazel, via the [Moderne CLI](https://docs.moderne.io/user-documentation/moderne-cli/getting-started/cli-intro)

Frameworks

Many different frameworks are supported such as (but not limited to):

*   Spring
*   Quarkus
*   Micronaut
*   Jakarta

Recipe

Next, we will walk through an example of how to migrate a project from Java 11 to Java 21. The idea is that when switching to Java 21, the code will also be updated to a more modern version. To achieve this, it is necessary to use the correct recipe, which is:

**org.openrewrite.java.migrate.UpgradeToJava21**

> "_This recipe will apply changes commonly needed when migrating to Java 21. This recipe will also replace deprecated API with equivalents when there is a clear migration strategy. Build files will also be updated to use Java 21 as the target/source and plugins will be also be upgraded to versions that are compatible with Java 21._"

Hands on

In this example, a Maven project was created using JDK 11.

The `Main` class is using string concatenation across multiple lines, something that in more recent versions of Java can be done using **Text Blocks**. The idea is that by migrating from Java 11 to Java 21, the code will be refactored to use Text Blocks.

```
package com.natancode;

public class Main {
    public static void main(String[] args) {
        String text = "<html>\n" +
                "  <body>\n" +
                "    <p>Hello </p>\n" +
                "  </body>\n" +
                "</html>";
    }
}

```

Below is the `pom.xml`. Notice that in the `properties` section, Java 11 is specified.

```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.natancode</groupId>
    <artifactId>openrewrite</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>11</maven.compiler.source>
        <maven.compiler.target>11</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>

</project>
```

It is necessary to add the following configuration to the `pom.xml`, so that we can use OpenRewrite and specify the recipe mentioned earlier that migrates the project to Java 21.

```
  <build>
        <plugins>
            <plugin>
                <groupId>org.openrewrite.maven</groupId>
                <artifactId>rewrite-maven-plugin</artifactId>

                <version>6.27.0</version>
                <configuration>
                    <exportDatatables>true</exportDatatables>
                    <activeRecipes>
                        <recipe>org.openrewrite.java.migrate.UpgradeToJava21</recipe>
                    </activeRecipes>

                </configuration>

                <dependencies>
                    <dependency>
                        <groupId>org.openrewrite.recipe</groupId>
                        <artifactId>rewrite-migrate-java</artifactId>
                        <version>3.25.0</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>
```

You need to run the following command:

```
mvn rewrite:run
```

After that, the `pom.xml` will have its properties updated to Java 21.

```
    <properties>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>
```

The code is updated to the most recent Java version using **Text Blocks**.

```
package com.natancode;

public class Main {
    public static void main(String[] args) {
        String text = """
                <html>
                  <body>
                    <p>Hello </p>
                  </body>
                </html>""";
    }
}
```

![](https://natancode.com/wp-content/uploads/2024/10/image-1.webp)

Conclusion

OpenRewrite is not just a version upgrade tool — it is a **code evolution platform**.

When migrating from Java 11 to Java 21, it is not enough to simply compile with a new JDK. The real value comes from allowing the code to use modern language features such as **Text Blocks, Records, Switch expressions, Pattern Matching**, and more.

The automatic conversion from string concatenation to Text Blocks is an excellent example of this:  
the behavior does not change, but the quality of the code improves dramatically.

If you maintain large, long-lived, or legacy Java systems, OpenRewrite enables something that used to be almost impossible:  
👉 **modernizing your code at scale, with safety and confidence.**

More information:

![](https://natancode.com/wp-content/uploads/2024/11/giphy.webp)
