

---

## 1. Project Structure

```
springboot-helloworld/
 ├─ src/main/java/
 │   └─ com/example/demo/
 │        ├─ DemoApplication.java
 │        └─ HelloController.java
 ├─ src/main/resources/
 │   └─ templates/hello.html   (using Thymeleaf)
 └─ pom.xml
```

---

## 2. `pom.xml`

We’ll use **Spring Boot Starter Web + Thymeleaf**.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>springboot-helloworld</artifactId>
    <version>1.0-SNAPSHOT</version>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.3.2</version>
    </parent>

    <dependencies>
        <!-- Spring Web (MVC + REST) -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <!-- Thymeleaf template engine -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>

        <!-- Test (optional) -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <properties>
        <java.version>17</java.version>
    </properties>
</project>
```

---

## 3. Main App Class

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```

---

## 4. Controller

```java
package com.example.demo;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HelloController {

    @GetMapping("/hello")
    public String sayHello(Model model) {
        model.addAttribute("message", "Hello World from Spring Boot MVC!");
        return "hello"; // looks for templates/hello.html
    }
}
```

---

## 5. Thymeleaf View: `src/main/resources/templates/hello.html`

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
    <title>Hello Page</title>
</head>
<body>
    <h2 th:text="${message}"></h2>
</body>
</html>
```

---

## 6. Run

Build and run with:

```bash
mvn spring-boot:run
```

Open in browser:

```
http://localhost:8080/hello
```

---

### ✅ Output

```
Hello World from Spring Boot MVC!
```

---

