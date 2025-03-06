## **Notes on Email and Batch Processing in Spring Boot**  

Spring Boot provides powerful mechanisms for **email processing** and **batch processing**, supporting **cron schedules**, **scheduler locks**, and **distributed scheduling** in multi-instance environments. Below is a detailed breakdown of expert-level concepts in **email processing** and **batch job execution** with **Spring Boot**.

---

## **1. Email Processing in Spring Boot**  
Spring Boot integrates with **JavaMailSender** and **Spring Mail** to send emails using SMTP, IMAP, or Exchange servers.

### **1.1 Dependencies**
Add the following dependency in `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```

### **1.2 Configuring Email in `application.yml`**
Configure SMTP details in `application.yml`:

```yaml
spring:
  mail:
    host: smtp.gmail.com
    port: 587
    username: your-email@gmail.com
    password: your-password
    properties:
      mail:
        smtp:
          auth: true
          starttls:
            enable: true
```

#### **Using Environment Variables for Security**
Instead of hardcoding credentials, use `application.properties`:

```properties
spring.mail.username=${MAIL_USERNAME}
spring.mail.password=${MAIL_PASSWORD}
```

Then, set them in your environment:

```bash
export MAIL_USERNAME=your-email@gmail.com
export MAIL_PASSWORD=your-password
```

---

### **1.3 Sending Simple Emails**
Use `JavaMailSender` to send plain text emails:

```java
@Service
public class EmailService {
    @Autowired
    private JavaMailSender mailSender;

    public void sendSimpleMail(String to, String subject, String text) {
        SimpleMailMessage message = new SimpleMailMessage();
        message.setTo(to);
        message.setSubject(subject);
        message.setText(text);
        message.setFrom("your-email@gmail.com");
        mailSender.send(message);
    }
}
```

---

### **1.4 Sending HTML Emails**
For richer emails, use `MimeMessageHelper`:

```java
public void sendHtmlMail(String to, String subject, String htmlContent) throws MessagingException {
    MimeMessage message = mailSender.createMimeMessage();
    MimeMessageHelper helper = new MimeMessageHelper(message, true);
    
    helper.setTo(to);
    helper.setSubject(subject);
    helper.setText(htmlContent, true);
    helper.setFrom("your-email@gmail.com");

    mailSender.send(message);
}
```

---

### **1.5 Sending Emails with Attachments**
To send an email with attachments:

```java
public void sendEmailWithAttachment(String to, String subject, String text, File file) throws MessagingException {
    MimeMessage message = mailSender.createMimeMessage();
    MimeMessageHelper helper = new MimeMessageHelper(message, true);

    helper.setTo(to);
    helper.setSubject(subject);
    helper.setText(text);
    helper.setFrom("your-email@gmail.com");

    FileSystemResource resource = new FileSystemResource(file);
    helper.addAttachment(file.getName(), resource);

    mailSender.send(message);
}
```

---

## **2. Batch Processing in Spring Boot**
Spring Boot provides **Spring Batch**, a robust batch processing framework.

### **2.1 Adding Dependencies**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-batch</artifactId>
</dependency>
```

---

### **2.2 Spring Batch Architecture**
Spring Batch has three key components:
1. **Job** – The entire batch process.
2. **Step** – A part of a job (e.g., read data, process it, write to DB).
3. **ItemReader, ItemProcessor, ItemWriter** – Read, process, and write data.

---

### **2.3 Defining a Simple Batch Job**
#### **Step 1: Create a Job Configuration**
```java
@Configuration
@EnableBatchProcessing
public class BatchConfig {

    @Autowired
    private JobBuilderFactory jobBuilderFactory;

    @Autowired
    private StepBuilderFactory stepBuilderFactory;

    @Bean
    public Job sampleJob(Step sampleStep) {
        return jobBuilderFactory.get("sampleJob")
                .incrementer(new RunIdIncrementer())
                .flow(sampleStep)
                .end()
                .build();
    }

    @Bean
    public Step sampleStep(ItemReader<String> reader, ItemProcessor<String, String> processor, ItemWriter<String> writer) {
        return stepBuilderFactory.get("sampleStep")
                .<String, String>chunk(10)
                .reader(reader)
                .processor(processor)
                .writer(writer)
                .build();
    }

    @Bean
    public ItemReader<String> reader() {
        return new ListItemReader<>(List.of("Item1", "Item2", "Item3"));
    }

    @Bean
    public ItemProcessor<String, String> processor() {
        return item -> "Processed: " + item;
    }

    @Bean
    public ItemWriter<String> writer() {
        return items -> items.forEach(System.out::println);
    }
}
```

---

## **3. Scheduling Jobs (Cron Schedules)**
Spring provides `@Scheduled` for periodic job execution.

### **3.1 Using Fixed Delay & Fixed Rate**
```java
@Component
public class ScheduledTasks {

    @Scheduled(fixedRate = 5000)
    public void runEvery5Seconds() {
        System.out.println("Running every 5 seconds...");
    }

    @Scheduled(fixedDelay = 5000)
    public void runAfterLastCompletion() {
        System.out.println("Running 5 seconds after the last completion...");
    }
}
```

### **3.2 Using Cron Expressions**
```java
@Scheduled(cron = "0 0 12 * * ?")  // Runs daily at 12 PM
public void runDaily() {
    System.out.println("Running scheduled task at 12 PM...");
}
```

### **3.3 Understanding Cron Syntax**
| Field       | Allowed Values          | Example |
|------------|-------------------------|---------|
| Seconds    | 0-59                     | `0` |
| Minutes    | 0-59                     | `30` |
| Hours      | 0-23                     | `12` |
| Day of Month | 1-31                   | `*` |
| Month      | 1-12 or JAN-DEC          | `*` |
| Day of Week | 0-7 or SUN-SAT          | `?` |

Example: `"0 0/10 * * * *"` → Runs every **10 minutes**.

---

## **4. Handling Scheduler Locks in Multi-Instance Environments**
In microservices, multiple instances might run the same scheduled task, leading to **duplicate execution**. To prevent this, we use **distributed locks**.

### **4.1 Using ShedLock for Distributed Locking**
Add dependency:

```xml
<dependency>
    <groupId>net.javacrumbs.shedlock</groupId>
    <artifactId>shedlock-spring</artifactId>
    <version>5.4.0</version>
</dependency>
```

Enable ShedLock in Spring Boot:

```java
@EnableScheduling
@EnableSchedulerLock(defaultLockAtMostFor = "PT30S")
@Configuration
public class SchedulerConfig {
}
```

Modify the scheduled method:

```java
@Scheduled(cron = "0 0 * * * *")
@SchedulerLock(name = "uniqueJobName", lockAtMostFor = "PT5M")
public void lockedJob() {
    System.out.println("Executing job with lock...");
}
```

### **4.2 Lock Storage Using MySQL**
Configure MySQL lock storage:

```yaml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/scheduler_db
    username: root
    password: password
```

Create a lock table:
```sql
CREATE TABLE shedlock (
    name VARCHAR(64) NOT NULL,
    lock_until TIMESTAMP NOT NULL,
    locked_at TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
    locked_by VARCHAR(255) NOT NULL,
    PRIMARY KEY (name)
);
```

---

## **Conclusion**
- **Email Processing**: Use `JavaMailSender` for sending simple, HTML, and attachment-based emails.
- **Batch Processing**: Implement `Spring Batch` for handling large-scale processing.
- **Scheduling**: Use `@Scheduled` with cron jobs.
- **Scheduler Locking**: Prevent duplicate execution using `ShedLock` in multi-instance environments.

This setup ensures a **robust**, **scalable**, and **highly available** system for email and batch job execution in Spring Boot.