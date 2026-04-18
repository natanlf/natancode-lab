---
title: "How does work New Control Flow Syntax in Angular 17 ?"
slug: how-does-work-new-control-flow-syntax-in-angular-17
date: 2023-11-19T19:55:08
categories: ["Angular", "Angular 17", "HTML", "Javascript", "Typescript"]
tags: ["Angular", "Angular 17", "HTML", "Javascript", "Typescript"]
---
**Angular 17** has undergone significant modernization, introducing new syntax, and features, and achieving better performance. Notably, they made substantial transformations. In this post, we will focus on **New Control Flow Syntax**.

The Angular 17 introduced the **@if**, **@for**, and **@switch** syntax. Let’s explore each one.

**@if** block conditionally

The **@if** block conditionally displays its content when its condition expression is truthy. Let's see an example:

```
<div id="if">
  <h2>If-Block</h2>
  @if (a > b) {
    {{a}} is greater than {{b}}
  } @else if (b > a) {
    {{a}} is less than {{b}}
  } @else {
    {{a}} is equal to {{b}}
  }
</div>
```

```
  a: number = 10;
  b: number = 20;
```

The result is:

![](https://natancode.com/wp-content/uploads/2023/11/image-13-300x215.png)

![](https://natancode.com/wp-content/uploads/2023/11/image-12.gif)

I copied the example below from the documentation, which illustrates the old way:

```
<div *ngIf="condition; else elseBlock">Content to render when condition is true.</div>
<ng-template #elseBlock>Content to render when condition is false.</ng-template>
```

To be honest, the new mode is easier for me to read.

**@for** block - repeaters

The **@for** repeatedly renders content of a block for each item in a collection. The collection can be represented as any JavaScript iterable but there are performance advantages of using a regular Array.

There are some variables available:

*   **$count**: Number of items in a collection;
*   **$index**: Index of current row;
*   **$first**: Whether the current row is the first row;
*   **$last**: Whether the current row is the last row;
*   **$even**: Whether the current row index is even;
*   **$odd**: Whether the current row index is odd.

Let's see an example:

```
<div id="for">
    <h2>For-Block</h2>
    @for (item of items; track item.id; let idx = $index, e = $even, o = $odd, c = $count, f = $first, l = $last) {
      <p>Item index: #{{ idx }} - Name: {{ item.name }}</p>
      <p>Even: {{ e }} - Odd: {{ o }}</p>
      <p>Count - Total items: {{ c }}</p>
      <p>First: {{ f }} - Last: {{ l }}</p>
      <hr/>
    }
</div>
```

The value of the `track` expression determines a key used to associate array items with the views in the DOM. 

```
  items = [
    {
      id: 1,
      name: "Webcam"
    },
    {
      id: 2,
      name: "Mouse"
    },
    {
      id: 3,
      name: "Keyboard"
    }
  ];
```

The result is:

![](https://natancode.com/wp-content/uploads/2023/11/image-14-276x300.png)

**@empty** block

You can use it after the **@for**; if the array is empty, it will display the content inside **@empty**, serving as a message for an empty array.

```
<div id="for">
    <h2>Empty-Block</h2>
    @for (item of items2; track item.name) {
        <li> {{ item.name }} </li>
      } @empty {
        <li> There are no items. </li>
    }
</div>
```

```
items2 = new Array;
```

The result is:

![](https://natancode.com/wp-content/uploads/2023/11/image-15-300x66.png)

**@switch** block - selection

It is similar to the JavaScript switch, but there is no need for break or return. If no **@case** matches the expression and there is no @default block, nothing is shown.

```
<div id="switch">
  <h2>Switch-Block</h2>
  @switch (month) {
    @case ('december') {
      December.
    }
    @case ('november') {
      November.
    }
    @default {
      Default case.
    }
  }
</div>
```

```
month = "november";
```

The result is:

![](https://natancode.com/wp-content/uploads/2023/11/image-16-300x213.png)

Conclusion

**Angular 17** introduced several new features, and the new syntax is not only more readable but also comes with performance improvements behind the scenes. Don't worry, though, because we can still use the old way; there's no need to migrate if you prefer the existing approach.
