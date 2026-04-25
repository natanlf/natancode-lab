---
title: "How to use Rest Controllers in Spring?"
slug: how-to-use-rest-controllers-in-spring
date: 2024-10-29T20:52:44
categories: ["API", "Java", "Spring"]
tags: ["API", "Java", "Spring"]
---
When creating a REST API in Spring, it is necessary to use some annotations, as this allows us to create the endpoints. All of this is necessary because we need to follow the "Richardson Maturity Model" to maintain a REST API with a good level of maturity.

If you want to learn more about the Richardson Maturity Model [click here](https://natancode.com/2024/10/21/richardson-maturity-model/).

Let's look at an example next to explain it step by step.

```
@RestController
@RequestMapping("/v1/states")
public class StateController {

    @Autowired
    private StateService stateService;

    @Autowired
    private StateModelAssembler stateModelAssembler;

    @Autowired
    private StateInputDisassembler stateInputDisassembler;

    @GetMapping
    public List<StateModel> findAll() {
        log.info("Searching all states");
        var states = stateService.findAll();
        return stateModelAssembler.toCollectionModel(states);
    }

    @GetMapping("/{stateId}")
    public StateModel findById(@PathVariable Long stateId) {
        var state = stateService.findById(stateId);
        return stateModelAssembler.toModel(state);
    }

    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public StateModel save(@Valid @RequestBody StateInput stateInput) {
        var body = stateInputDisassembler.toDomainObject(stateInput);
        var state = stateService.save(body);
        return stateModelAssembler.toModel(state);
    }

    @PutMapping("/{stateId}")
    public StateModel update(@PathVariable Long stateId, @RequestBody StateInput stateInput) {
        var state = stateService.findById(stateId);
        stateInputDisassembler.copyToDomainObject(stateInput, state);
        return stateModelAssembler.toModel(state);
    }

    @DeleteMapping("/{stateId}")
    @ResponseStatus(HttpStatus.NO_CONTENT)
    public void deleteById(@PathVariable Long stateId) {
        stateService.deleteById(stateId);
    }

}
```

*   **@RestController**: It combines the functionalities of **_@Controller_** and _**@ResponseBody**_, indicating that a class is a Controller. There's no need to add the @ResponseBody annotation to each method because, as mentioned earlier, this annotation already includes @ResponseBody, allowing it to serialize to JSON without needing to specify it;

*   **@RequestMapping**: It maps the URL path **_"/v1/states"_** that the controller will handle. All requests with the specified URL will be routed to this controller.

*   **@Autowired**: It is used to perform Dependency Injection in this class, allowing us to use other services;

*   **@GetMapping**: It specifies that the _**HTTP GET**_ request to the URL _**"/v1/states"**_ will be handled by the findAll method, which is used to retrieve a list of resources;

*   **@GetMapping("/{stateId}")**: It is similar to the previous one, but the difference is that to use the findById method to retrieve a specific resource, it is necessary to specify an ID in the URL itself, as shown in the example: /v1/states/{stateId};

*   **@PathVariable**: Represents the {_**stateId**_} informed in **_@GetMapping("/{stateId}")_**, to be able to obtain this value, we need this annotation.

*   **@PostMapping**: It specifies that the **_HTTP POST_** request to the URL _**"/v1/states**_" will be handled here by the save method, which is used to create a new resource;

*   **@Valid**: It is used to validate the fields in the StateInput object and returns an error if the validation fails;

*   **@RequestBody**: It converts the JSON provided in the request body into a Java object;

*   **@ResponseStatus(HttpStatus.CREATED)**: It defines the return code; when a resource is created successfully, **_HTTP 201 (Created)_** is returned;

*   **@PutMapping("/{stateId}")**: It specifies that the **_HTTP PUT_** request to the URL _**"/v1/states**_" will be handled here by the update method, which is used to update a resource;

*   **@DeleteMapping("/{stateId}")**: It is used to delete a resource, utilized in the deleteById method when making a request with the _**HTTP DELETE**_ request to the URL _**"/v1/states/{stateId}"**_;

*   **@ResponseStatus(HttpStatus.NO\_CONTENT)**: It defines that the HTTP return code will be _**204 (No Content)**_ when a resource is successfully deleted.

![](https://natancode.com/wp-content/uploads/2023/11/image-6.gif)
