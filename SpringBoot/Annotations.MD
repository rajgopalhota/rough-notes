# **Spring Boot & Testing Annotations - Complete Summary**  

The table below provides a **big picture** of Spring Boot and testing annotations, including their **use cases, alternatives, and better fits**.

---

## **1. Spring Boot Core Annotations**
| **Annotation** | **Use Case** | **Better Fit / Alternative** | **Notes** |
|--------------|------------|----------------|--------|
| `@SpringBootApplication` | Marks the main class and enables auto-configuration, component scanning, and configuration properties. | `@Configuration`, `@EnableAutoConfiguration`, `@ComponentScan` (individually) | Combines multiple annotations into one. |
| `@Component` | Marks a general Spring-managed component. | `@Service`, `@Repository`, `@Controller` | Use more specific annotations where applicable. |
| `@Service` | Marks a service layer component. | `@Component` | Used for business logic; better clarity than `@Component`. |
| `@Repository` | Marks a data repository (DAO) and provides exception translation. | `@Component` | Recommended for data access components. |
| `@Controller` | Marks a controller in MVC. | `@RestController` (for APIs) | Used for web controllers (returns views). |
| `@RestController` | Combines `@Controller` and `@ResponseBody` for REST APIs. | `@Controller` + `@ResponseBody` | Preferred for API development. |
| `@Configuration` | Marks a class as a Spring configuration class. | `@SpringBootApplication` (in small projects) | Defines beans and configurations explicitly. |
| `@Bean` | Declares a Spring-managed bean inside a `@Configuration` class. | Component Scanning (`@Component`) | Used when manually defining beans. |
| `@Primary` | Gives priority to a bean when multiple implementations exist. | `@Qualifier` | Use `@Qualifier` for more explicit control. |
| `@Qualifier` | Specifies which bean to inject when multiple exist. | `@Primary` | Required for resolving multiple bean conflicts. |
| `@Value` | Injects values from properties files. | `@ConfigurationProperties` | Use `@ConfigurationProperties` for structured configuration. |
| `@ConfigurationProperties` | Binds external properties to a POJO. | `@Value` (for simple values) | Best for structured configuration. |
| `@Profile` | Activates beans based on the environment profile. | Environment-based configs (`application.yml`) | Used for environment-specific beans. |

---

## **2. Spring Boot Web Annotations**
| **Annotation** | **Use Case** | **Better Fit / Alternative** | **Notes** |
|--------------|------------|----------------|--------|
| `@GetMapping` | Handles HTTP GET requests. | `@RequestMapping(method = RequestMethod.GET)` | Simplifies endpoint mapping. |
| `@PostMapping` | Handles HTTP POST requests. | `@RequestMapping(method = RequestMethod.POST)` | Preferred for clarity. |
| `@PutMapping` | Handles HTTP PUT requests. | `@RequestMapping(method = RequestMethod.PUT)` | Preferred for clarity. |
| `@DeleteMapping` | Handles HTTP DELETE requests. | `@RequestMapping(method = RequestMethod.DELETE)` | Preferred for clarity. |
| `@RequestMapping` | Maps web requests to a controller method. | Specific mappings like `@GetMapping` | Use for general or multi-method mappings. |
| `@RequestParam` | Extracts query parameters from the URL. | `@PathVariable` (for path params) | Used for optional and query parameters. |
| `@PathVariable` | Extracts path variables from the URL. | `@RequestParam` (for query params) | Use for mandatory path parameters. |
| `@RequestBody` | Binds request body to a method parameter. | `@ModelAttribute` (for form data) | Used for JSON or XML input. |
| `@ResponseBody` | Converts return value into JSON or XML. | `@RestController` (combines `@Controller` + `@ResponseBody`) | Automatically applied in `@RestController`. |
| `@ExceptionHandler` | Handles exceptions in controllers. | `@ControllerAdvice` (for global handling) | Use for controller-specific error handling. |
| `@ControllerAdvice` | Global exception handling for all controllers. | `@ExceptionHandler` (for local exceptions) | Centralized error handling. |

---

## **3. Spring Boot Database & Persistence Annotations**
| **Annotation** | **Use Case** | **Better Fit / Alternative** | **Notes** |
|--------------|------------|----------------|--------|
| `@Entity` | Marks a JPA entity. | - | Represents a database table. |
| `@Table` | Customizes table name in JPA. | - | Use when entity and table names differ. |
| `@Id` | Marks a field as the primary key. | - | Required for JPA entities. |
| `@GeneratedValue` | Configures ID generation strategy. | - | Use `GenerationType.AUTO`, `IDENTITY`, `SEQUENCE`, or `TABLE`. |
| `@Column` | Customizes column properties in JPA. | - | Optional unless customization is needed. |
| `@OneToOne`, `@OneToMany`, `@ManyToOne`, `@ManyToMany` | Defines relationships between entities. | - | Used for entity associations. |
| `@Transactional` | Manages transactions at the method/class level. | Manual transaction handling | Ensures ACID properties. |

---

## **4. Spring Boot Security Annotations**
| **Annotation** | **Use Case** | **Better Fit / Alternative** | **Notes** |
|--------------|------------|----------------|--------|
| `@EnableWebSecurity` | Enables Spring Security. | Auto-configured in Spring Boot | Required for security configurations. |
| `@PreAuthorize` | Restricts access before method execution. | `@Secured` (simpler roles-based access) | Supports SpEL expressions. |
| `@PostAuthorize` | Checks access after method execution. | `@PreAuthorize` | Useful when return value is needed for checks. |
| `@Secured` | Defines role-based security. | `@PreAuthorize` (more flexible) | Simple but limited role-based access control. |

---

## **5. Spring Boot Testing Annotations**
| **Annotation** | **Use Case** | **Better Fit / Alternative** | **Notes** |
|--------------|------------|----------------|--------|
| `@SpringBootTest` | Loads the full application context for integration testing. | `@WebMvcTest`, `@DataJpaTest` (for focused testing) | Slower but covers all components. |
| `@WebMvcTest` | Loads only the web layer for testing controllers. | `@SpringBootTest` (if testing full app) | Faster than `@SpringBootTest`. |
| `@DataJpaTest` | Tests JPA repositories with an in-memory DB. | `@SpringBootTest` (for full DB testing) | Optimized for JPA testing. |
| `@MockBean` | Creates a mock Spring bean in the context. | `@Mock` (for unit tests without context) | Used in Spring tests. |
| `@Mock` | Creates a mock object. | `@Spy` (if calling real methods) | Used for unit testing without Spring context. |
| `@InjectMocks` | Injects mocks into the class under test. | Manual constructor injection | Used in unit testing with `@Mock`. |
| `@Spy` | Partially mocks a bean (calls real methods unless stubbed). | `@Mock` | Use when testing some real behavior. |
| `@Captor` | Captures arguments passed to a method. | Manual capturing with `ArgumentCaptor` | Useful for verifying method arguments. |
| `@Test` | Marks a test method in JUnit. | - | Required in JUnit 5. |
| `@BeforeEach` | Runs before each test method. | `@BeforeAll` (for setup once per class) | Initializes resources per test. |
| `@AfterEach` | Runs after each test method. | `@AfterAll` (for cleanup once per class) | Cleans up resources per test. |

---

## **Big Picture Summary**
- **Use `@SpringBootTest` for integration tests** but **prefer `@WebMvcTest` or `@DataJpaTest`** for faster execution.
- **Use `@RestController` instead of `@Controller + @ResponseBody`** for APIs.
- **Prefer `@Qualifier` over `@Primary`** for resolving multiple beans.
- **For unit tests, use `@Mock` + `@InjectMocks`** and **for Spring tests, use `@MockBean`**.
- **Use `@PreAuthorize` for security authorization** instead of `@Secured`.

This table helps in choosing the **right annotation for the right use case** while following best practices!