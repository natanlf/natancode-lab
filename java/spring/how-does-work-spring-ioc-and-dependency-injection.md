---
title: "How do Spring IoC and Dependency Injection work?"
slug: how-does-work-spring-ioc-and-dependency-injection
date: 2024-11-05T21:42:09
categories: ["API", "Java", "Spring"]
tags: ["Java", "Spring"]
---
Before discussing how **_Inversion of Control (IoC)_** and **_Dependency Injection (DI)_** work in Spring, it's essential to understand what each of them means. If you have any questions, check out this [post](https://natancode.com/2024/04/12/what-is-dependency-injection/) where I cover these two very important concepts.

_**Spring has a container that manages Beans**_.

**_Beans_** are objects managed by the Spring container and are the dependencies. A Bean can be injected into other Beans.

Management is done automatically by Spring; we can define what a Bean is through an annotation and inject it as well.

Notice that in the following example, we use _**@Component**_, which indicates that this is a Spring component—it is a Bean.

```
@Getter
@Setter
@Component
@ConfigurationProperties("otica.storage")
public class StorageProperties {

    private Local local = new Local();
    private S3 s3 = new S3();
    private TypeStorage type = TypeStorage.LOCAL;

    public enum TypeStorage {
        LOCAL, S3
    }

    @Getter
    @Setter
    public class Local {
        private Path directoryPhotos;
    }

    @Getter
    @Setter
    public class S3 {
        private String idKeyAccess;
        private String keyAccessSecret;
        private String bucket;
        private Regions region;
        private String directoryPhotos;
    }

}
```

Spring manages this Bean automatically by placing it in its IoC Container, and we can use it in the application through dependency injection.

```
@Configuration
public class StorageConfig {

    @Autowired
    private StorageProperties storageProperties;

    @Bean
    @ConditionalOnProperty(name = "otica.storage.type", havingValue = "s3")
    public AmazonS3 amazonS3() {
        BasicAWSCredentials credentials = new BasicAWSCredentials(
                storageProperties.getS3().getIdKeyAccess(), storageProperties.getS3().getKeyAccessSecret());

        return AmazonS3ClientBuilder.standard()
                .withCredentials(new AWSStaticCredentialsProvider(credentials))
                .withRegion(storageProperties.getS3().getRegion())
                .build();
    }

    @Bean
    public PhotoStorageService photoStorageService() {
        if (TypeStorage.S3.equals(storageProperties.getType())) {
            return new S3PhotoStorageService();
        } else {
            return new LocalPhotoStorageService();
        }
    }

}
```

Since we registered StorageProperties as a _**Bean**_ in the Spring container, we can use the _**@Autowired**_ annotation to perform **_dependency injection_**.

An interesting point is that when creating a Bean, it’s not always necessary to use the _**@Component**_ annotation. We can create a class with the _**@Configuration**_ annotation and define methods with the **_@Bean_** annotation, which also allows us to create Beans.

How is it possible?

![](https://natancode.com/wp-content/uploads/2023/11/image-9.gif)

**_@Configuration_** makes use of _**@Component**_.

```
package org.springframework.context.annotation;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import org.springframework.core.annotation.AliasFor;
import org.springframework.stereotype.Component;

@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Configuration {
    @AliasFor(
        annotation = Component.class
    )
    String value() default "";

    boolean proxyBeanMethods() default true;

    boolean enforceUniqueMethods() default true;
}
```

Other annotations also utilize _**@Component**_. For example, _**@Service**_ and _**@Repository**_.

```
package org.springframework.stereotype;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import org.springframework.core.annotation.AliasFor;

@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Service {
    @AliasFor(
        annotation = Component.class
    )
    String value() default "";
}
```

* * *

```
package org.springframework.stereotype;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import org.springframework.core.annotation.AliasFor;

@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Component
public @interface Controller {
    @AliasFor(
        annotation = Component.class
    )
    String value() default "";
}
```

Cool, but how does all this magic happen in Spring?

In the class that starts a Spring application, the _**@SpringBootApplication**_ annotation is used, which includes another annotation called _**@ComponentScan**_. This configures that all classes from the package where this annotation is used can become a Bean if they use _**@Component**_ or another annotation that allows the creation of a Bean. That’s why everything is managed automatically by Spring.

```
package org.springframework.boot.autoconfigure;

import java.lang.annotation.Documented;
import java.lang.annotation.ElementType;
import java.lang.annotation.Inherited;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;
import org.springframework.beans.factory.support.BeanNameGenerator;
import org.springframework.boot.SpringBootConfiguration;
import org.springframework.boot.context.TypeExcludeFilter;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;
import org.springframework.context.annotation.ComponentScan.Filter;
import org.springframework.core.annotation.AliasFor;

@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {
    @AliasFor(
        annotation = EnableAutoConfiguration.class
    )
    Class<?>[] exclude() default {};

    @AliasFor(
        annotation = EnableAutoConfiguration.class
    )
    String[] excludeName() default {};

    @AliasFor(
        annotation = ComponentScan.class,
        attribute = "basePackages"
    )
    String[] scanBasePackages() default {};

    @AliasFor(
        annotation = ComponentScan.class,
        attribute = "basePackageClasses"
    )
    Class<?>[] scanBasePackageClasses() default {};

    @AliasFor(
        annotation = ComponentScan.class,
        attribute = "nameGenerator"
    )
    Class<? extends BeanNameGenerator> nameGenerator() default BeanNameGenerator.class;

    @AliasFor(
        annotation = Configuration.class
    )
    boolean proxyBeanMethods() default true;
}
```

![](https://natancode.com/wp-content/uploads/2024/10/image-1.webp)

Regarding dependency injection, **_@Autowired_** was used, but we can also perform _**dependency injection**_ through the constructor. In this case, we don't need to specify any annotations; it just needs to be a Bean managed by Spring.

```
    private StorageProperties storageProperties;

    public StorageConfig(StorageProperties storageProperties) {
        this.storageProperties = storageProperties;
    }
```

As we can see, Spring manages Beans automatically, which brings more productivity and simplicity to projects.
