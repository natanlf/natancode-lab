---
title: "Why use Abstract Classes in Java?"
slug: why-use-abstract-classes-in-java
date: 2025-02-03T12:06:19
categories: ["Java", "OOP"]
tags: ["Java", "OOP"]
---
Abstract classes in Java are templates that cannot be instantiated directly and may include abstract methods (without implementation) and concrete methods (with implementation). They are used to define common behaviors that subclasses must implement while sharing reusable code. Abstract methods act as placeholders, enforcing a contract that subclasses must fulfill.

Let's look at an example. Imagine a veterinary system; we need to have information about animals. For instance, a dog and a cat are both animals.

```
public abstract class Animal {
    protected String name;

    public Animal(String name) {
        this.name = name;
    }

    public abstract void makeSound();

    public void eat() {
        System.out.println(name + " is eating.");
    }
}
```

```
class Dog extends Animal {
    public Dog(String name) {
        super(name);
    }

    @Override
    public void makeSound() {
        System.out.println(name + " says: Woof!");
    }
}
```

```
class Cat extends Animal {
    public Cat(String name) {
        super(name);
    }

    @Override
    public void makeSound() {
        System.out.println(name + " says: Meow!");
    }
}
```

We have 3 classes: the `Animal` class, which is abstract and cannot be instantiated.

**Why create a class that cannot be instantiated?**

![](https://natancode.com/wp-content/uploads/2025/02/giphy.webp)

This depends on the business context. In this specific case, it’s important to know which animal needs attention; we can’t use something generic. We need to know if it’s a cat or a dog because this impacts the veterinary treatment. Therefore, in this case, it’s necessary to specify if it’s a cat or dog. So, we can’t instantiate an animal directly, but we can instantiate a dog or a cat, which are also animals but more specific.

![](https://natancode.com/wp-content/uploads/2024/10/image-1.webp)

Benefits:

*   **Code reuse:** we can leverage common attributes and methods in all subclasses;
*   **Polymorphism:** we can use the `Animal` class to enable polymorphism, as a dog is an animal, and so is a cat;
*   **Extensibility:** adding a new class, such as `Bird`, becomes straightforward;
*   **Partial implementation:** the `makeSound` method is abstract, so the subclasses are responsible for providing their own implementation.
