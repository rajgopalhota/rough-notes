# **Liquibase**  

## **1. Introduction to Liquibase**  
Liquibase is an open-source database version control tool that allows developers to track, manage, and automate database schema changes. It supports various databases like MySQL, PostgreSQL, Oracle, SQL Server, and more. Liquibase helps in **Continuous Integration/Continuous Deployment (CI/CD)** pipelines by enabling automated, versioned, and rollback-friendly database changes.

---

## **2. Key Features of Liquibase**
- **Version Control for Database Changes** – Tracks schema changes like a source code versioning system (Git).
- **Supports Multiple Database Types** – Works with relational databases like MySQL, PostgreSQL, Oracle, and SQL Server.
- **Flexible Change Set Formats** – Supports YAML, XML, JSON, and SQL.
- **Automatic Rollback and History Tracking** – Allows rolling back to previous states.
- **Changelog Execution Tracking** – Uses `DATABASECHANGELOG` and `DATABASECHANGELOGLOCK` tables to track applied changes.
- **Integration with DevOps & CI/CD** – Works with tools like Jenkins, Docker, Kubernetes, and Spring Boot.
- **Database Diffing & Validation** – Compares database schemas and identifies differences.

---

## **3. Liquibase Core Concepts**
| Concept | Description |
|---------|------------|
| **ChangeSet** | A single unit of change (SQL operation) that is tracked and executed by Liquibase. |
| **Changelog File** | A file (XML, YAML, JSON, SQL) that defines a set of database changes. |
| **Contexts & Labels** | Used to execute specific changes in different environments. |
| **Rollback** | Allows reverting specific changes. |
| **Preconditions** | Conditions that must be met before applying a change. |
| **Tagging** | Assigns tags to specific changesets for rollback or versioning. |

---

## **4. Liquibase Installation & Setup**
### **Using Maven**  
```xml
<dependency>
    <groupId>org.liquibase</groupId>
    <artifactId>liquibase-core</artifactId>
    <version>4.21.1</version>
</dependency>
```
### **Using Spring Boot**  
Add Liquibase to your `application.properties`:  
```properties
spring.liquibase.change-log=classpath:/db/changelog/db.changelog-master.xml
spring.liquibase.enabled=true
```

---

## **5. Changelog Formats**
### **XML Example**
```xml
<databaseChangeLog>
    <changeSet id="1" author="admin">
        <createTable tableName="users">
            <column name="id" type="int" autoIncrement="true">
                <constraints primaryKey="true" nullable="false"/>
            </column>
            <column name="name" type="varchar(255)"/>
            <column name="email" type="varchar(255)"/>
        </createTable>
    </changeSet>
</databaseChangeLog>
```
### **YAML Example**
```yaml
databaseChangeLog:
  - changeSet:
      id: 1
      author: admin
      changes:
        - createTable:
            tableName: users
            columns:
              - column:
                  name: id
                  type: int
                  autoIncrement: true
                  constraints:
                    primaryKey: true
                    nullable: false
              - column:
                  name: name
                  type: varchar(255)
              - column:
                  name: email
                  type: varchar(255)
```
### **JSON Example**
```json
{
  "databaseChangeLog": [
    {
      "changeSet": {
        "id": "1",
        "author": "admin",
        "changes": [
          {
            "createTable": {
              "tableName": "users",
              "columns": [
                {
                  "name": "id",
                  "type": "int",
                  "autoIncrement": true,
                  "constraints": {
                    "primaryKey": true,
                    "nullable": false
                  }
                },
                {
                  "name": "name",
                  "type": "varchar(255)"
                },
                {
                  "name": "email",
                  "type": "varchar(255)"
                }
              ]
            }
          }
        ]
      }
    }
  ]
}
```
### **SQL Example**
```sql
--liquibase formatted sql
--changeset admin:1
CREATE TABLE users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255),
    email VARCHAR(255)
);
```

---

## **6. Advanced Liquibase Topics**
### **6.1 Running Liquibase Commands**
```sh
liquibase update
liquibase rollbackCount 1
liquibase status
liquibase diff
```

### **6.2 Using Liquibase in CI/CD**
- **Jenkins Integration**: Run `liquibase update` in build steps.
- **GitHub Actions**: Automate Liquibase in pipelines.
- **Dockerized Liquibase**:
  ```sh
  docker run --rm liquibase/liquibase update --url=jdbc:mysql://db_host:3306/db_name --username=user --password=pass
  ```

### **6.3 Rollbacks**
#### **Rollback a Specific Changeset**
```xml
<changeSet id="1" author="admin">
    <rollback>
        <dropTable tableName="users"/>
    </rollback>
</changeSet>
```
#### **Rollback Using CLI**
```sh
liquibase rollbackCount 1
liquibase rollbackToTag v1.0
```

### **6.4 Liquibase Contexts & Labels**
- **Contexts**: Execute changes in specific environments.
```xml
<changeSet id="2" author="admin" context="dev,test">
    <addColumn tableName="users">
        <column name="status" type="varchar(10)"/>
    </addColumn>
</changeSet>
```
```sh
liquibase update --contexts=dev
```
- **Labels**: Apply changes to selective deployments.
```xml
<changeSet id="3" author="admin" labels="prod">
    <addColumn tableName="users">
        <column name="created_at" type="timestamp"/>
    </addColumn>
</changeSet>
```
```sh
liquibase update --labels=prod
```

---

## **7. Best Practices for Production**
1. **Use Version Control** – Store Liquibase changelog files in Git.
2. **Avoid SQL Directly in ChangeSets** – Use abstract change types for better portability.
3. **Always Define Rollbacks** – Prevent irreversible changes in production.
4. **Use Contexts and Labels** – Differentiate changes for dev, test, and production environments.
5. **Tag Releases** – Helps in rollback and versioning.
   ```sh
   liquibase tag v1.0
   ```
6. **Automate Database Migrations** – Integrate with CI/CD pipelines.
7. **Validate Before Applying** – Use `liquibase status` and `liquibase diff` to verify changes.
8. **Monitor DATABASECHANGELOG Table** – Ensures changes are applied correctly.

---

## **8. Common Issues & Troubleshooting**
| Issue | Solution |
|-------|----------|
| Locking Issues (`DATABASECHANGELOGLOCK` stuck) | `DELETE FROM DATABASECHANGELOGLOCK;` |
| Liquibase not detecting changes | Run `liquibase clearCheckSums` |
| Missing changelogs | Ensure correct path in `application.properties` |
| Changes applied multiple times | Ensure unique `id` for each `changeSet` |

---

## **9. Conclusion**
Liquibase is a powerful tool for managing database schema changes in a structured, automated, and version-controlled manner. Using Liquibase efficiently requires understanding changelogs, rollback strategies, best practices, and integration with DevOps pipelines.