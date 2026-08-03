---
title: "What is Garbage Collection in Java?"
slug: what-is-garbage-collection-in-java
date: 2024-08-20T23:35:49
categories: ["Java", "Quarkus", "Spring"]
tags: ["Java", "Quarkus", "Spring"]
---
It is a process that manages memory in software written in Java. When running programs on the JVM (Java Virtual Machine), objects are created in the Heap memory. When these objects are no longer needed, the Garbage Collector will find unused objects and remove them from memory, helping to prevent memory leaks.

![](https://natancode.com/wp-content/uploads/2023/10/image.gif)

* * *

We need to understand that there is the Call Stack, Stack Memory, and Heap Memory.

Call Stack, Stack Memory and Heap Memory

![](https://natancode.com/wp-content/uploads/2024/08/Garbage-Collection-1.jpg)

There is Stack Memory and Heap Memory. Stack Memory contains the Call Stack.

Each method that is executing resides in the call stack.

The variables `mary` and `x` are in the stack memory.

The variable `mary` was created in the stack memory and holds a value that is a reference to an object located in the heap memory.

In the case of the variable `x`, its value is stored in the stack memory.

Example

![](https://natancode.com/wp-content/uploads/2023/10/image-2.gif)

I created the `Client` class.

```
public class Client {

    private String name;
    private String email;

    public Client(String name, String email) {
        this.name = name;
        this.email = email;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }
}
```

* * *

Main class.

```
public class Main {
    public static void main(String[] args) {
        int x = 10;
        createClient();
    }

    static void createClient() {
        Client mary = new Client("Mary", "mary@natancode.com");
        System.out.println(mary);
    }
}
```

* * *

When running the `main` method, it will be on the call stack. Let's debug to see step by step what happens.

![](https://natancode.com/wp-content/uploads/2024/08/call-stack-java.png)

* * *

In the image above, we executed line 5, and the program is paused at line 6.

On the left side, we see the `main` method running, and on the right side, the variable `x` with the value 10. This means that in the call stack area, we have the `main` method call and the variable `x` along with its value in the stack memory. Next, we'll see what happens when the `createClient()` method is called.

We have another method in the call stack.

![](https://natancode.com/wp-content/uploads/2024/08/call-stack-3-java.png)

* * *

The variable `mary` was created within the stack memory and holds a value that is a reference to an object located in the heap memory.

![](https://natancode.com/wp-content/uploads/2024/08/heap-memory-java.png)

* * *

When we finish executing the `createClient` method, it will exit the call stack, and there will no longer be a variable pointing to the object in the heap memory. At some point, the Garbage Collector (which is responsible for carrying out the Garbage Collection process) will automatically detect this and remove the object from memory. This process is known as **Garbage Collection**.

![](https://natancode.com/wp-content/uploads/2024/08/call-stack-and-heap-memory-2-java.png)

* * *

![](https://natancode.com/wp-content/uploads/2023/11/image.gif)

This was an example of how it works. This management is done automatically and helps prevent memory leaks.
