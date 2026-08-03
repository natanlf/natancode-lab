---
title: "How does work Deferrable Views in Angular 17 ?"
slug: how-does-work-deferrable-views-in-angular-17
date: 2023-12-07T23:08:51
categories: ["Angular", "Angular 17", "HTML", "Javascript", "Typescript"]
---
In previous versions of Angular, to use **lazy loading** it is necessary to specify modules in the routes using loadChildren.  
This behavior can be observed in the documentation itself.

Below is code taken from the documentation.

```
const routes: Routes = [
  {
    path: 'items',
    loadChildren: () => import('./items/items.module').then(m => m.ItemsModule)
  }
];
```

In the new version of **Angular 17** we can use **Deferrable Views**, so we can do **lazy loading**.

![](https://natancode.com/wp-content/uploads/2023/11/image-8.gif)

Practice

Let's see **@defer block**.

```

@defer {
  <app-calculation-component />
}
```

I created a component and used it in **@defer block**.

![](https://natancode.com/wp-content/uploads/2023/12/image.png)

The content of the main **@defer block** is the section of content that is **lazily loaded**.

There are some interesting features that can be used like **@placeholder**. "By default, defer blocks do not render any content before they are triggered. The **@placeholder** is an optional block that declares content to show before the defer block is triggered". This way I can show content before the defer block renders the content inside the defer block.

```
@defer {
  <app-calculation-component />
}   @placeholder (minimum 1000ms) {
  <p>Placeholder content</p>
}
```

In the code above we have the **@defer block** and the **@placeholder block**. Minimum is an optional parameter that informs the minimum time that the placeholder should be displayed.

![](https://natancode.com/wp-content/uploads/2023/12/defer-placeholder.gif)

Triggers

When the **@defer block** is triggered, this replaces the content of the placeholder, we can specify trigger conditions with **on** and **when**, let's see an example of each.

```
@defer (on hover) {
  <app-calculation-component />
} @placeholder {
  <p>Placeholder hover</p>
}
```

In this example the placeholder is displayed until the hover event happens, when the hover happens the content of the **@defer block** is displayed.

![](https://natancode.com/wp-content/uploads/2023/12/defer-placeholder-hover.gif)

Let's see an example with when.

```
@defer (when false) {
  <app-calculation-component />
} @placeholder {
  <p>Placeholder when</p>
}
```

![](https://natancode.com/wp-content/uploads/2023/12/image-1.png)

As the condition is false, it does not show the contents of the @defer block.

```
@defer (when true) {
  <app-calculation-component />
} @placeholder {
  <p>Placeholder when</p>
}
```

![](https://natancode.com/wp-content/uploads/2023/12/image-2.png)

As the condition is true, the contents of the **@defer block** are displayed.

It is also possible to use **on** and **when** together.

Conclusion

There is more to see about **@defer block**, these were just a few examples to show the power of this new functionality. It's simple and very useful, compared to previous versions of Angular, we now have more flexibility. Don't worry, the old way still works. You can find more in the official Angular documentation which has much more details and explanations:  
[https://angular.dev/guide/defer#why-use-deferrable-views](https://angular.dev/guide/defer#why-use-deferrable-views)
