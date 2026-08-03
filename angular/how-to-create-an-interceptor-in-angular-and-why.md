---
title: "How to create an Interceptor in Angular and Why?"
slug: how-to-create-an-interceptor-in-angular-and-why
date: 2025-03-26T13:41:52
categories: ["Angular"]
tags: ["Angular"]
---
In Angular, an **Interceptor** is an feature that allows intercepting **HTTP requests** . This enables you to add logic such as:

*   **Automatically adding** **authentication tokens (JWT)**;
*   **Logging**;
*   **Global error handling**;
*   **Monitoring performance**.

Hands on

We can create an **Interceptor** with the following command.

```
ng generate interceptor interceptorname
```

Now, let's create an **Interceptor** to add **JWT tokens**.

```
export const authInterceptor: HttpInterceptorFn = (req, next) => {

  const accessToken = inject(AuthService).getAccessToken();

  if(accessToken) {
    const reqClone = req.clone({
      headers: req.headers.set('Authorization', `Bearer ${accessToken}`),
    });

    return next(reqClone);
  }
  
  return next(req);
};
```

This is a real example of an **Interceptor**. We have a **service** that retrieves the **token**, allowing us to **clone** the request and **add the token**.

Why not just modify the original request and add the token?

![](https://natancode.com/wp-content/uploads/2025/02/giphy.webp)

Because the **request is immutable**—we **cannot modify** it directly, which is why we need to **clone** it before adding the token.

This alone is **not enough** for it to work. We need to specify it in the **module** under the **providers** property.

In the **AppModule**, I specified the following interceptor:

```
  providers: [
    provideHttpClient(
    withInterceptors([authInterceptor, HttpErrorInterceptor])),
  ],
```

We can see the **authInterceptor** we created, along with the **HttpErrorInterceptor**. We can have **multiple interceptors**. In this specific case, we have **two**:

The **HttpErrorInterceptor**, which handles global error management.

The **authInterceptor**, which we created.

```
export const HttpErrorInterceptor: HttpInterceptorFn = (req, next) => {
    //some logic
};
```

This allows us to **implement global error handling**.

What if we are using **standalone components**? Since there is **no AppModule**, how do we register the interceptor?

![](https://natancode.com/wp-content/uploads/2023/11/image-7.gif)

To answer this question, I created a **new interceptor** in a **project without an AppModule**.

```
import { HttpInterceptorFn } from '@angular/common/http';

export const httpInterceptor: HttpInterceptorFn = (req, next) => {
  console.log("Interceptor")
  return next(req);
};
```

To register it, we need to **add it to the `main.ts` file**.

```
import { bootstrapApplication } from '@angular/platform-browser';
import { appConfig } from './app/app.config';
import { AppComponent } from './app/app.component';

bootstrapApplication(AppComponent, appConfig)
  .catch((err) => console.error(err));
```

The **appConfig** contains a **providers array**, where we need to **declare the interceptor**.

```
import { ApplicationConfig, provideZoneChangeDetection } from '@angular/core';
import { provideRouter } from '@angular/router';

import { routes } from './app.routes';
import { provideHttpClient, withInterceptors } from '@angular/common/http';
import { httpInterceptor } from './http.interceptor';

export const appConfig: ApplicationConfig = {
  providers: [
    provideZoneChangeDetection({ eventCoalescing: true }), 
    provideRouter(routes),
    provideHttpClient(withInterceptors([httpInterceptor]))
  ]
};
```

Now it's **successfully registered!**

Conclusion

**Interceptors** are a **powerful tool** in Angular for **centralizing logic** that affects all HTTP requests. With them, we can:

✔ **Automatically add authentication tokens**  
✔ **Handle errors globally**  
✔ **Monitor requests**

This helps **avoid code duplication** and **improves application maintainability**. 🚀
