# learn-spring-boot

Most Important Spring Boot Annotations + Interview Explanation
Interviewers usually check 3 things:


Do you know why annotation is used?


Do you know where it is used?


Can you explain it in real project flow?


Most people only memorize definitions. That fails in interviews.

1. @SpringBootApplication
@SpringBootApplicationpublic class DemoApplication {    public static void main(String[] args) {        SpringApplication.run(DemoApplication.class, args);    }}
Interview Explanation

@SpringBootApplication is the main entry annotation of Spring Boot.
It combines 3 annotations:


@Configuration


@EnableAutoConfiguration


@ComponentScan


It starts the Spring Boot application and automatically configures beans and packages.


Important Follow-up
What is inside @SpringBootApplication?
@Configuration@EnableAutoConfiguration@ComponentScan

2. @RestController
@RestControllerpublic class UserController {    @GetMapping("/users")    public String getUsers() {        return "Users";    }}
Interview Explanation

@RestController is used to create REST APIs.
It combines:


@Controller


@ResponseBody


@ResponseBody automatically converts Java objects into JSON response.


Real Flow
Request → Controller → Service → Repository → Database
Controller receives HTTP request.

3. @Controller
Used for returning JSP/HTML pages.
@Controllerpublic class HomeController {    @GetMapping("/")    public String home() {        return "home";    }}
Interview Difference
AnnotationPurpose@ControllerReturns View/Page@RestControllerReturns JSON/API response

4. @RequestMapping
Maps HTTP requests.
@RequestMapping("/api/users")
Interview Explanation

Used to map URL requests to controller methods or classes.


5. @GetMapping, @PostMapping, @PutMapping, @DeleteMapping
Shortcut versions of request mapping.
@GetMapping("/users")@PostMapping("/users")@PutMapping("/users/{id}")@DeleteMapping("/users/{id}")
Interview Explanation

These annotations handle specific HTTP methods.

AnnotationHTTP Method@GetMappingGET@PostMappingPOST@PutMappingPUT@DeleteMappingDELETE

6. @PathVariable
@GetMapping("/users/{id}")public String getUser(@PathVariable int id) {    return "User";}
Interview Explanation

Used to get values directly from URL path.

Example:
/users/10
Here 10 is path variable.

7. @RequestParam
@GetMapping("/users")public String getUser(@RequestParam String name) {    return name;}
URL
/users?name=pavan
Difference Between PathVariable & RequestParam
PathVariableRequestParamPart of URL pathQuery parameterMandatory usuallyOptional mostly/users/1/users?id=1

8. @RequestBody
@PostMapping("/save")public User saveUser(@RequestBody User user) {    return user;}
Interview Explanation

Converts incoming JSON request into Java object.

JSON:
{  "name":"Pavan",  "age":25}
Converted into User object.

9. @Service
@Servicepublic class UserService {}
Interview Explanation

Used in service layer where business logic is written.


10. @Repository
@Repositorypublic class UserRepository {}
Interview Explanation

Used in DAO/Repository layer for database operations.
It also handles database exception translation.


11. @Autowired
@Autowiredprivate UserService service;
Interview Explanation

Used for Dependency Injection.
Spring automatically injects required object.


Better Modern Answer
Constructor Injection is preferred.
private final UserService service;public UserController(UserService service) {    this.service = service;}
Why?


Immutable


Better testing


Avoids NullPointerException


If you still say only @Autowired, experienced interviewers may think you know old style only.

12. @Component
Generic Spring Bean.
@Componentpublic class CommonUtil {}
Interview Explanation

Used to create Spring-managed beans.


Difference
AnnotationUsage@ComponentGeneric bean@ServiceBusiness logic@RepositoryDB layer@ControllerWeb layer
All are internally components.

13. @Entity
@Entitypublic class User {}
Interview Explanation

Marks class as database table entity in JPA.


14. @Table
@Table(name="users")
Interview Explanation

Used to map entity with table name.


15. @Id
@Idprivate Long id;
Interview Explanation

Marks primary key.


16. @GeneratedValue
@GeneratedValue(strategy = GenerationType.IDENTITY)
Interview Explanation

Automatically generates primary key values.


17. @Column
@Column(name="email")
Interview Explanation

Maps class field with database column.


18. @OneToMany, @ManyToOne
Example
One user → Many orders
@OneToManyprivate List<Order> orders;
@ManyToOneprivate User user;
Interview Explanation

Used to define relationships between database tables.


19. @Bean
@Beanpublic RestTemplate restTemplate() {    return new RestTemplate();}
Interview Explanation

@Bean manually creates object and stores it in Spring IOC container.


Important Follow-up
Difference Between @Component and @Bean
@Component@BeanAuto detectedManually createdUsed on classUsed on method

20. @Configuration
@Configurationpublic class AppConfig {}
Interview Explanation

Used to define Spring configuration class.


21. @EnableCaching
@EnableCaching
Flow Explanation for Interview

First we add @EnableCaching in configuration class.
Then Spring enables cache support.
After that we use annotations like:


@Cacheable


@CachePut


@CacheEvict




22. @Cacheable
@Cacheable("users")public User getUser(Long id)
Interview Explanation

First request hits database.
Result stored in cache.
Next requests directly return from cache without DB call.


23. @CachePut
@CachePut("users")
Interview Explanation

Updates cache with latest value after method execution.


24. @CacheEvict
@CacheEvict("users")
Interview Explanation

Removes old data from cache.


25. @Transactional
@Transactional
Interview Explanation

Used for database transaction management.
If any exception occurs, transaction rolls back automatically.


Real Example
Money Transfer:


Deduct from account A


Add to account B


If second operation fails, first operation also rolls back.

26. @Value
@Value("${server.port}")private String port;
Interview Explanation

Reads values from application.properties.


27. @ExceptionHandler
@ExceptionHandler(Exception.class)
Interview Explanation

Handles exceptions globally or locally.


28. @ControllerAdvice
@ControllerAdvice
Interview Explanation

Global exception handling across application.


29. @CrossOrigin
@CrossOrigin("*")
Interview Explanation

Used to allow frontend applications from different origins/domains.


30. @Valid
public User save(@Valid @RequestBody User user)
Interview Explanation

Used for request validation.

Example:
@NotNull@Emailprivate String email;

VERY IMPORTANT INTERVIEW TIP
Do NOT explain annotations like dictionary definitions.
Bad Answer:

"@Service is used for service."

This sounds weak.
Better:

"@Service is used in service layer where business logic is written like validation, calculations, API communication, etc."


BEST INTERVIEW FLOW ANSWER
If interviewer asks:
“Explain Spring Boot Flow”
Answer like this:

Client sends HTTP request.
Request comes to Controller using @RestController.
Controller calls Service layer marked with @Service.
Service contains business logic.
Service calls Repository layer using @Repository.
Repository interacts with database using JPA entities marked with @Entity.
Response returns as JSON.

That single answer already shows architecture understanding.

High-Value Annotations Experienced Interviewers Ask
AnnotationWhy Asked@TransactionalReal project usage@BeanIOC understanding@AutowiredDependency Injection@CacheablePerformance@RestControllerAPI knowledge@EntityJPA understanding@ValidValidation@ExceptionHandlerProduction handling

Biggest Mistake Candidates Make
They memorize annotations but cannot explain:


lifecycle


real usage


flow


why needed


difference between annotations


That is where interviews are won or lost.
