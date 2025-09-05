
---

# 1) Project Structure

```
spring-mvc-student/
├─ pom.xml
├─ src/main/java/com/example/studentmvc/
│  ├─ StudentMvcApplication.java
│  ├─ controller/StudentController.java
│  ├─ entity/Student.java
│  ├─ repository/StudentRepository.java
│  └─ service/
│     ├─ StudentService.java
│     └─ StudentServiceImpl.java
├─ src/main/resources/
│  ├─ application.properties
│  └─ templates/
│     ├─ students_form.html
│     └─ students_list.html
└─ src/test/java/... (optional)
```

---

# 2) `pom.xml`

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.example</groupId>
  <artifactId>spring-mvc-student</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <name>spring-mvc-student</name>
  <description>Spring MVC + Hibernate (JPA) Student form</description>

  <parent>
    <!-- Any stable Spring Boot 3.x is fine -->
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.3.2</version>
    <relativePath/>
  </parent>

  <properties>
    <java.version>17</java.version> <!-- 17+ is fine; Boot 3 uses Jakarta -->
  </properties>

  <dependencies>
    <!-- Web + MVC -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Thymeleaf templates -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-thymeleaf</artifactId>
    </dependency>

    <!-- JPA (Hibernate provider under the hood) -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>

    <!-- Validation (Jakarta) -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>

    <!-- H2 (default) -->
    <dependency>
      <groupId>com.h2database</groupId>
      <artifactId>h2</artifactId>
      <scope>runtime</scope>
    </dependency>

    <!-- MySQL (optional; uncomment properties below to switch) -->
    <!--
    <dependency>
      <groupId>com.mysql</groupId>
      <artifactId>mysql-connector-j</artifactId>
      <scope>runtime</scope>
    </dependency>
    -->

    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

---

# 3) `application.properties`

```properties
# --- H2 (default) ---
spring.datasource.url=jdbc:h2:mem:studentsdb;DB_CLOSE_DELAY=-1;MODE=MySQL
spring.datasource.username=sa
spring.datasource.password=
spring.datasource.driver-class-name=org.h2.Driver

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

# Hibernate dialect is auto in Boot 3, but you can set it explicitly if you want:
# spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.H2Dialect

# Thymeleaf cache off in dev
spring.thymeleaf.cache=false

# H2 console
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

server.port=8080

# --- MySQL (optional) ---
# spring.datasource.url=jdbc:mysql://localhost:3306/studentsdb?useSSL=false&allowPublicKeyRetrieval=true&serverTimezone=UTC
# spring.datasource.username=root
# spring.datasource.password=pass123
# spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
# spring.jpa.hibernate.ddl-auto=update
# spring.jpa.show-sql=true
# spring.jpa.properties.hibernate.format_sql=true
# spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQL8Dialect
```

---

# 4) Boot Application

`src/main/java/com/example/studentmvc/StudentMvcApplication.java`

```java
package com.example.studentmvc;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class StudentMvcApplication {
    public static void main(String[] args) {
        SpringApplication.run(StudentMvcApplication.class, args);
    }
}
```

---

# 5) Entity (Hibernate/JPA)

`src/main/java/com/example/studentmvc/entity/Student.java`

```java
package com.example.studentmvc.entity;

import jakarta.persistence.*;
import jakarta.validation.constraints.*;

@Entity
@Table(name = "students")
public class Student {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY) // works fine with H2 & MySQL
    private Long id;

    @NotBlank(message = "Name is required")
    @Size(min = 2, max = 100, message = "Name length must be 2–100")
    @Column(nullable = false, length = 100)
    private String name;

    @NotBlank(message = "Email is required")
    @Email(message = "Provide a valid email")
    @Column(nullable = false, unique = true, length = 150)
    private String email;

    @NotNull(message = "Age is required")
    @Min(value = 1, message = "Age must be >= 1")
    @Max(value = 120, message = "Age must be <= 120")
    @Column(nullable = false)
    private Integer age;

    public Student() {}

    public Student(String name, String email, Integer age) {
        this.name = name;
        this.email = email;
        this.age = age;
    }

    // Getters & Setters
    public Long getId() { return id; }

    public void setId(Long id) { this.id = id; }

    public String getName() { return name; }

    public void setName(String name) { this.name = name; }

    public String getEmail() { return email; }

    public void setEmail(String email) { this.email = email; }

    public Integer getAge() { return age; }

    public void setAge(Integer age) { this.age = age; }
}
```

---

# 6) Repository

`src/main/java/com/example/studentmvc/repository/StudentRepository.java`

```java
package com.example.studentmvc.repository;

import com.example.studentmvc.entity.Student;
import org.springframework.data.jpa.repository.JpaRepository;

import java.util.Optional;

public interface StudentRepository extends JpaRepository<Student, Long> {
    Optional<Student> findByEmailIgnoreCase(String email);
}
```

---

# 7) Service Layer (optional but clean)

`src/main/java/com/example/studentmvc/service/StudentService.java`

```java
package com.example.studentmvc.service;

import com.example.studentmvc.entity.Student;

import java.util.List;

public interface StudentService {
    Student save(Student student);
    List<Student> findAll();
    boolean emailExists(String email);
}
```

`src/main/java/com/example/studentmvc/service/StudentServiceImpl.java`

```java
package com.example.studentmvc.service;

import com.example.studentmvc.entity.Student;
import com.example.studentmvc.repository.StudentRepository;
import org.springframework.stereotype.Service;
import org.springframework.transaction.annotation.Transactional;

import java.util.List;

@Service
@Transactional
public class StudentServiceImpl implements StudentService {

    private final StudentRepository repo;

    public StudentServiceImpl(StudentRepository repo) {
        this.repo = repo;
    }

    @Override
    public Student save(Student student) {
        return repo.save(student);
    }

    @Override
    @Transactional(readOnly = true)
    public List<Student> findAll() {
        return repo.findAll();
    }

    @Override
    @Transactional(readOnly = true)
    public boolean emailExists(String email) {
        return repo.findByEmailIgnoreCase(email).isPresent();
    }
}
```

---

# 8) Controller

`src/main/java/com/example/studentmvc/controller/StudentController.java`

```java
package com.example.studentmvc.controller;

import com.example.studentmvc.entity.Student;
import com.example.studentmvc.service.StudentService;
import jakarta.validation.Valid;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.*;

@Controller
@RequestMapping("/students")
public class StudentController {

    private final StudentService service;

    public StudentController(StudentService service) {
        this.service = service;
    }

    // List all students
    @GetMapping
    public String list(Model model) {
        model.addAttribute("students", service.findAll());
        return "students_list";
    }

    // Show form
    @GetMapping("/new")
    public String showForm(Model model) {
        model.addAttribute("student", new Student());
        return "students_form";
    }

    // Handle form submit
    @PostMapping
    public String save(@Valid @ModelAttribute("student") Student student,
                       BindingResult bindingResult,
                       Model model) {

        if (!bindingResult.hasFieldErrors("email") && service.emailExists(student.getEmail())) {
            bindingResult.rejectValue("email", "duplicate", "Email already exists");
        }

        if (bindingResult.hasErrors()) {
            return "students_form";
        }

        service.save(student);
        // Redirect to list to avoid resubmission on refresh
        return "redirect:/students";
    }
}
```

---

# 9) Thymeleaf Templates

`src/main/resources/templates/students_form.html`

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="UTF-8"/>
  <title>New Student</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 24px; }
    .card { max-width: 520px; padding: 24px; border: 1px solid #ddd; border-radius: 10px; }
    label { display: block; margin-top: 12px; font-weight: 600; }
    input { width: 100%; padding: 10px; margin-top: 6px; border: 1px solid #ccc; border-radius: 6px; }
    .error { color: #b30000; font-size: 0.9rem; margin-top: 4px; }
    .actions { margin-top: 18px; display: flex; gap: 10px; }
    button, a.btn { padding: 10px 16px; border: none; background: #0b66c3; color: #fff; border-radius: 6px; text-decoration: none; display: inline-block; }
    a.btn.secondary { background: #555; }
    h1 { margin-bottom: 16px; }
  </style>
</head>
<body>
  <h1>Add Student</h1>
  <div class="card">
    <form th:action="@{/students}" th:object="${student}" method="post">
      <label for="name">Name</label>
      <input id="name" type="text" th:field="*{name}" placeholder="e.g. Sachin Tendulkar"/>
      <div class="error" th:if="${#fields.hasErrors('name')}" th:errors="*{name}">Name error</div>

      <label for="email">Email</label>
      <input id="email" type="email" th:field="*{email}" placeholder="e.g. sachin@example.com"/>
      <div class="error" th:if="${#fields.hasErrors('email')}" th:errors="*{email}">Email error</div>

      <label for="age">Age</label>
      <input id="age" type="number" th:field="*{age}" min="1" max="120"/>
      <div class="error" th:if="${#fields.hasErrors('age')}" th:errors="*{age}">Age error</div>

      <div class="actions">
        <button type="submit">Save</button>
        <a class="btn secondary" th:href="@{/students}">Cancel</a>
      </div>
    </form>
  </div>
</body>
</html>
```

`src/main/resources/templates/students_list.html`

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="UTF-8"/>
  <title>Students</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 24px; }
    table { width: 100%; border-collapse: collapse; margin-top: 16px; }
    th, td { border: 1px solid #ddd; padding: 10px; }
    th { background: #f7f7f7; text-align: left; }
    a.btn { padding: 10px 16px; border: none; background: #0b66c3; color: #fff; border-radius: 6px; text-decoration: none; }
    .topbar { display:flex; justify-content: space-between; align-items:center; }
  </style>
</head>
<body>
  <div class="topbar">
    <h1>Students</h1>
    <a class="btn" th:href="@{/students/new}">+ Add Student</a>
  </div>

  <table>
    <thead>
      <tr>
        <th>#ID</th>
        <th>Name</th>
        <th>Email</th>
        <th>Age</th>
      </tr>
    </thead>
    <tbody>
      <tr th:each="s : ${students}">
        <td th:text="${s.id}">1</td>
        <td th:text="${s.name}">Name</td>
        <td th:text="${s.email}">email@example.com</td>
        <td th:text="${s.age}">21</td>
      </tr>
      <tr th:if="${#lists.isEmpty(students)}">
        <td colspan="4">No students yet. Click “Add Student”.</td>
      </tr>
    </tbody>
  </table>
</body>
</html>
```

---

# 10) How to Run

```bash
# from project root
mvn spring-boot:run
```

Open:

* **Form:** `http://localhost:8080/students/new`
* **List:** `http://localhost:8080/students`
* **H2 console (dev):** `http://localhost:8080/h2-console` (JDBC URL: `jdbc:h2:mem:studentsdb`)

---

## Switching to MySQL

1. Add the MySQL dependency in `pom.xml` (uncomment it).
2. Comment out H2 props and uncomment the MySQL block in `application.properties`.
3. Create schema `studentsdb` in MySQL.
4. Run the app; JPA will create/update the `students` table.

---


