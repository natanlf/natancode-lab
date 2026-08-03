---
title: "How to use Generic Types in Java?"
slug: how-to-use-generic-types-in-java
date: 2025-01-16T00:21:24
categories: ["Java", "Quarkus", "Spring"]
tags: ["Java", "Quarkus", "Spring"]
---
The use of Generics in Java improves security, readability, and code reuse, simplifying the development of robust and secure applications. Let's see some usage examples below.

Generic Methods

```
public class GenericMethods {

    public static <T> void print(T item) {
        System.out.println("Print item");
        System.out.println(item);
        System.out.println("\n");
    }

    public static <T> void print(T[] items) {
        System.out.println("Print items");
        for (T item : items) {
            System.out.println(item);
        }
        System.out.println("\n");
    }

}
```

<T>: It's a generic type parameter, accepting any type.

T Item: The method accepts a parameter of the generic type.

```
    GenericMethods.print("Hello world!");
    GenericMethods.print(123);

    GenericMethods.print(new String[]{"cat", "dog", "bird"});
    GenericMethods.print(new Integer[]{1, 2, 3});
```

We can specify, for example, String and Integer as in the example.

Generic Bounded Types

```
public class GenericBoundedTypes {

    public static <T extends Number> double sum(T num1, T num2) {
        return num1.doubleValue() + num2.doubleValue();
    }

}
```

T extends Number: The main difference compared to the previous example is that, in this case, it can be any type as long as it inherits from Number.

```
    System.out.println(GenericBoundedTypes.sum(2, 2));
    System.out.println(GenericBoundedTypes.sum(1.5, 2));
```

Generic Classes

```
public class GenericClass<T> {

    private T value;

    public GenericClass(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }

    public void setValue(T value) {
        this.value = value;
    }
}
```

GenericClass<T>: It is a class that accepts any type.

```
    GenericClass<Integer> someNumber = new GenericClass<>(123);
    System.out.println(someNumber.getValue());

    GenericClass<String> someName = new GenericClass<>("Mary");
    System.out.println(someName.getValue());
```

Generic Interfaces

```
public interface GenericInterface<T> {

    void print(T value);

}
```

```
public class StringPrinter implements GenericInterface<String> {

    @Override
    public void print(String value) {
        System.out.println("StringPrinter: " + value);
    }

}
```

```
public class IntegerPrinter implements GenericInterface<Integer> {

    @Override
    public void print(Integer value) {
        System.out.println("IntegerPrinter: " + value);
    }

}
```

GenericInterface<T>: It is a generic interface, so it accepts any type of data when implemented.

```
    GenericInterface<String> printString = new StringPrinter();
    printString.print("Hello!");

    GenericInterface<Integer> printInteger = new IntegerPrinter();
    printInteger.print(10);
```

WildCard (`?`)

```
public class WildCard {

    public static void printList(List<?> list) {
        for (Object object : list) {
            System.out.println(object);
        }
    }

    public static double sum(List<? extends Number> list) {
        double sum = 0;

        for (Number number : list) {
            sum+=number.doubleValue();
        }

        return sum;
    }

}
```

<?>: It accepts receiving a generic type.

```
    WildCard.printList(List.of("Dog", "Cat", "Bird"));
    WildCard.printList(List.of(1, 2, 3, 4, 5));
  
    List<Integer> intList = List.of(1, 2, 3);
    List<Double> doubleList = List.of(1.5, 2.5, 3.5);
  
    System.out.println(WildCard.sum(intList));
    System.out.println(WildCard.sum(doubleList));
```

Observations

It is not necessary to use the letter "T"; it is commonly found in many examples but can be different.

```
    public static <J extends Number> double sum(J num1, J num2) {
        return num1.doubleValue() + num2.doubleValue();
    }
```

Another important point is that Java, libraries, and frameworks use Generics in various situations. For example, the `Comparable` interface in Java.

```
public interface Comparable<T> {
    public int compareTo(T o);
}
```

Since we are talking about generic types, we can create our own types and use generics.

Generics improve code reusability and readability, making it clearer and more flexible.

![](https://natancode.com/wp-content/uploads/2024/12/giphy-1.webp)
