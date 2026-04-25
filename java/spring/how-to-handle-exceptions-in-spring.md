---
title: "How to handle exceptions in Spring?"
slug: how-to-handle-exceptions-in-spring
date: 2024-11-13T22:29:52
categories: ["API", "Java", "Spring"]
tags: ["API", "Java", "Spring"]
---
Exception handling in APIs is essential to ensure clear and efficient communication between the server and client, preventing unexpected interruptions and enhancing the user experience. By properly handling exceptions, the API can return user-friendly and informative error messages, making it easier to identify and resolve issues. Additionally, this practice improves the application's security and robustness, as it helps prevent sensitive information leaks and allows failures to be managed in a controlled way, ensuring greater stability and maintainability of the API.

Let's see an example in Spring.

![](https://natancode.com/wp-content/uploads/2024/10/image.webp)

I have an API that uses Spring JPA, where I have a CRUD for Branch. Let's focus on a few classes.

* * *

```
@Data
@Entity
@EqualsAndHashCode(onlyExplicitlyIncluded = true)
public class Branch {

    @EqualsAndHashCode.Include
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

}
```

* * *

```
@Service
public class BranchService {

    private static final String MSG_BRANCH_IN_USE  =
            "Branch with id %d cannot be removed, because is in use";

    @Autowired
    private BranchRepository branchRepository;

    public List<Branch> findAll() {
        return branchRepository.findAll();
    }

    public Branch findById(Long id) {
        return branchRepository.findById(id).orElseThrow(() -> new BranchNotFoundException(id));
    }

    @Transactional
    public Branch save(Branch branch) {
        return branchRepository.save(branch);
    }

    @Transactional
    public void deleteById(Long id) {
        try {
            branchRepository.deleteById(id);
            branchRepository.flush();
        } catch (EmptyResultDataAccessException e) {
            throw new BranchNotFoundException(id);
        } catch (DataIntegrityViolationException e) {
            throw new EntityInUseException(String.format(MSG_BRANCH_IN_USE, id));
        }
    }

}
```

We can see that in the BranchService class, we are throwing some exceptions; for example, when the branch with the specified ID is not found, we throw the BranchNotFoundException. Let's take a look at how this class was created.

* * *

```
public class BranchNotFoundException extends EntityNotFoundException {

    public BranchNotFoundException(String message) {
        super(message);
    }

    public BranchNotFoundException(Long id) {
        this(String.format("Branch not found for the id %d", id));
    }
}
```

* * *

```
public class EntityNotFoundException extends RuntimeException {

    @Serial
    private static final long serialVersionUID = 1L;

    public EntityNotFoundException(String message) {
        super(message);
    }

}
```

* * *

Note that we have inheritance here: BranchNotFoundException extends EntityNotFoundException; this will make sense shortly.

* * *

```
@RestController
@RequestMapping("/v1/branches")
public class BranchController {

    @Autowired
    private BranchService branchService;

    @Autowired
    private BranchModelAssembler branchModelAssembler;

    @Autowired
    private BranchInputDisassembler branchInputDisassembler;

    @GetMapping(produces = MediaType.APPLICATION_JSON_VALUE)
    public CollectionModel<BranchModel> findAll() {
        List<Branch> branches = branchService.findAll();
        return branchModelAssembler.toCollectionModel(branches);
    }

    @GetMapping(path = "/{branchId}", produces = MediaType.APPLICATION_JSON_VALUE)
    public BranchModel findById(@PathVariable Long branchId) {
        Branch branch = branchService.findById(branchId);
        return branchModelAssembler.toModel(branch);
    }

    @PostMapping(produces = MediaType.APPLICATION_JSON_VALUE)
    @ResponseStatus(HttpStatus.CREATED)
    public BranchModel save(@Valid @RequestBody BranchInput branchInput) {
        Branch branch = branchInputDisassembler.toDomainObject(branchInput);
        branch = branchService.save(branch);
        return branchModelAssembler.toModel(branch);
    }

    @PutMapping(path = "/{branchId}")
    public BranchModel update(@PathVariable Long branchId, @RequestBody BranchInput branchInput) {
        Branch branch = branchService.findById(branchId);
        branchInputDisassembler.copyToDomainObject(branchInput, branch);
        branch = branchService.save(branch);
        return branchModelAssembler.toModel(branch);
    }

    @DeleteMapping(path = "/{branchId}")
    @ResponseStatus(HttpStatus.NO_CONTENT)
    public void deleteById(@PathVariable Long branchId) {
        branchService.deleteById(branchId);
    }
}


```

The BranchModel class has the properties id and name, and BranchModelAssembler handles conversions from the Entity (Branch class) to the model (BranchModel). So, when we make a request with an existing ID, we should receive a response with the branch; otherwise, we’ll get an HTTP 404 status. Up to this point, nothing is coded to throw this error, but as we saw in the service, we can throw the BranchNotFoundException when the ID is not found.

![](https://natancode.com/wp-content/uploads/2024/11/image.png)

* * *

![](https://natancode.com/wp-content/uploads/2024/11/image-1.png)

By default, the API throws a 500 error.

When making a request with an existing ID, we get the expected result.

![](https://natancode.com/wp-content/uploads/2024/11/image-2.png)

Another scenario where we need to handle exceptions correctly is the following.

![](https://natancode.com/wp-content/uploads/2024/11/image-5.png)

Note that the exception thrown was as follows.

![](https://natancode.com/wp-content/uploads/2024/11/image-6-1024x127.png)

We need to handle exceptions properly, so let's create a _**global ExceptionHandler**_ for this purpose. This will allow us to keep the code more organized as the API grows, making it possible to handle different types of exceptions effectively.

Let's create a class called `_**ApiExceptionHandler**_` that will use the `**_@ControllerAdvice_**` annotation. With this, we can add `ExceptionHandlers` to handle exceptions within this class. We'll also create a custom DTO to provide detailed information about the exception.

We'll extend the **_`ResponseEntityExceptionHandler`_** class, as it uses `**_@ExceptionHandler_**` and can handle various types of exceptions effectively.

![](https://natancode.com/wp-content/uploads/2024/11/image-3.png)

Additionally, we’ll create a DTO to have a custom error object. Feel free to design the DTO in whatever way you prefer.

```
@Getter
public enum ProblemType {

    ENTITY_IN_USE("/entity-in-use", "Entity in use"),
    ACCESS_DENIED("/access-denied", "Access denied"),
    INVALID_PARAMETER("/invalid-parameter", "Invalid parameter"),
    INCOMPREHENSIBLE_MESSAGE("/incomprehensible-message", "Incomprehensible message"),
    INVALID_DATA("/invalid-data", "Invalid data"),
    SYSTEM_ERROR("/system-error", "System error"),
    NOT_FOUND_RESOURCE("/resource-not-found", "Resource not found"),
    BUSINESS_ERROR("/business-error", "Business Error");

    private String title;
    private String uri;

    ProblemType(String path, String title) {
        this.uri = "http://localhost:8080" + path;
        this.title =  title;
    }

}
```

* * *

```
@JsonInclude(JsonInclude.Include.NON_NULL)
@Getter
@Builder
public class Problem {

    private Integer status;

    private String type;

    private String title;

    private String detail;

    private String userMessage;

    private OffsetDateTime timestamp;

    private List<Object> objects;

    @Getter
    @Builder
    public static class Object {

        private String name;

        private String userMessage;

    }
}
```

* * *

```
@Slf4j
@ControllerAdvice
public class ApiExceptionHandler extends ResponseEntityExceptionHandler {

    public static final String GENERIC_ERROR_MESSAGE
            = "An unexpected internal system error has occurred. " +
            "Please try again and if the problem persists, contact your system administrator.";

    @ExceptionHandler(EntityNotFoundException.class)
    public ResponseEntity<?> handleEntityNotFoundException(EntityNotFoundException e, WebRequest request) {

        HttpStatus status = HttpStatus.NOT_FOUND;
        ProblemType problemType = ProblemType.NOT_FOUND_RESOURCE;
        String detail = e.getMessage();

        Problem problem = createProblemBuilder(status, problemType, detail)
                .userMessage(GENERIC_ERROR_MESSAGE).build();

        return handleExceptionInternal(e, problem, new HttpHeaders(), status, request);
    }

    @ExceptionHandler(EntityInUseException.class)
    public ResponseEntity<?> handleEntidadeEmUsoException(EntityInUseException e, WebRequest request) {

        HttpStatus status = HttpStatus.CONFLICT;
        ProblemType problemType = ProblemType.ENTITY_IN_USE;
        String detail = e.getMessage();

        Problem problem = createProblemBuilder(status, problemType, detail)
                .userMessage(detail).build();

        return handleExceptionInternal(e, problem, new HttpHeaders(), status, request);
    }

    @Override
    protected ResponseEntity<Object> handleExceptionInternal(
            Exception ex, @Nullable Object body, HttpHeaders headers, HttpStatusCode status, WebRequest request) {

        if (body == null) {
            body = Problem.builder()
                    .title(status.toString())
                    .status(status.value())
                    .userMessage(GENERIC_ERROR_MESSAGE)
                    .timestamp(OffsetDateTime.now())
                    .build();
        }

        return super.handleExceptionInternal(ex, body, headers, status, request);
    }

    private Problem.ProblemBuilder createProblemBuilder(HttpStatus status, ProblemType problemType, String detail) {

        return Problem.builder()
                .status(status.value())
                .type(problemType.getUri())
                .title(problemType.getTitle())
                .timestamp(OffsetDateTime.now())
                .detail(detail);
    }

}
```

* * *

Now, with the Global ExceptionHandler, every time an exception of type `_**EntityNotFoundException**_` is thrown, the **_`handleEntityNotFoundException`_** method is called and constructs the error in a customized way.

![](https://natancode.com/wp-content/uploads/2024/11/image-7.png)

The **_`handleEntityInUseException`_** method is called when we throw the `_**EntityInUseException**_`, following this logic, we can handle various types of exceptions. Meanwhile, the **_`handleExceptionInternal`_** method is generic; if there is no specific `ExceptionHandler` for a particular exception, it will handle it. This often applies to cases like a 415 error.

![](https://natancode.com/wp-content/uploads/2024/11/image-8.png)

* * *

![](https://natancode.com/wp-content/uploads/2023/10/image-1.gif)
