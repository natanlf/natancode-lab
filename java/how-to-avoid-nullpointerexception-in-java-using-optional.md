---
title: "How to avoid NullPointerException in Java using Optional?"
slug: how-to-avoid-nullpointerexception-in-java-using-optional
date: 2025-04-07T22:39:36
categories: ["Java", "Quarkus", "Spring"]
tags: ["Java", "Quarkus", "Spring"]
---
**Optional** was introduced in Java 8 as a way to handle potentially null values more safely and expressively. It helps to avoid **NullPointerException** and makes the code more readable.

Hands on

![](https://natancode.com/wp-content/uploads/2024/12/giphy.webp)

Creating an Optional

**Optional** can be created in several ways:

```
  Optional<String> name = Optional.of("Bob");
  Optional<String> nameNullable = Optional.ofNullable("Mary");
  Optional<String> nameNullable2 = Optional.ofNullable(null);
  Optional<String> nameEmpty =  Optional.empty();
```

*   **Optional.of()**: Does not accept null values and returns an **Optional** with the given value;
*   **Optional.ofNullable()**: Accepts null values, returns an **Optional** with the given value, or if the value is null, returns an **Optional.empty**;
*   **Optional.empty()**: Returns an **Optional** without a value.

Printing the results will show the following output.

```
Optional[Bob]
Optional[Mary]
Optional.empty
Optional.empty
```

Checking for Value Presence

**Optional** provides methods to check if a value is present before accessing it:

```
  Optional<String> name = Optional.ofNullable("Bob");

  if (name.isPresent()) {
      System.out.println(name.get());
  } else {
      System.out.println("No name");
  }
  
  name.ifPresent(value -> System.out.println(value));
  
  name.ifPresentOrElse(
          value -> System.out.println(value),
          () -> System.out.println("No name")
  );
  
  if (name.isEmpty()) {
        System.out.println("It's empty!");
  }
```

*   **isPresent()**: If a value is present, returns **true**; otherwise, **false**.
*   **ifPresent()**: If a value is present, executes the given action.
*   **ifPresentOrElse()**: Similar to **ifPresent**, but has a second parameter that is executed if no value is present.
*   **isEmpty()**: If no value is present, returns **true**; otherwise, **false**.

```
Bob
Bob
Bob
```

Setting Default Values

Instead of checking if a value is present, we can define a default value using **orElse** and **orElseGet**:

```
Optional<String> name = Optional.ofNullable(null);

String name1 =  name.orElse("Default");
String name2 = name.orElseGet( () -> "We can generate some name" );
```

*   **orElse()**: If a value is present, returns the value; otherwise, returns the default value.
*   **orElseGet()**: If a value is present, returns the value; otherwise, returns the result produced by the supplying function.

Exceptions

```
Optional<String> name = Optional.ofNullable(null);
name.orElseThrow();
```

There are two ways to use it: with and without parameters.

**orElseThrow()**: If no value is present, throws **NoSuchElementException**.

```
java.util.NoSuchElementException: No value present
```

```
name.orElseThrow( () -> new IllegalArgumentException("Value not found") );
```

**orElseThrow(Supplier<? extends X> exceptionSupplier)**: If a value is present, returns the value; otherwise, throws an exception produced by the given exception-supplying function.

```
java.lang.IllegalArgumentException: Value not found
```

Map and FlatMap

```
  Optional<String> name = Optional.of("Java");
  Optional<String> nameUpperCase = name.map(value -> value.toUpperCase());
  Optional<String> nameUpperCaseReference = name.map(String::toUpperCase);
```

**map()**: Allows transforming the value inside the **Optional** and returns an **Optional** with the new value.

```
  Optional<Optional<String>> nestedOptional = Optional.of(nameUpperCaseReference);
  Optional<String> regularOptional = nestedOptional.flatMap(op -> op);
```

**flatMap()**: Used to transform and "flatten" nested **Optional** instances.

Filtering the values

```
  Optional<String> name = Optional.of("Java");
  Optional<String> nameFiltered = name.filter(n -> n.endsWith("va"));
```

**filter()**: Applies conditions to filter the **Optional** value.

Conclusion

**Optional** allows representing the absence of a value explicitly, encouraging better handling of null returns without the need for excessive **if** checks to avoid null.

When used correctly, Optional improves code maintainability by promoting more functional patterns with **map()**, **flatMap()**, **orElse()**, **orElseGet()**, and **ifPresent()**, making the value-handling flow more seamless and readable.

![](https://natancode.com/wp-content/uploads/2024/12/giphy-1.webp)
