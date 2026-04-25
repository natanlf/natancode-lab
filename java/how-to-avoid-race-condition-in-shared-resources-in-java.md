---
title: "How to avoid race condition in shared resources in Java?"
slug: how-to-avoid-race-condition-in-shared-resources-in-java
date: 2025-06-20T17:33:46
categories: ["Java", "Thread"]
tags: ["Java", "Thread"]
---
Sharing resources between threads can be a challenging task, as it requires ensuring that operations are atomic.

What is a resource?

A resource can be variables, data structures, files, messages, etc.

In Java, we have two important memory areas: the Stack and the Heap.

*   The **stack memory** stores local primitive types, local references, and does _not_ share resources.
*   The **heap memory** stores objects, class members, static variables, and _does_ share resources.

Why use shared resources between threads?

There are many reasons, but a common example is an event emitter that publishes messages to a queue, while multiple threads listen to the queue to process the events.

![](https://natancode.com/wp-content/uploads/2025/06/worker.png)

This means that the **Queue** (a data structure stored in the heap memory) is a shared resource between threads. This enables efficient CPU usage and allows for low latency, as there's no need to create a new thread for every new event.

Another example is making HTTP requests in different threads. Imagine a scenario with 5 HTTP requests, and each response populates a Java List — in this case, the **List** is a shared resource. There’s even a post explaining how to make HTTP requests in multiple threads in Java if you’re interested.

https://natancode.com/2024/12/10/how-to-run-tasks-in-parallel-with-timeout-in-java/

Hands on

![](https://natancode.com/wp-content/uploads/2024/12/giphy-2.webp)

In the following example, we simulate a company’s stock management system, where we can increment (when the company receives products) or decrement (when the company sells products) the stock count.

```
public class MainProblem {

    public static void main(String[] args) throws InterruptedException {
        InventoryCounter inventoryCounter = new InventoryCounter();
        IncrementingThread incrementingThread = new IncrementingThread(inventoryCounter);
        DecrementingThread decrementingThread = new DecrementingThread(inventoryCounter);

        incrementingThread.start();
        decrementingThread.start();
        incrementingThread.join();
        decrementingThread.join();

        System.out.println(inventoryCounter.getItems());
    }

    public static class DecrementingThread extends Thread {
        private InventoryCounter inventoryCounter;

        public DecrementingThread(InventoryCounter inventoryCounter) {
            this.inventoryCounter = inventoryCounter;
        }

        @Override
        public void run() {
            for (int i = 0; i < 100000; i++) {
                inventoryCounter.decrement();
            }
        }
    }

    public static class IncrementingThread extends Thread {
        private InventoryCounter inventoryCounter;

        public IncrementingThread(InventoryCounter inventoryCounter) {
            this.inventoryCounter = inventoryCounter;
        }

        @Override
        public void run() {
            for (int i = 0; i < 100000; i++) {
                inventoryCounter.increment();
            }
        }
    }

    private static class InventoryCounter {
        private int items = 0;

        public void increment() {
            items++;
        }

        public void decrement() {
            items--;
        }

        public int getItems() {
            return items;
        }
    }

}
```

It demonstrates a **race condition** problem: two threads accessing and modifying a shared variable (`items`) without proper synchronization.

🧠 What does this Java code illustrate?

### ✅ Objective:

Create two threads:

*   One thread increments `items` 100,000 times.
*   Another thread decrements `items` 100,000 times.

The final value of `items` _should_ be 0 — since the increments and decrements cancel each other out.

## 🔍 Analysis by parts:

### 1\. **MainProblem Class**

```
InventoryCounter inventoryCounter = new InventoryCounter();
```

Creates an instance that contains the inventory counter.

```
IncrementingThread incrementingThread = new IncrementingThread(inventoryCounter);
DecrementingThread decrementingThread = new DecrementingThread(inventoryCounter);
```

Creates both threads, passing the same InventoryCounter object.

```
incrementingThread.start();
decrementingThread.start();
```

Starts threads in parallel.

```
incrementingThread.join();
decrementingThread.join();
```

Wait for both threads to finish.

```
System.out.println(inventoryCounter.getItems());
```

Displays the final result of items.

### 2\. **`InventoryCounter class`**

```
private int items = 0;
```

Item counter shared between threads.

```
public void increment() { items++; }
public void decrement() { items--; }
```

Increment and decrement methods are not synchronized, i.e. they are not thread-safe.

### 3\. **`IncrementingThread` and `DecrementingThread class`**

Each one calls increment() or decrement() 100,000 times:

```
for (int i = 0; i < 100_000; i++) {
    inventoryCounter.increment(); // ou decrement()
}
```

## ❗ Problem

The operations `items++` and `items--` are **not atomic**. Each consists of:

1.  Reading the value from memory,
2.  Modifying it (add or subtract 1),
3.  Writing it back to memory.

If two threads perform these steps simultaneously, they may overwrite each other’s changes — resulting in incorrect outcomes like:

*   \-13264;
*   5009;
*   5552;
*   \-4298;
*   18828.

✅ How to fix it?

### 1\. **Use `synchronized` methods:**

```
 public synchronized void increment() {
      items++;
  }

  public synchronized void decrement() {
      items--;
  }
```

### 2\. **Use `synchronized` blocks:**

```
private static class InventoryCounter {

      Object lock = new Object();
      private int items = 0;

      public void increment() {
          synchronized (this.lock) {
              items++;
          }
      }

      public void decrement() {
          synchronized (this.lock) {
              items--;
          }
      }

      public int getItems() {
          synchronized (this.lock) {
              return items;
          }
      }
  }
```

Instead of marking the entire method as synchronized, the synchronized block is applied only to the critical section, that is, the piece of code that actually needs to be thread-safe.

### ✅ Advantages:

1.  More control: You decide exactly what gets protected by synchronization.
2.  Potentially better performance: Only the necessary code is locked, freeing up the rest of the execution.
3.  Avoid synchronizing simple read methods that don't need to be synchronized completely.

### 🧱 Comparison with synchronized in the method

```
public synchronized void increment() {
    items++;
}
```

➡️ Blocks the entire method, even if it does several things besides items++.

```
public void increment() {
    synchronized (this.lock) {
        items++;
    }
}
```

➡️ Blocks only the critical part, which is item modification.

#### Use `synchronized (lock)`:

*   It is more flexible and controlled than synchronizing the entire method.
*   Allows you to synchronize multiple blocks independently.
*   It is a best practice in more complex situations where performance and modularity matter.

For small didactic examples, synchronizing the method may be sufficient. But in real production code, using synchronized blocks with a separate lock object is a safer and more scalable approach.

### 3\. **Use AtomicInteger:**

```
private static class InventoryCounter {
      private AtomicInteger items = new AtomicInteger(0);

      public void increment() {
          items.incrementAndGet();
      }

      public void decrement() {
          items.decrementAndGet();
      }

      public int getItems() {
          return items.get();
      }
  }
```

Conclusion

This code shows how to and how not to manipulate variables shared between threads. It is excellent for educational purposes and shows in a practical way the importance of concurrency control.

![](https://natancode.com/wp-content/uploads/2024/11/giphy.webp)
