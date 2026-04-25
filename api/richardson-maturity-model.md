---
title: "What is Richardson Maturity Model?"
slug: richardson-maturity-model
date: 2024-10-21T12:26:53
categories: ["API", "Java", "Quarkus", "Spring"]
tags: ["API", "Java", "Quarkus", "Spring"]
---
_It is a model (developed by Leonard Richardson) to improve API maturity_.

To achieve the glory of REST, the API must have maturity levels. There are 4 levels, and next we will see how each of them works.

![](https://natancode.com/wp-content/uploads/2024/10/overview-Richard-Maturity-Model.png)

Level 0: The Swamp of POX

This level defines that the use of the **_HTTP protocol_** is necessary to transport the information.

We have a single endpoint for all requests, using only one HTTP VERB, which in this case is POST.

Examples:

*   **_https:myapi/v1/states?action=list_**
*   **_https:myapi/v1/states?action=add_**

Level 1: Resources

Now we no longer have a single endpoint, but we are still using the POST verb for all endpoints. We use **_resources_** as in the examples:

*   _**https:myapi/v1/states/getAllStates**_
*   _**https:myapi/v1/states/getStateById**_

Level 2: HTTP Verbs

We make proper use of **_HTTP verbs_** to indicate each action.

Among the most commonly used are:

*   **GET**: responsible for retrieving one or more resources;
*   **POST**: responsible for creating a new resource;
*   **PUT**: responsible for updating a resource;
*   **DELETE**: responsible for removing a resource;
*   **PATCH**: responsible for partially updating a resource.

Examples:

![](https://natancode.com/wp-content/uploads/2024/10/Level-2-Http-Verbs.png)

Level 3: Hypermedia Controls

In this final level of maturity, we apply **_HATEOAS_** (Hypermedia as the Engine of Application State). It is responsible for showing the **_URI of the resource itself_** and **_other resources related to it_**. It's like website navigation.

Example:

```
{
  "states": [
    {
      "id": 1,
      "name": "São Paulo",
      "links": [
        {
          "rel": "self",
          "href": "http://api.example.com/v1/states/1"
        }
      ]
    },
    {
      "id": 2,
      "name": "Rio de Janeiro",
      "links": [
        {
          "rel": "self",
          "href": "http://api.example.com/v1/states/2"
        }
      ]
    }
  ],
  "links": [
    {
      "rel": "first",
      "href": "http://api.example.com/v1/states?page=1"
    },
    {
      "rel": "next",
      "href": "http://api.example.com/v1/states?page=3"
    },
    {
      "rel": "prev",
      "href": "http://api.example.com/v1/states?page=1"
    },
    {
      "rel": "last",
      "href": "http://api.example.com/v1/states?page=10"
    }
  ]
}

```

Pagination is a good example of its use, but it's not limited to that, as it returns the possible actions related to the returned resource.

![](https://natancode.com/wp-content/uploads/2024/10/image-2.webp)

This is how we can achieve the glory of Rest.

References
