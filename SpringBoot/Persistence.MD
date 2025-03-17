### **Notes on Jakarta Persistence (JPA) with Advanced Use Cases**  

## **Introduction to Jakarta Persistence (JPA)**  
Jakarta Persistence (JPA) is a Java specification for managing relational data using object-relational mapping (ORM). It allows developers to interact with databases using Java objects instead of SQL queries.  

### **Key JPA Concepts**
- **Entity** – A Java class mapped to a database table.  
- **Persistence Context** – A set of managed entity instances that track changes.  
- **EntityManager** – The interface responsible for CRUD operations.  
- **JPQL (Jakarta Persistence Query Language)** – A SQL-like query language for entities.  
- **Criteria API** – A type-safe query approach for dynamic queries.  

---

## **1. Setting Up JPA with Spring Boot**  
To enable JPA, include the following dependencies in your `pom.xml` (for Maven users):  
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <scope>runtime</scope>
</dependency>
```

### **Application Configuration (`application.yml`)**
```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/mydb
    username: myuser
    password: mypassword
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
    database-platform: org.hibernate.dialect.PostgreSQLDialect
```

---

## **2. Creating Entities and Relationships**
Let's define entities for **Users, Orders, and Products**.  

### **User Entity**
```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false)
    private String name;

    @Column(unique = true, nullable = false)
    private String email;

    @OneToMany(mappedBy = "user", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    private List<Order> orders = new ArrayList<>();

    // Getters and Setters
}
```

### **Order Entity**
```java
@Entity
@Table(name = "orders")
public class Order {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    @JoinColumn(name = "user_id", nullable = false)
    private User user;

    @OneToMany(mappedBy = "order", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    private List<OrderItem> orderItems = new ArrayList<>();

    private LocalDateTime orderDate;

    // Getters and Setters
}
```

### **OrderItem Entity**
```java
@Entity
@Table(name = "order_items")
public class OrderItem {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne
    @JoinColumn(name = "order_id", nullable = false)
    private Order order;

    @ManyToOne
    @JoinColumn(name = "product_id", nullable = false)
    private Product product;

    private int quantity;

    // Getters and Setters
}
```

### **Product Entity**
```java
@Entity
@Table(name = "products")
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private double price;

    @OneToMany(mappedBy = "product", cascade = CascadeType.ALL, fetch = FetchType.LAZY)
    private List<OrderItem> orderItems;

    // Getters and Setters
}
```

---

## **3. Typed Queries in JPA**
Typed queries ensure type safety at compile-time while executing JPQL queries.

### **Find All Users with TypedQuery**
```java
public List<User> getAllUsers(EntityManager entityManager) {
    TypedQuery<User> query = entityManager.createQuery("SELECT u FROM User u", User.class);
    return query.getResultList();
}
```

### **Find Users with Orders After a Specific Date**
```java
public List<User> findUsersWithOrdersAfter(EntityManager entityManager, LocalDateTime date) {
    TypedQuery<User> query = entityManager.createQuery(
        "SELECT DISTINCT u FROM User u JOIN u.orders o WHERE o.orderDate > :date", User.class);
    query.setParameter("date", date);
    return query.getResultList();
}
```

---

## **4. Criteria API (CriteriaBuilder)**
Criteria API is useful for building dynamic queries.

### **Find Users Who Ordered a Specific Product**
```java
public List<User> findUsersByProduct(EntityManager entityManager, String productName) {
    CriteriaBuilder cb = entityManager.getCriteriaBuilder();
    CriteriaQuery<User> query = cb.createQuery(User.class);
    Root<User> userRoot = query.from(User.class);
    Join<User, Order> orderJoin = userRoot.join("orders");
    Join<Order, OrderItem> orderItemJoin = orderJoin.join("orderItems");
    Join<OrderItem, Product> productJoin = orderItemJoin.join("product");

    query.select(userRoot)
         .distinct(true)
         .where(cb.equal(productJoin.get("name"), productName));

    return entityManager.createQuery(query).getResultList();
}
```

### **Find Total Revenue for a Product**
```java
public double getTotalRevenueForProduct(EntityManager entityManager, Long productId) {
    CriteriaBuilder cb = entityManager.getCriteriaBuilder();
    CriteriaQuery<Double> query = cb.createQuery(Double.class);
    Root<OrderItem> orderItemRoot = query.from(OrderItem.class);

    query.select(cb.sum(cb.prod(orderItemRoot.get("quantity"), orderItemRoot.get("product").get("price"))))
         .where(cb.equal(orderItemRoot.get("product").get("id"), productId));

    return entityManager.createQuery(query).getSingleResult();
}
```

---

## **5. Advanced Use Cases**
### **Use Case 1: Fetch Users with Their Latest Orders**
```java
public List<Object[]> getUsersWithLatestOrder(EntityManager entityManager) {
    String jpql = "SELECT u, o FROM User u JOIN u.orders o WHERE o.orderDate = " +
                  "(SELECT MAX(o2.orderDate) FROM Order o2 WHERE o2.user = u)";
    return entityManager.createQuery(jpql, Object[].class).getResultList();
}
```

### **Use Case 2: Find Most Sold Product**
```java
public Product getMostSoldProduct(EntityManager entityManager) {
    String jpql = "SELECT oi.product FROM OrderItem oi GROUP BY oi.product ORDER BY SUM(oi.quantity) DESC";
    return entityManager.createQuery(jpql, Product.class).setMaxResults(1).getSingleResult();
}
```

### **Use Case 3: Get Orders Along with Total Price**
```java
public List<Object[]> getOrdersWithTotalPrice(EntityManager entityManager) {
    String jpql = "SELECT o, SUM(oi.quantity * p.price) FROM Order o " +
                  "JOIN o.orderItems oi JOIN oi.product p GROUP BY o";
    return entityManager.createQuery(jpql, Object[].class).getResultList();
}
```

---

## **6. Summary**
- **Typed Queries** ensure compile-time type checking.
- **Criteria API** allows dynamic queries with type safety.
- **Advanced Queries** help in real-world scenarios like revenue calculation, trending products, and customer insights.

This guide provides expert-level insights into **Jakarta Persistence**, including **Typed Queries, CriteriaBuilder, and Advanced Queries**, covering **multi-table relationships and real-world use cases**.