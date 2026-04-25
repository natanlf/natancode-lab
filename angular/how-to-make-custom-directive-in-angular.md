---
title: "How to make custom directive in Angular?"
slug: how-to-make-custom-directive-in-angular
date: 2024-07-24T00:36:59
categories: ["Angular", "CSS", "Flexbox", "Javascript", "Typescript"]
tags: ["Angular", "Typescript"]
---
Before creating a custom directive, we need to understand what a directive is in Angular.

"Directives are classes that add additional behavior to elements in your Angular applications."

"Use Angular's built-in directives to manage forms, lists, styles, and what users see."

The different types of Angular directives are as follows:

Directive Types

Details

[Components](https://angular.dev/guide/components)

Used with a template. This type of directive is the most common directive type.

[Attribute directives](https://angular.dev/guide/directives#built-in-attribute-directives)

Change the appearance or behavior of an element, component, or another directive.

[Structural directives](https://angular.dev/guide/directives#built-in-structural-directives)

Change the DOM layout by adding and removing DOM elements.

[https://angular.dev/](https://angular.dev/)

* * *

Now that we know what it is, let's see how to build our own directive.

Custom Directive

The following example is a directive to zoom in on text.

* * *

![](https://media3.giphy.com/media/v1.Y2lkPTc5MGI3NjExd2I1aXEwank4aGpydXlwa3RsYTJvYmgxd2ZtNGcyYjBmY3BkeGpzMiZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/13GIgrGdslD9oQ/giphy.webp)

* * *

```
import { Directive, HostBinding, HostListener, Input, OnInit } from '@angular/core';

@Directive({
  selector: '[appZoom]',
  standalone: true
})
export class ZoomDirective implements OnInit {

  @Input() defaultSize: string = '22px';
  @Input('appZoom') zoomSize!: string;
  @HostBinding('style.fontSize') fontSize!: string;

  ngOnInit(): void {
    this.fontSize = this.defaultSize;
  }

  @HostListener('mouseenter') mouseOver(eventData: Event) {
    this.fontSize = this.zoomSize;
  }

  @HostListener('mouseleave') mouseLeave(eventData: Event) {
    this.fontSize = this.defaultSize;
  }

}
```

* * *

We need the **_@Directive_** decorator to inform Angular that this class is a directive.

When using the directive, we will provide parameters, which is why **_@Input_** exists.

**_@HostBinding_** is a decorator that marks a DOM property or an element class, style, or attribute as a host-binding property. In this case, it changes the specified style, which is fontSize.

**_@HostListener_** is a decorator that declares a DOM event to listen for and provides a handler method to run when that event occurs. In this case, the events are mouseenter and mouseleave.

How to use this directive?

Since I will be using it in the App Component and it is standalone, I need to import it as shown in the example.

app.component.ts

```
import { Component } from '@angular/core';
import { RouterOutlet } from '@angular/router';
import { ZoomDirective } from './zoom.directive';

@Component({
  selector: 'app-root',
  standalone: true,
  imports: [RouterOutlet, ZoomDirective],
  templateUrl: './app.component.html',
  styleUrl: './app.component.css'
})
export class AppComponent {
  title = 'directiveApp';
}
```

* * *

app.component.css

```
.hello {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-items: center;
    margin-top: 20px;
    height: 150px;
}
```

* * *

app.component.html

```
<div class="hello">
  <div [appZoom]="'50px'" defaultSize="40px">Hello Folks!</div>
  <div [appZoom]="'50px'">Hello Folks!</div>
</div>
```

* * *

This way we can call it in the HTML.

![](https://natancode.com/wp-content/uploads/2024/07/directive-angular.gif)

* * *

![](https://media4.giphy.com/media/v1.Y2lkPTc5MGI3NjExbnZhc3hmazJpcjJxanI5NW56cHZhMGZuNXM5c2NzOXlmdDI2aXZycCZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/3o6Mb4iDo3ne2U3M0o/giphy.webp)
