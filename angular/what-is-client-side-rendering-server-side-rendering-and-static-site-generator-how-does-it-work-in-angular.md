---
title: "What is Client-Side Rendering, Server-Side Rendering and Static Site Generator ? How does it work in Angular ?"
slug: what-is-client-side-rendering-server-side-rendering-and-static-site-generator-how-does-it-work-in-angular
date: 2023-11-02T21:57:47
categories: ["Angular", "Angular Universal", "Javascript", "Rendering", "Typescript"]
tags: ["Angular", "Angular Universal", "Client-Side-Rendering", "Javascript", "Server-Side_Rendering", "Static Site Generator", "Typescript"]
---
The types of rendering are crucial in a system. First and foremost, we need to understand the client's requirements, and then we can determine which rendering type is the better choice.  
All rendering types have their own set of advantages and disadvantages; let's explore each of them.

Requirements

![Requirements Angular](https://natancode.com/wp-content/uploads/2023/10/requirements.png "Requirements Angular")

You can find more here:

[https://nodejs.org/en](https://nodejs.org/en)

[https://angular.io/cli](https://angular.io/cli)

Client-Side Rendering (CSR)

It is a type of rendering found in SPA (Single Page Application), the page is rendered in the browser, which makes the website more dynamic, but SEO (Search Engine Optimization) is compromised and more hardware and internet resources are required of the client’s device compared to other types of rendering.

I created an angular application, a SPA to be more precise. Let's see the application on the browser.

[![Angular application](https://natancode.com/wp-content/uploads/2023/10/image-4.png "Angular application")](https://natancode.com/wp-content/uploads/2023/10/image-4.png)

Let’s go to the view page source. To see this option, just right-click on the page.

[![View Page Souce](https://natancode.com/wp-content/uploads/2023/10/image-5.png "View Page Souce")](https://natancode.com/wp-content/uploads/2023/10/image-5.png)

Where is the content ?

![](https://natancode.com/wp-content/uploads/2023/11/image-1.gif)

This happens because the application is running on the client side. The browser receives the files and renders the page on the client side.

[![Inspect Browser](https://natancode.com/wp-content/uploads/2023/10/image-7.png "Inspect Browser")](https://natancode.com/wp-content/uploads/2023/10/image-7.png)

Server-Side Rendering (SSR)

The page is rendered on the server, and the browser receives it. This is advantageous for SEO, as it demands less from the client's device and internet (since the page is already pre-rendered, reducing interaction time). However, it places a greater load on the server.

To implement server-side rendering for pages in Angular, you need to utilize **Angular Universal**. You can install it using the following command:

```
ng add @nguniversal/express-engine
```

To run use this command:

```
npm run dev:ssr
```

When you run the above command, it indicates that you are using a Node.js server, enabling server-side rendering. You can verify this by going to the "View Page Source" option, as previously demonstrated.

[![View Page Source](https://natancode.com/wp-content/uploads/2023/10/image-8.png "View Page Source")](https://natancode.com/wp-content/uploads/2023/10/image-8.png)

![](https://natancode.com/wp-content/uploads/2023/11/image.gif)

The rendering process takes place on the server, allowing you to view the content this time.

An additional point of interest is that Angular Universal transforms the application into a Full Stack application. In the `server.ts` file, you can create endpoints and consume them through the frontend. It's quite impressive, isn't it? Let's explore this feature next.

Static Site Generator (SSG)

The page is pre-rendered at build time. This ensures that the pages are ready and can be quickly delivered to customers when requested. Access is faster compared to Server-Side Rendering (SSR). However, if you need to make changes to the page, you will have to perform another build. SSG is particularly advantageous for SEO, demands less from the customer's device and internet, and also reduces interaction time. It is widely used for pages that do not undergo frequent changes, such as e-commerce product pages or blog posts.

To use SSG, you need to have Angular Universal, as we've set up earlier. To illustrate how it works, I've created a product module with two components: one for listing products and another for displaying product details. When you click on a product, you are directed to a product details page. It is this product details page that I want to use SSG for, as it doesn't change frequently. Let's see how it works below.

```
/products/123
/products/124
```

We have products with IDs 123 and 124, and we will use them in the specified routes to statically render the detail pages of these products at build time. To pre-render a static page, you need to run this command:

```
npm run prerender
```

After running this command, you may wonder, "Where are the product detail pages?"

[![prerender](https://natancode.com/wp-content/uploads/2023/11/image.png)](https://natancode.com/wp-content/uploads/2023/11/image.png)

It's necessary to specify the routes of products. We can specify in a file or only in a command.

```
ng run types-of-rendering-angular:prerender --routes-file routes.txt
```

or this other command:

```
ng run types-of-rendering-angular:prerender --routes /products/123 /products/124
```

The file routes.txt is in root folder and has this content:

```
/products/123
/products/124
```

types-of-rendering-angular is the name of application.

[![Static Site Generator (SSG)](https://natancode.com/wp-content/uploads/2023/11/image-1.png)](https://natancode.com/wp-content/uploads/2023/11/image-1.png)

Conclusion

All approaches have their advantages and disadvantages. It is necessary to evaluate each case to decide the best path to follow.

The code is available here: [https://github.com/natanlf/angular-types-of-rendering](https://github.com/natanlf/angular-types-of-rendering)
