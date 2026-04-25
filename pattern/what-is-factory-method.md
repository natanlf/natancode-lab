---
title: "What is Factory Method?"
slug: what-is-factory-method
date: 2025-02-10T15:32:15
categories: ["Design Patterns", "Java", "OOP"]
tags: ["Design Patterns"]
---
The **Factory Method** is a creational design pattern that provides an interface for creating objects in a superclass but allows subclasses to alter the type of objects that will be created.

![](https://natancode.com/wp-content/uploads/2025/01/image.png)

Image from: [https://refactoring.guru/images/patterns/diagrams/factory-method/structure.png](https://refactoring.guru/images/patterns/diagrams/factory-method/structure.png)

In addition to the use of polymorphism, it is possible to create either ProductA or ProductB, depending on which factory is being used. The client does not have the responsibility of creating the products; the factories handle that.

Hands on

![](https://natancode.com/wp-content/uploads/2025/01/factory-method-white.drawio.png)

Let's start by specifying the products.

Abstract Product

```
public interface Car {
    void assemble();
}
```

Concrete Products

```
public class Sedan implements Car {

    @Override
    public void assemble() {
        System.out.println("Assembling a Sedan");
    }

}
```

```
public class Suv implements Car {

    @Override
    public void assemble() {
        System.out.println("Assembling an SUV");
    }

}
```

Now let's specify the factories.

Abstract Factory

```
public abstract class CarFactory {

    public abstract Car createCar();

}
```

It contains the createCar method, which will be used by the concrete factories, allowing us to create the concrete products.

Concrete Factories

```
public class SedanFactory extends CarFactory {

    @Override
    public Car createCar() {
        return new Sedan();
    }

}
```

```
public class SuvFactory extends CarFactory {

    @Override
    public Car createCar() {
        return new Suv();
    }

}
```

Each concrete factory returns a different concrete product. We use polymorphism, making the code more flexible.

Client

```
    CarFactory sedanFactory = new SedanFactory();
    Car sedan = sedanFactory.createCar();
    sedan.assemble();

    CarFactory suvFactory = new SuvFactory();
    Car suvCar = suvFactory.createCar();
    suvCar.assemble();
```

This way, the client doesn't need to create the concrete product; the Factory holds this responsibility.

This is a flexible way to create products. If it becomes necessary to create a different type of car, we can simply add this new concrete product and create a factory for it, ensuring that the existing classes do not need to be modified.

![](https://natancode.com/wp-content/uploads/2025/02/giphy.gif)
