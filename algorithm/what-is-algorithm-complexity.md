---
title: "What is algorithm complexity?"
slug: what-is-algorithm-complexity
date: 2025-03-19T12:52:18
categories: ["Data Structures + Algorithms", "Java"]
tags: ["Data Structures + Algorithms", "Java"]
---
Algorithm complexity is a fundamental concept in computer science, used to measure the efficiency of an algorithm in terms of execution time and memory usage. By analyzing complexity, we can predict how an algorithm behaves as the input size increases, allowing us to choose more appropriate solutions for different problems. How do we measure it?

Time Complexity

Time complexity refers to the number of steps required to execute an algorithm.

Let's look at a search example where we have an array of integers, and when a number is provided, we search for its index. If the number is not found in the array, we return -1.

![](https://natancode.com/wp-content/uploads/2025/03/sequential-search-1.png)

Time Complexity Analysis

Time complexity is not about the actual execution time but rather the number of steps taken during execution. This is why the amount of input data can influence the number of steps.

**f** is function and **n** is quantity of steps.

*   **Best case:** The searched element is the first in the array. In this example, it’s 89, found in 1 step.
    *   **f(n) = 1** (constant function)
*   **Worst case:** The searched element is the last in the array. In this example, it’s 55, found in 6 steps.
    *   **f(n) = n** (linear function)
*   **Average case:** The searched element is located at a middle position, around **n/2**. In this example, it’s somewhere in the first half of the array.
    *   **f(n) = n/2** (linear function)

```
public class Complexity {

    public static void main(String[] args) {
        int[] numbers = new int[]{89, 25, 3, 36, 14, 55};
        var response = search(3, numbers);
        System.out.println(response);
    }

    private static int search(int number, int[] array) {
        for (int i = 0; i < array.length; i++) {
            if (array[i] == number) {
                return i;
            }
        }

        return -1;
    }

}
```

The more elements we provide, the more steps may be required. Running this algorithm on different computers with different hardware, operating systems, and conditions can cause variations in execution time measured in milliseconds. This is why we measure complexity by steps rather than absolute execution time.

For example, if two developers create code to solve a problem, and Developer 1 gets a response in 3 milliseconds while Developer 2 gets a response in 1 millisecond, it does not necessarily mean that Developer 2's code is better. The developer 2 might simply have a more powerful machine that processes faster. That’s why we evaluate the number of steps to determine which code is more efficient.

![](https://natancode.com/wp-content/uploads/2024/10/image-1.webp)

Space Complexity

Space complexity refers to the amount of memory used to execute an algorithm.

Space Complexity Analysis

Typically, the analysis considers the memory used by the algorithm in addition to what comes from the function’s parameters. In this case, the `i` variable in the `for` loop does not change memory usage based on the array size. Therefore, we have:

**f(n) = 1** (constant function).

Since the algorithm does not create additional data structures that grow with the input size, its space complexity remains constant.

![](https://natancode.com/wp-content/uploads/2024/12/giphy-1fc1c917-2905-4463-8ba3-a7fdaaaa6908.webp)

Conclusion

Understanding algorithm complexity is essential to writing efficient and scalable code. Choosing the right algorithm can directly impact the performance of a system, making it faster and more efficient. When designing solutions, it is essential to consider time and space complexity to ensure optimal performance in different scenarios.
