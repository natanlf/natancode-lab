---
title: "How does work Asymptotic Notations for Analysis of Algorithms?"
slug: how-does-work-asymptotic-notations-for-analysis-of-algorithms
date: 2025-10-30T15:01:06
categories: ["Data Structures + Algorithms"]
tags: ["Data Structures + Algorithms"]
---
When we develop algorithms, one of the biggest concerns is efficiency: how long it takes to run and how much memory it consumes.

But measuring efficiency in absolute numbers (seconds or megabytes) is not enough, because these values change depending on the machine, the compiler, and even small code adjustments.

That’s where **Asymptotic Notation** comes in: a standardized way to describe the efficiency of an algorithm regardless of hardware or implementation.

## What is Asymptotic Notation?

Asymptotic notation describes the behavior of an algorithm as the input size grows indefinitely (that is, when _n → ∞_).

It ignores constants and lower-order terms that have little influence on large inputs.

It abstracts away minor details and focuses on the **growth rate** of the algorithm.

Exemplo:

*   **Example:**
*   n/2 → n
*   5n → n
*   8n² + 17n + 10 → n²
*   7n³ + 11n² + 30 → n³

Types of Asymptotic Notation

### 1\. Big-O (O-notation) – Worst Case

Represents an upper bound on time/memory.  
Answers the question: **“What is the maximum amount of resources the algorithm might need?”**

**Example:**  
A linear search in a list has O(n), because in the worst case it must check all elements.

👉 It is the most widely used notation in the industry, since it provides performance guarantees.

### 2\. Omega (Ω-notation) – Best Case

Represents a lower bound.  
Answers the question: **“What is the minimum amount of resources required?”**

**Example:**  
In linear search, the best case is finding the element in the very first position → Ω(1).

### 3\. Theta (Θ-notation) – Average Case

*   Represents situations where best case and worst case grow in the same way.  
    Provides a **tight bound** for growth.

**Example:**  
Sorting a list using Merge Sort always takes Θ(n log n), regardless of the input.



Examples

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

**Best case:** Ω(1) (if the element is at the first position).

**Average case:** Θ(n) (if the element is in the middle).

**Worst case:** O(n) (if the element is not in the list or is at the last position or after middle).

Big-O Chart Example
![Alt Text][my-image-ref]

[my-image-ref]: images/bigOComplexityChart.webp "Optional Title"

This shows how, as input size increases, the computational cost grows very differently depending on the algorithm.

Conclusion

Asymptotic Notation is one of the most important tools in computer science, as it allows comparing algorithms without depending on hardware or specific implementations.

By understanding O, Ω, and Θ, you can better predict how your code behaves for different inputs and choose more efficient solutions.

In the end, mastering algorithmic complexity is essential to writing code that not only works but is also **scalable and ready for the real world**.
