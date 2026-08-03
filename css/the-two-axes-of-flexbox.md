---
title: "The two Axes of Flexbox"
slug: the-two-axes-of-flexbox
date: 2023-11-13T00:39:01
categories: ["CSS", "Flexbox", "HTML"]
---
Flexbox is widely used nowadays, as it is possible to create complex and flexible layouts in a simple way. Before Flexbox existed, it was more complex to create layouts.

Introduction

We have two directions: **_row_** and _**column**_. We can specify the direction using the `flex-direction` property. There are two axes: the **main axis** and the **cross axis**. I'll illustrate this with an example to make it easier to understand.

![](https://natancode.com/wp-content/uploads/2023/11/image-8.gif)

For example if I set **_flex-direction: row_**, the **main axis** will be the **row**.

![](https://natancode.com/wp-content/uploads/2023/11/Main-axis-row.png)

Therefore the **cross axis** will be **column**.

![](https://natancode.com/wp-content/uploads/2023/11/Cross-axis-row.png)

Let's do some examples.

Practice

I created a container with 3 boxes inside it.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flex</title>
    <link rel="stylesheet" href="styles/style.css">
</head>
<body>
    <main>
        <div class="container">
            <div class="box">Box 1</div>
            <div class="box">Box 2</div>
            <div class="box">Box 3</div>
        </div>
    </main>
</body>
</html>
```

This html references a css file with the styles.

```
.container {
    display: flex;
    flex-direction: row;
    width: 600px;
    height: 400px;
    background-color: 	#4682B4;
    border: 2px solid black;
}

.box {
    width: 150px;
    height: 150px;
    background-color: #4169E1;
    border: 1px solid black;
    color: white;
}
```

This is the result:

![](https://natancode.com/wp-content/uploads/2023/11/imagem_2023-11-11_183805323.png)

The boxes are too close together, and I'd like to add space between them and the edges. How can I achieve this?

![](https://natancode.com/wp-content/uploads/2023/11/image-9.gif)

Now, I'll apply the following code to the container: `justify-content: space-evenly;`.

```
.container {
    display: flex;
    flex-direction: row;
    width: 600px;
    height: 400px;
    background-color: 	#4682B4;
    border: 2px solid black;
    justify-content: space-evenly;
}
```

![justify-content: space-evenly](https://natancode.com/wp-content/uploads/2023/11/image-4.png)

The `justify-content` property affects changes along the **main axis**, which, in this case, is the **row**.

With `space-evenly`, items are distributed evenly, with equal gaps at the start, in-between, and end.

However, this doesn't provide the desired result. I want to center the items. To achieve this, we need to apply changes vertically, using the `align-items` property. This property is responsible for making adjustments along the **cross-axis.**

```
.container {
    display: flex;
    flex-direction: row;
    width: 600px;
    height: 400px;
    background-color: 	#4682B4;
    border: 2px solid black;
    justify-content: space-evenly;
    align-items: center;
}
```

![](https://natancode.com/wp-content/uploads/2023/11/imagem_2023-11-11_191238660.png)

Conclusion

Understanding the axes in flexbox is crucial—a fundamental and essential concept. There are numerous properties available for use with flexbox.

You can find more here: [https://developer.mozilla.org/enUS/docs/Learn/CSS/CSS\_layout/Flexbox](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Flexbox)
