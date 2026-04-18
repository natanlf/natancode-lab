---
title: "What is Data Binding in Angular?"
slug: what-is-data-binding-in-angular
date: 2024-03-07T22:24:19
categories: ["Angular", "Angular 17", "CSS", "HTML", "Javascript", "Typescript"]
tags: ["Angular", "Angular 17", "Javascript", "Typescript"]
---
It's a way of **communication** between **_TypeScript code_** and _**HTML template**_.

![](https://natancode.com/wp-content/uploads/2024/03/Databinding.jpeg)

Let's see an example below. I created a component called "user".

```
import { Component } from '@angular/core';

@Component({
  selector: 'app-user',
  templateUrl: './user.component.html',
  styleUrls: ['./user.component.css']
})
export class UserComponent {
    username: string = "";

    onResetUsername() {
      this.username = "";
    }
}
```

We have a variable that stores the value of the **_"username"_** and a method that resets this value, it's a method triggered by a **_click event_** in the **_HTML template_**.

```
<div>
    <input type="text" [(ngModel)]="username">
    <button [disabled]="username === ''" (click)="onResetUsername()">Reset</button>
</div>

<div>
    <h2>Welcome {{ username }}!</h2>
</div>
```

In the HTML template, we have examples of **_Property Binding, String Interpolation, Event Binding,_** and **_Two-Way Binding_**. Let's look at each of them.

**Property Binding**

_\[disabled\]="username === ''"_

Needs to receive a true or false value, so we have this verification. It's something that comes from the TypeScript code to the HTML template. If the value provided is true, the button is disabled.

**String Interpolation**

_{{ username }}_

Displays the value of the "username" variable.

**Event** **Binding**

_(click)="onResetUsername()"_

When clicking the button, the event occurs and calls the `onResetUsername()` method, which is defined in the TypeScript code. Thus, the event flows from the HTML template to the TypeScript code.

**Two-Way-Binding**

_\[(ngModel)\]="username"_

It's the combination of _**Property Binding**_ and _**Event Binding**_, which means it works in both directions.

Don't forget to use _FormsModule_, we need it for **_ngModel_** to work.

```
import { NgModule } from '@angular/core';
import { BrowserModule } from '@angular/platform-browser';

import { AppComponent } from './app.component';
import { UserComponent } from './user/user.component';
import { FormsModule } from '@angular/forms';

@NgModule({
  declarations: [
    AppComponent,
    UserComponent
  ],
  imports: [
    FormsModule,
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

Let's see the project running:

![](https://natancode.com/wp-content/uploads/2024/03/databinding.gif)

![](https://natancode.com/wp-content/uploads/2023/11/image-12.gif)
