## **Microservices Architecture with Spring Boot**
This guide covers:
1. **Setting up Microservices** (with Spring Boot)
2. **Service-to-Service Communication** (REST, Feign, Kafka)
3. **Database Setup** (MySQL/PostgreSQL)
4. **API Gateway & Service Discovery** (Spring Cloud Gateway, Eureka)
5. **Security & Authentication** (Spring Security, JWT)
6. **Spring Batch for Email Processing**
7. **Logging, Monitoring, and Tracing** (ELK Stack, Prometheus, Grafana)
8. **Containerization & Deployment to AWS** (Docker, Kubernetes, AWS ECS/EKS, AWS RDS, AWS Lambda)

---

# **1. Microservices Overview**
We will create the following microservices:

1. **User Service** - Manages user accounts and authentication.
2. **Order Service** - Handles orders.
3. **Payment Service** - Manages transactions.
4. **Notification Service** - Sends email notifications.
5. **Batch Email Service** - Uses Spring Batch for scheduled email processing.

## **2. Setting Up Each Microservice**
Each microservice will be a separate Spring Boot application.

### **Create a New Microservice**
```bash
mkdir microservices
cd microservices
spring init --dependencies=web,data-jpa,mysql,lombok user-service
```
Repeat this for `order-service`, `payment-service`, `notification-service`, and `batch-email-service`.

---

## **3. Implementing Microservices**

### **User Service**
- Uses **Spring Security** for authentication.
- Stores user data in **MySQL**.
- Exposes REST endpoints for user management.

**Dependencies (`pom.xml`)**
```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
    </dependency>
</dependencies>
```

**User Entity**
```java
@Entity
@Data
public class User {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String username;
    private String email;
    private String password;
}
```

**User Repository**
```java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByEmail(String email);
}
```

**User Controller**
```java
@RestController
@RequestMapping("/users")
@RequiredArgsConstructor
public class UserController {
    private final UserService userService;

    @PostMapping("/register")
    public ResponseEntity<User> register(@RequestBody User user) {
        return ResponseEntity.ok(userService.registerUser(user));
    }
}
```

**JWT Security Implementation**
- Use Spring Security and JWT to secure endpoints.

---

### **Order Service**
- Manages user orders.
- Uses **MySQL/PostgreSQL** for storing order data.

**Order Entity**
```java
@Entity
@Data
public class Order {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private Long userId;
    private String product;
    private Double amount;
}
```

**Order Controller**
```java
@RestController
@RequestMapping("/orders")
@RequiredArgsConstructor
public class OrderController {
    private final OrderService orderService;

    @PostMapping
    public ResponseEntity<Order> createOrder(@RequestBody Order order) {
        return ResponseEntity.ok(orderService.createOrder(order));
    }
}
```

---

### **Payment Service**
- Processes payments using Stripe/PayPal integration.

**Payment Controller**
```java
@RestController
@RequestMapping("/payments")
@RequiredArgsConstructor
public class PaymentController {
    private final PaymentService paymentService;

    @PostMapping
    public ResponseEntity<String> processPayment(@RequestBody PaymentRequest paymentRequest) {
        return ResponseEntity.ok(paymentService.processPayment(paymentRequest));
    }
}
```

---

### **Notification Service**
- Listens to Kafka for order/payment events.
- Sends notifications via email.

**Kafka Listener**
```java
@Component
public class NotificationListener {
    @KafkaListener(topics = "order-events", groupId = "notification-group")
    public void listen(String message) {
        System.out.println("Received Notification: " + message);
    }
}
```

---

### **Batch Email Service**
- Uses **Spring Batch** to send emails at a scheduled time.

**Batch Job Configuration**
```java
@Configuration
@EnableBatchProcessing
public class BatchConfig {
    @Bean
    public Job emailJob(JobBuilderFactory jobBuilderFactory, Step emailStep) {
        return jobBuilderFactory.get("emailJob")
                .incrementer(new RunIdIncrementer())
                .flow(emailStep)
                .end()
                .build();
    }

    @Bean
    public Step emailStep(StepBuilderFactory stepBuilderFactory, ItemReader<User> reader, ItemWriter<User> writer) {
        return stepBuilderFactory.get("emailStep")
                .<User, User>chunk(10)
                .reader(reader)
                .writer(writer)
                .build();
    }

    @Bean
    public ItemReader<User> reader(UserRepository userRepository) {
        return new RepositoryItemReaderBuilder<User>()
                .repository(userRepository)
                .methodName("findAll")
                .pageSize(10)
                .build();
    }

    @Bean
    public ItemWriter<User> writer(JavaMailSender mailSender) {
        return users -> {
            for (User user : users) {
                SimpleMailMessage mail = new SimpleMailMessage();
                mail.setTo(user.getEmail());
                mail.setSubject("Monthly Report");
                mail.setText("Hello, your monthly report is ready.");
                mailSender.send(mail);
            }
        };
    }
}
```

**Scheduled Job Execution**
```java
@Scheduled(cron = "0 0 12 * * ?")
public void triggerBatchJob() {
    jobLauncher.run(emailJob, new JobParameters());
}
```

---

## **4. Service-to-Service Communication**
- **REST API Calls**
- **Feign Clients**
- **Kafka for Async Communication**

---

## **5. API Gateway & Eureka Discovery**
- API Gateway routes requests.
- Eureka handles service discovery.

**Eureka Server**
```java
@EnableEurekaServer
@SpringBootApplication
public class EurekaServerApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }
}
```

**Eureka Client (application.yml)**
```yaml
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
```

---

## **6. Deployment to AWS**
### **Step 1: Containerizing Services**
Create `Dockerfile` for each service:
```dockerfile
FROM openjdk:17
COPY target/*.jar app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

### **Step 2: Deploy to AWS ECS**
- Push Docker images to **AWS ECR**.
- Use **AWS ECS Fargate** for deployment.

```bash
aws ecs create-cluster --cluster-name microservices-cluster
aws ecs create-service --cluster microservices-cluster --service-name user-service
```

### **Step 3: Deploy RDS**
- Create **MySQL RDS Instance**.

### **Step 4: Load Balancing & Auto-Scaling**
- Use **AWS ALB** for routing.
- Enable **Auto Scaling** in ECS.

### **Step 5: Monitoring with CloudWatch**
- Log API requests.
- Monitor CPU, memory usage.

---

## **Conclusion**
This guide provides a step-by-step expert approach to **building and deploying a Spring Boot microservices architecture on AWS**, including:
- Secure **JWT authentication**.
- **Batch processing** with **Spring Batch**.
- **Kafka-based async communication**.
- **Docker + AWS ECS** deployment.