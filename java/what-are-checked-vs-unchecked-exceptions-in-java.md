---
title: "What are Checked vs Unchecked Exceptions in Java?"
slug: what-are-checked-vs-unchecked-exceptions-in-java
date: 2025-06-04T15:13:41
categories: ["Java", "Quarkus", "Spring"]
tags: ["Java", "Quarkus", "Spring"]
---
In Java, exceptions are part of the **Throwable** hierarchy and are used to signal abnormal situations during the execution of a program. Understanding how exceptions are structured helps in designing more robust and readable code.

At the top of the hierarchy is the `Throwable` class, which has two direct subclasses:

*   `Error`: Serious problems that applications should not try to catch (e.g., `OutOfMemoryError`, `StackOverflowError`).
*   `Exception`: Conditions that applications might want to catch.

![](https://natancode.com/wp-content/uploads/2025/06/exceptions.png)

Checked Exceptions

Checked exceptions are the ones that you are required to handle, either with a **try-catch block** or even with a **throws statement** (re-throwing it to another location).. These exceptions are **checked at compile time**.

**Examples:**

*   `IOException`
*   `SQLException`
*   `ParseException`

```
public static String getContentFromResource(String resourceName) {
        try {
            InputStream stream = ResourceUtils.class.getResourceAsStream(resourceName);
            return StreamUtils.copyToString(stream, Charset.forName("UTF-8"));
        } catch (IOException e) {
            throw new RuntimeException(e);
        }
    }
```

If you forget to handle a checked exception, the compiler will raise an error.

**When to use?**  
When the failure is expected (e.g., I/O issues or database access problems).

Unchecked Exceptions

Unchecked exceptions are **not required to be declared or handled**, and are checked at **runtime**. They extend `RuntimeException`.

**Examples:**

*   `NullPointerException`
*   `IllegalArgumentException`
*   `IndexOutOfBoundsException`

```
public void divide(int a, int b) {
    int result = a / b; // Can throws ArithmeticException (unchecked)
}
```

**When to use?**  
When the failure indicates a programming error or incorrect use of the API.

Conclusion

Understanding the exception hierarchy helps you make more informed decisions about how your code should respond to failures. When choosing between checked and unchecked exceptions, consider the type of error and whether it can (or should) be recovered from.

![](https://natancode.com/wp-content/uploads/2024/12/giphy-1fc1c917-2905-4463-8ba3-a7fdaaaa6908.webp)
