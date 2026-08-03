---
title: "How does work Server-Side Rendering (SSR) in Angular 17 ?"
slug: how-does-work-server-side-rendering-ssr-in-angular-17
date: 2023-11-15T00:37:00
categories: ["Angular", "Angular 17", "Rendering", "SSR", "Typescript"]
tags: ["Angular", "Angular 17", "Client-Side-Rendering", "Javascript", "Server-Side_Rendering", "Typescript"]
---
**Angular 17** has undergone significant modernization, introducing new syntax, and features, and achieving better performance. Notably, they made substantial transformations. In this post, we will focus on **Server-Side Rendering (SSR)**.

SSR

Now, when creating a project, there's a straightforward option to enable SSR.

![](https://natancode.com/wp-content/uploads/2023/11/image-6.png)

To create a new project with SSR, run:

```
ng new --ssr
```

To add to an existing project, run:

```
ng add @angular/ssr
```

Let's compare the old version with the new one.

![](https://natancode.com/wp-content/uploads/2023/11/image-7.png)

In previous versions, installing **Angular Universal** was required, but not anymore. Just follow the steps listed above. To run it, simply use **_'ng serve'._** I created a project and executed the command.

![](https://natancode.com/wp-content/uploads/2023/11/image-10.gif)

![](https://natancode.com/wp-content/uploads/2023/11/image-8.png)

In "View Page Source" I can see the content.

![](https://natancode.com/wp-content/uploads/2023/11/image-11.png)

There are resources like **window** and **document** that can only be executed in the browser, not on the server. The introduction of lifecycle hooks, specifically **afterRender** and **afterNextRender**, aids in this distinction—they are exclusively executed in the browser and skipped on the server. To test this functionality, I employ a template reference.

```
<p #content>Congratulations! Your app is running. 🎉</p>
```

And I used the following code to access:

```
import { Component, ElementRef, ViewChild, afterNextRender } from '@angular/core';
import { CommonModule } from '@angular/common';
import { RouterOutlet } from '@angular/router';


@Component({
  selector: 'app-root',
  standalone: true,
  imports: [CommonModule, RouterOutlet],
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  @ViewChild('content') contentRef: ElementRef;
  title = 'ssrTest';

  constructor() {
    afterNextRender(() => {
      // Safe to check `scrollHeight` because this will only run in the browser, not the server.
      console.log('content height: ' + this.contentRef.nativeElement.scrollHeight);
    });
  }
}
```

The following result emerges:

![](https://natancode.com/wp-content/uploads/2023/11/image-12.png)

![](https://natancode.com/wp-content/uploads/2023/11/image-11.gif)

**afterRender**: Register a callback to be invoked each time the application finishes rendering.

**afterNextRender**: Register a callback to be invoked the next time the application finishes rendering.

Another interesting thing is It is possible to incorporate data caching when utilizing HttpClient.

Conclusion

This post specifically focuses on Server-Side Rendering (SSR) in Angular 17. The new lifecycles, including afterRender and afterNextRender, enable us to access resources such as document and window. The future of this framework appears promising, thanks to its innovative approach.
