# **Dependency Injection (DI) & Inversion of Control (IoC) in Spring Boot**

## **1. Dependency Injection (DI)**
**Definition:**  
Dependency Injection is a design pattern where Spring injects dependencies into objects rather than objects creating them manually.

### **Types of Dependency Injection**
1. **Constructor Injection (Recommended)**
2. **Setter Injection**
3. **Field Injection (Not recommended due to testing issues)**

### **Example - Constructor Injection (Best Practice)**
```java
@Component
public class Car {
    private final Engine engine;

    @Autowired  // Optional in Spring Boot
    public Car(Engine engine) {
        this.engine = engine;
    }

    public void start() {
        engine.run();
    }
}

@Component
public class Engine {
    public void run() {
        System.out.println("Engine is running...");
    }
}
```

### **Example - Setter Injection**
```java
@Component
public class Car {
    private Engine engine;

    @Autowired
    public void setEngine(Engine engine) {
        this.engine = engine;
    }
}
```

### **Example - Field Injection (Not Recommended)**
```java
@Component
public class Car {
    @Autowired
    private Engine engine;
}
```

---

## **2. Inversion of Control (IoC)**
**Definition:**  
IoC is a design principle where the control of object creation and lifecycle is transferred to the Spring container.

### **Example - IoC in Action**
Spring Boot automatically manages beans using `@ComponentScan`:
```java
@SpringBootApplication
public class MyApp {
    public static void main(String[] args) {
        ApplicationContext context = SpringApplication.run(MyApp.class, args);
        Car car = context.getBean(Car.class);
        car.start();
    }
}
```

### **IoC Use Case: Managing Multiple Implementations**
```java
@Component
public class PetrolEngine implements Engine {
    public void run() {
        System.out.println("Petrol Engine Running...");
    }
}

@Component
public class DieselEngine implements Engine {
    public void run() {
        System.out.println("Diesel Engine Running...");
    }
}

@Component
public class Car {
    private final Engine engine;

    @Autowired
    public Car(@Qualifier("petrolEngine") Engine engine) { // Choosing PetrolEngine
        this.engine = engine;
    }

    public void start() {
        engine.run();
    }
}
```

---

## **3. Use Cases of DI & IoC**
| **Use Case** | **Description** |
|-------------|---------------|
| **Service Layer** | Injecting `@Service` dependencies into `@Controller`. |
| **Repository Layer** | Injecting `@Repository` into `@Service`. |
| **Configurable Components** | Changing implementations without modifying dependent classes (e.g., `@Qualifier`). |
| **Singleton Beans** | Spring manages beans as singletons by default to optimize memory usage. |

---

## **4. Summary**
- **DI** enables loose coupling by injecting dependencies instead of creating them.
- **IoC** allows Spring to manage object creation and lifecycle.
- **Constructor Injection** is recommended for mandatory dependencies.
- **Use `@Qualifier` or `@Primary`** for multiple bean implementations.

These concepts make Spring Boot applications modular, testable, and maintainable.