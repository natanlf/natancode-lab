---
title: "How do @Input and @Output work in Angular?"
slug: how-do-input-and-output-work-in-angular
date: 2025-02-24T14:33:56
categories: ["Angular", "HTML", "Javascript", "Typescript"]
tags: ["Angular", "HTML", "Javascript", "Typescript"]
---
In Angular, **@Input** and **@Output** are essential decorators that facilitate **communication between components**. They allow data to be passed **from a parent to a child component (@Input)** and events to be emitted **from a child to a parent component (@Output)**.

@Input

![](https://natancode.com/wp-content/uploads/2025/02/input.png)

It allows the child component to receive values from the parent component. The parent sets the value, and the child uses it.

The parent calls the child component, sending the value. The child component receives this value, and to do so, it is necessary to use `@Input`.

@Output

![](https://natancode.com/wp-content/uploads/2025/02/output.png)

It allows the child component to send data to the parent component.

It uses `EventEmitter` to emit events that the parent can listen to. To do this, it is necessary to use `@Output` in the child component to emit the event that the parent component listens to.

Hands on

Parent

```
<div id="parent">
    <app-child [message]="'Hello from Parent!'" (notify)="onNotify($event)"></app-child>
    <p>{{ receivedMessage }}</p>
</div>
```

```
import { Component } from '@angular/core';

@Component({
  selector: 'app-parent',
  standalone: false,
  
  templateUrl: './parent.component.html',
  styleUrl: './parent.component.css'
})
export class ParentComponent {

  receivedMessage = '';

  onNotify(message: string) {
    this.receivedMessage = message;
  }
}
```

Child

```
<div id="child">
    {{ message }}
    <button (click)="sendMessage()">Send Message to Parent</button>
</div>
```

```
import { Component, EventEmitter, Input, Output } from '@angular/core';

@Component({
  selector: 'app-child',
  standalone: false,
  
  templateUrl: './child.component.html',
  styleUrl: './child.component.css'
})
export class ChildComponent {

@Input() message!: string;
@Output() notify = new EventEmitter<string>();

  sendMessage() {
    this.notify.emit("Hello from Child!")
  }

}
```

This allows for efficient communication between Angular components!

![](https://natancode.com/wp-content/uploads/2024/11/giphy.webp)
