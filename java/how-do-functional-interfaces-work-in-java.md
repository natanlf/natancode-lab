---
title: "How do functional interfaces work in Java?"
slug: how-do-functional-interfaces-work-in-java
date: 2025-03-05T15:07:32
categories: ["Java", "Quarkus", "Spring"]
tags: ["Java", "Quarkus", "Spring"]
---
Functional Interfaces are a fundamental concept in functional programming introduced in Java 8. They are interfaces that have exactly one abstract method, allowing the use of lambda expressions and method references to make the code more concise and readable.

They can be used in Streams, for example, and promote code reuse.

We can find several Java Functional Interfaces at this link: [https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html)

Hands on

To create a Functional Interface, we need to use the **@FunctionalInterface** annotation, which only allows one abstract method.

```
@FunctionalInterface
public interface StringTransformer {

    String transform(String input);

}
```

However, it is possible to have other types of methods that are not abstract.

```
@FunctionalInterface
public interface StringTransformer {

    String transform(String input);

    default void print() {
        System.out.println("Hello");
    }

}
```

Main Functional Interfaces

**`Function<T, R>`** → Takes an argument of type T and returns a value of type R.

```
  Function<Integer, String> function = value -> "Your age: " + value;
  String yourAge = function.apply(22);
  System.out.println(yourAge);
```

**`Consumer<T>`** → Consumes an argument of type T without returning anything.

```
  Consumer<String> consumer = value -> System.out.println(value);
  consumer.accept("Hello World!");
```

**`Supplier<T>`** → Takes no arguments but returns a value of type T.

```
  Supplier<String> supplier = () -> "Hello";
  String message = supplier.get();
  System.out.println(message);
```

**`Predicate<T>`** → Evaluates a condition and returns a boolean.

```
  Predicate<Integer> predicate = n -> n % 2 == 0;
  boolean isEven = predicate.test(10);
  System.out.println("Is even? " + isEven);
```

Primitive Types

To avoid boxing in primitive types, there are specific options for this. Let's look at an example of **Predicate**.

```
  IntPredicate isEvenPredicate = n -> n % 2 == 0;
  boolean isEven = isEvenPredicate.test(10);
  System.out.println("Is even? " + isEven);
```

There are several options for using primitive types, and this is not exclusive to **Predicate**—many interface types support primitive types.

Other Functional Interfaces

**`BiConsumer<T,U>`** → Receive two input arguments and returns no result.

```
  BiConsumer<String, Integer> sum = (a, b) -> System.out.println(a + b);
  sum.accept("$", 10);
```

**`BiFunction<T,U,R>`** → Accepts two arguments and produces a result.

```
  BiFunction<Integer, Double, BigDecimal> sumInteger = (a, b) -> {
      int s = a + b.intValue();
      return new BigDecimal(s);
  };
  BigDecimal sumMessage = sumInteger.apply(2, 2.5);
  System.out.println(sumMessage);
```

**`UnaryOperator<T>`** → Represents an operation on a single operand that produces a result of the same type as its operand.

```
  UnaryOperator<String> welcome = name -> "Welcome " + name;
  System.out.println(welcome.apply("Mary"));
```

More about Predicates

We can create a **Predicate** and negate it.

```
    IntPredicate isEvenPredicate = n -> n % 2 == 0;
    boolean isEven = isEvenPredicate.test(10);
    System.out.println("Is even? " + isEven);
  
    IntPredicate isOddPredicate = isEvenPredicate.negate();
    boolean isOdd = isOddPredicate.test(9);
    System.out.println("Is odd? " + isOdd);
```

We can also combine **Predicates**.

```
    IntPredicate isLegalAgePredicate = n -> n >= 18;
    boolean isLegalAge = isLegalAgePredicate.test(17);
    System.out.println("Is legal age? " + isLegalAge);
  
    boolean isEvenAndIsLegalAge = isEvenPredicate.and(isLegalAgePredicate).test(20);
    System.out.println("Is Even and is Legal age? " + isEvenAndIsLegalAge);
  
    boolean isEvenOrIsLegalAge = isEvenPredicate.or(isLegalAgePredicate).test(9);
    System.out.println("Is Even or is Legal age? " + isEvenOrIsLegalAge);
```

More about Functions

When using them, we have the options to use **compose** and **andThen**, but what do they mean?

![](https://natancode.com/wp-content/uploads/2023/11/image-9.gif)

**`compose`** → First executes the function passed as an argument, then the main function.

```
    Function<Integer, Integer> multiplyByTwo = x -> x * 2;
    Function<Integer, Integer> addTen = x -> x + 10;

    Function<Integer, Integer> composedFunction = multiplyByTwo.compose(addTen);

    int result = composedFunction.apply(5);
    System.out.println(result);
```

Adds 10 to the given value, which is 5 (as done by the **Function addTen**), and then uses this result in the **Function multiplyByTwo**, which multiplies it by 2, resulting in 30.

**`andThen`** → First executes the main function, then the function passed as an argument.

```
    Function<Integer, Integer> andThenFunction = multiplyByTwo.andThen(addTen);
    int resultAndThen = andThenFunction.apply(5);
    System.out.println(resultAndThen);
```

Multiplies the given value by 2 and then adds 10 to the result.

**Summary**:

*   **`compose(f)`** → Executes **f** first.
*   **`andThen(f)`** → Executes **f** afterward.

More about Consumer

``**`andThen`** →`` Allows executing multiple consumers in sequence while keeping the same argument.

```
    Consumer<String> printUpperCase = name -> System.out.println("UpperCase: " + name.toUpperCase());
    Consumer<String> printLength = name -> System.out.println("Length: " + name.length());
    Consumer<String> combinedConsumer = printUpperCase.andThen(printLength);

    combinedConsumer.accept("Java");
```

First, it executes **printUpperCase**, then **printLength**.

Custom Interface

Creating and using a **Functional Interface** is simple.

```
@FunctionalInterface
public interface MathOperation {

    int operate(int a, int b);

}
```

```
    MathOperation sum = (a, b) -> a + b;
    MathOperation subtract = (a, b) -> a - b;
  
    System.out.println(sum.operate(2,2));
    System.out.println(subtract.operate(2,2));
```

Method Reference

Let's see an example without **Method Reference** and then how it looks using **Method Reference**.

First, we create the **record Person** with **name** and **email**.

```
record Person(String name, String email){}
```

Now, let's create a **Function**.

```
  Function<Person, String> function = person -> person.name();
  System.out.println(function.apply(new Person("Mary", "mary@fakemail.com")));
```

We can use **Method Reference** as in the following example:

```
Function<Person, String> function = Person::name;
```

**person -> person.name()** has the same effect as **Person::name**.

Both represent an instance of the **record Person** that makes use of **name**.

Stream

```
  List<String> names = List.of("Alice", "Bob", "Mary");
  
  List<String> uppercaseNames = names.stream()
          .map(String::toUpperCase)
          .toList();
  
  System.out.println(uppercaseNames);
```

Here, we use a **Function<T, R>** to map values from the **Stream**.

Asynchronous

```
 public static void main(String[] args) throws ExecutionException, InterruptedException {

        Supplier<String> task = () -> {
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                throw new IllegalStateException(e);
            }
            return "Asynchronous execution completed!";
        };

        CompletableFuture<String> response = CompletableFuture.supplyAsync(task);

        System.out.println(response.get());
    }
```

Conclusion

**Functional Interfaces** make Java code more modular, concise, and reusable, facilitating the adoption of functional paradigms. When combined with **lambda expressions** and function composition (**compose, andThen**, etc.), they enable writing powerful and elegant solutions for data manipulation and asynchronous operations.
