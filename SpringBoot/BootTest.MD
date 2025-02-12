# **Mockito & Unit Testing in Spring Boot - Complete Documentation**

## **1. Introduction to Mockito**
Mockito is a popular Java testing framework used to create mock objects for unit testing. It allows developers to test components in isolation by simulating dependencies.

- **Why Use Mockito?**
  - Isolates the unit being tested.
  - Avoids database and network dependencies.
  - Improves test speed and reliability.
  - Provides better control over test scenarios.

---

## **2. Setting Up Mockito in Spring Boot**
### **Step 1: Add Dependencies**
Add the following dependencies in your `pom.xml` for Maven:
```xml
<dependencies>
    <!-- Spring Boot Test -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
        <exclusions>
            <exclusion>
                <groupId>org.junit.vintage</groupId>
                <artifactId>junit-vintage-engine</artifactId>
            </exclusion>
        </exclusions>
    </dependency>

    <!-- Mockito -->
    <dependency>
        <groupId>org.mockito</groupId>
        <artifactId>mockito-core</artifactId>
        <scope>test</scope>
    </dependency>

    <!-- Mockito for Spring -->
    <dependency>
        <groupId>org.mockito</groupId>
        <artifactId>mockito-junit-jupiter</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>
```

For Gradle:
```gradle
dependencies {
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
    testImplementation 'org.mockito:mockito-core'
    testImplementation 'org.mockito:mockito-junit-jupiter'
}
```

---

## **3. Mockito Annotations**
| Annotation | Description |
|------------|------------|
| `@Mock` | Creates a mock instance of a dependency. |
| `@InjectMocks` | Injects mock dependencies into the class under test. |
| `@Spy` | Creates a partial mock that calls real methods unless stubbed. |
| `@Captor` | Captures method arguments passed to mocks. |
| `@MockBean` | Creates a mock Spring bean (for integration testing). |
| `@ExtendWith(MockitoExtension.class)` | Enables Mockito in JUnit 5 tests. |

---

## **4. Testing Services with Mockito**
### **Example Service**
```java
@Service
public class UserService {
    @Autowired
    private UserRepository userRepository;

    public User getUserById(Long id) {
        return userRepository.findById(id).orElseThrow(() -> new RuntimeException("User not found"));
    }
}
```

### **Unit Test for UserService**
```java
@ExtendWith(MockitoExtension.class)
public class UserServiceTest {

    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserService userService;

    @Test
    public void testGetUserById_Success() {
        User mockUser = new User(1L, "John Doe", "john@example.com");
        when(userRepository.findById(1L)).thenReturn(Optional.of(mockUser));

        User result = userService.getUserById(1L);

        assertNotNull(result);
        assertEquals("John Doe", result.getName());
    }

    @Test
    public void testGetUserById_UserNotFound() {
        when(userRepository.findById(1L)).thenReturn(Optional.empty());

        Exception exception = assertThrows(RuntimeException.class, () -> {
            userService.getUserById(1L);
        });

        assertEquals("User not found", exception.getMessage());
    }
}
```

---

## **5. Testing Controllers with MockMvc**
### **Example Controller**
```java
@RestController
@RequestMapping("/users")
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping("/{id}")
    public ResponseEntity<User> getUser(@PathVariable Long id) {
        User user = userService.getUserById(id);
        return ResponseEntity.ok(user);
    }
}
```

### **Controller Test Using MockMvc**
```java
@WebMvcTest(UserController.class)
public class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private UserService userService;

    @Test
    public void testGetUser_Success() throws Exception {
        User mockUser = new User(1L, "John Doe", "john@example.com");
        when(userService.getUserById(1L)).thenReturn(mockUser);

        mockMvc.perform(get("/users/1"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name").value("John Doe"))
            .andExpect(jsonPath("$.email").value("john@example.com"));
    }

    @Test
    public void testGetUser_NotFound() throws Exception {
        when(userService.getUserById(1L)).thenThrow(new RuntimeException("User not found"));

        mockMvc.perform(get("/users/1"))
            .andExpect(status().isInternalServerError())
            .andExpect(content().string("User not found"));
    }
}
```

---

## **6. Using Spy for Partial Mocks**
```java
@ExtendWith(MockitoExtension.class)
public class UserServiceSpyTest {

    @Spy
    private UserService userService;

    @Mock
    private UserRepository userRepository;

    @Test
    public void testSpy() {
        doReturn(new User(1L, "Spy User", "spy@example.com")).when(userService).getUserById(1L);

        User user = userService.getUserById(1L);

        assertEquals("Spy User", user.getName());
    }
}
```

---

## **7. Capturing Arguments Using @Captor**
```java
@ExtendWith(MockitoExtension.class)
public class UserServiceCaptorTest {

    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserService userService;

    @Captor
    private ArgumentCaptor<Long> idCaptor;

    @Test
    public void testArgumentCaptor() {
        when(userRepository.findById(anyLong())).thenReturn(Optional.of(new User(1L, "John Doe", "john@example.com")));

        userService.getUserById(1L);

        verify(userRepository).findById(idCaptor.capture());
        assertEquals(1L, idCaptor.getValue());
    }
}
```

---

## **8. Code Coverage and Jacoco Integration**
To measure code coverage, use **Jacoco**.

### **Add Jacoco Plugin in `pom.xml`**
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.jacoco</groupId>
            <artifactId>jacoco-maven-plugin</artifactId>
            <version>0.8.8</version>
            <executions>
                <execution>
                    <goals>
                        <goal>prepare-agent</goal>
                    </goals>
                </execution>
                <execution>
                    <id>report</id>
                    <phase>verify</phase>
                    <goals>
                        <goal>report</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

### **Run Tests and Generate Report**
```sh
mvn clean verify
```
The report is generated at:
```
target/site/jacoco/index.html
```

---

## **9. Best Practices for Unit Testing**
- Follow **AAA (Arrange, Act, Assert)**.
- Keep tests **independent**.
- Use `@MockBean` for Spring context tests.
- Test **positive and negative scenarios**.
- Keep service and controller tests **separate**.
- Achieve at least **80% code coverage**.

---

## **10. Conclusion**
Mockito is a powerful framework for unit testing in Spring Boot. Using `@Mock`, `@InjectMocks`, and `MockMvc`, you can test services and controllers effectively while ensuring high code quality and maintainability.

This guide covers everything from basic mocks to advanced testing with argument captors, spies, and code coverage tools like Jacoco.