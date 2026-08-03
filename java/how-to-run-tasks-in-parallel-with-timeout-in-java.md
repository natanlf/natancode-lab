---
title: "How to run tasks in parallel with timeout in Java?"
slug: how-to-run-tasks-in-parallel-with-timeout-in-java
date: 2024-12-10T23:46:21
categories: ["Java", "Quarkus", "Spring"]
tags: ["Java", "Quarkus", "Spring", "Thread"]
---
The use of Threads is essential to improving the performance and responsiveness of modern applications. With threads, it is possible to execute multiple tasks simultaneously, maximizing processor resources. They allow intensive operations, such as calculations or accessing external resources (e.g., databases, APIs), to be performed in parallel without blocking the main execution.

Imagine the following scenario where it is necessary to fetch information from different APIs, with an async request for each API. There is also a need to wait for a timeout for each request. This scenario describes the example below.

![](https://natancode.com/wp-content/uploads/2024/12/giphy-1.webp)

Hands on

```
import java.util.List;
import java.util.concurrent.*;

public class ParallelRequestsWithTimeout {

    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(5);

        List<CompletableFuture<String>> futures = List.of(
                simulateRequest(executor, 1000, "task 1"),
                simulateRequest(executor, 2000, "task 2"),
                simulateRequest(executor, 3000, "task 3"),
                simulateRequest(executor, 1500, "task 4"),
                simulateRequest(executor, 2500, "task 5")
        );

        long startTime = System.nanoTime();

        try {
            CompletableFuture<Void> allOfWithTimeout = CompletableFuture.allOf(
                    futures.toArray(new CompletableFuture[0])
            ).orTimeout(3, TimeUnit.SECONDS);

            allOfWithTimeout.join();
            System.out.println("All requests were completed on time!\n");


        } catch (CompletionException e) {
            System.out.println("Timeout: Not all requests were completed in time!");
        } finally {
            futures.forEach(future -> System.out.println(future.getNow("Fail future: "
                    + future)));

            futures.forEach(future -> future.cancel(true));
            executor.shutdown();
        }

        long endTime = System.nanoTime();
        long totalTimeMillis = (endTime - startTime) / 1_000_000;

        System.out.println("Total runtime: " + totalTimeMillis + " ms");
    }

    private static CompletableFuture<String> simulateRequest(ExecutorService executor, int duration, String task) {
        return CompletableFuture.supplyAsync(() -> {
            try {
                Thread.sleep(duration);
                return task+" - Request finished in " + duration + " ms";
            } catch (InterruptedException e) {
                return "Request interrupted!";
            }
        }, executor);
    }
}
```

This code manages parallel requests with a timeout using `CompletableFuture` and a thread pool. Let’s explain it step by step:

### Main steps:

1.  **Creation of ExecutorService:**:  
    A fixed thread pool of 5 threads is created using `Executors.newFixedThreadPool`.
2.  **Simulation of asynchronous requests:**
    *   The **simulateRequest** method creates tasks simulating requests of varying durations using **CompletableFuture.supplyAsync**, which delegates execution to the thread pool.
    *   Each task returns a message indicating either completion or interruption.
3.  **Execution and Timeout:**
    *   The tasks are stored in a list of **CompletableFuture**.
    *   **CompletableFuture.allOf** groups all tasks to wait for their completion.
    *   **orTimeout** sets a 3-second limit for all tasks to finish; otherwise, a **CompletionException** will be thrown.
4.  **Exception handling and cancellation:**
    *   If a timeout occurs, the tasks are cancelled with future.cancel(true).
    *   getNow returns the result value if completed, otherwise it returns the given value.
5.  **Total time measurement:**
    *   The execution time is calculated with System.nanoTime.

### Expected output:

*   If all tasks complete within 3 seconds, a message will indicate success.
*   Otherwise, a timeout will be indicated, and the remaining threads will be canceled.

```
List<CompletableFuture<String>> futures = List.of(
                simulateRequest(executor, 1000, "task 1"),
                simulateRequest(executor, 2000, "task 2"),
                simulateRequest(executor, 3500, "task 3"),
                simulateRequest(executor, 1500, "task 4"),
                simulateRequest(executor, 2500, "task 5")
        );
```

With the values provided above, we have the following result:

![](https://natancode.com/wp-content/uploads/2024/12/image.png)

Since one thread took more than 3 seconds to execute, a timeout occurred.

```
        List<CompletableFuture<String>> futures = List.of(
                simulateRequest(executor, 1000, "task 1"),
                simulateRequest(executor, 2000, "task 2"),
                simulateRequest(executor, 3000, "task 3"),
                simulateRequest(executor, 1500, "task 4"),
                simulateRequest(executor, 2500, "task 5")
        );
```

Now we set the maximum to 3 seconds, and we have the following result.

![](https://natancode.com/wp-content/uploads/2024/12/image-1.png)

No timeout occurred because no thread exceeded 3 seconds.

### Possible use:

This example is useful for simulating high-load scenarios or external services with time limits. It covers thread management, timeouts, and best practices for resource cleanup.

![](https://natancode.com/wp-content/uploads/2024/12/giphy-88d6279f-446f-411f-b232-2414d8318633.webp)
