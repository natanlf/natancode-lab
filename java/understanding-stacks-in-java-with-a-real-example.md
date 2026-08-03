---
title: "&#x1f9e0; Understanding Stacks in Java with a Real Example"
slug: understanding-stacks-in-java-with-a-real-example
date: 2025-10-15T16:59:54
categories: ["Data Structures + Algorithms", "Java"]
tags: ["Data Structures + Algorithms", "Java"]
---
When working with algorithms, data structures like **Stacks** play a crucial role in solving problems efficiently. A **Stack** follows a simple but powerful principle: **LIFO (Last In, First Out)** — the last element you add is the first one to be removed.

Think of a stack like a pile of books: you can only remove the top book before accessing the others beneath it.

In Java, we can use the built-in class `java.util.Stack` to represent this structure.

## ⚙️ Main Stack Operations

Here are the core operations every developer should know:

*   **push(element)** → Adds an element to the top of the stack.
*   **pop()** → Removes the element from the top of the stack.
*   **peek()** → Returns (but doesn’t remove) the element at the top.
*   **isEmpty()** → Checks if the stack is empty.
*   **size()** → Returns the number of elements currently in the stack.

![](https://natancode.com/wp-content/uploads/2024/10/image.webp)

## 🧩 Real Example — Removing Adjacent Duplicates from a String

Let’s consider a problem where we need to **repeatedly remove two adjacent equal characters** in a string until no such pairs remain.

### Example 1:

**Input:**  
`abbaca`  
**Output:**  
`ca`

### Example 2:

**Input:**  
`azxxzy`  
**Output:**  
`ay`

To solve this, we can use a stack to efficiently handle character comparison and removal.

## 💻 Java Implementation

```
package exercises.stack;

import java.util.Stack;

public class RemoveDuplicates {

    public static String removeDuplicates(String text) {
        Stack<Character> stack = new Stack<>();

        for (char c : text.toCharArray()) {
            if (!stack.isEmpty() && stack.peek() == c) {
                stack.pop(); // Remove the duplicate
            } else {
                stack.push(c); // Add character to the stack
            }
        }

        char[] array = new char[stack.size()];
        for (int i = array.length - 1; i >= 0; i--) {
            array[i] = stack.pop();
        }

        return new String(array);
    }

    public static void main(String[] args) {
        String response = removeDuplicates("abbaca");
        System.out.println(response); // Output: ca
    }
}
```

## 🔍 Step-by-Step Explanation

Let’s walk through the example `"abbaca"`:

1.  Start with an empty stack.
2.  Read `'a'` → stack = `[a]`
3.  Read `'b'` → stack = `[a, b]`
4.  Read `'b'` → top element equals current, so `pop()` → stack = `[a]`
5.  Read `'a'` → top element equals current, so `pop()` → stack = `[]`
6.  Read `'c'` → stack = `[c]`
7.  Read `'a'` → stack = `[c, a]`

Now the stack holds `['c', 'a']`, which forms the string `"ca"` — the correct result.

## 🚀 Why Use a Stack Here?

A stack simplifies the logic by handling **adjacent duplicates dynamically**, in a single pass, with **O(n)** time complexity.

## 🧩 Conclusion

Stacks are one of the most versatile and elegant data structures in computer science.  
Whether you’re parsing expressions, validating parentheses, performing undo/redo actions, or solving problems like this one — **understanding stacks can make your algorithms both cleaner and faster**.

So next time you face a problem involving “last added, first removed” behavior, remember — a **Stack** might be your best friend. 💡
