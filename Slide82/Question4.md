Awesome—here’s a clean Spring Boot (Spring MVC) + JSP + JSTL app that does exactly what you asked:

It shows an `index.jsp` with a link “Show Top Series/Movies”. When clicked, the controller returns a `Model` containing an `ArrayList<String>` (10+ titles), and `result.jsp` renders them using JSTL’s `<c:forEach>`.

---

# Project structure

```
spring-mvc-jstl-topview/
├─ pom.xml
├─ src/main/java/com/example/topview/TopViewApplication.java
├─ src/main/java/com/example/topview/controller/HomeController.java
├─ src/main/resources/application.properties
└─ src/main/webapp/WEB-INF/jsp/
   ├─ index.jsp
   └─ result.jsp
```

# pom.xml (Spring Boot 3.x + JSP + JSTL)

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" 
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>spring-mvc-jstl-topview</artifactId>
  <version>1.0.0</version>
  <packaging>war</packaging>

  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.3.2</version>
  </parent>

  <properties>
    <java.version>17</java.version>
  </properties>

  <dependencies>
    <!-- Spring MVC -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- JSP support for Tomcat (Jasper) -->
    <dependency>
      <groupId>org.apache.tomcat.embed</groupId>
      <artifactId>tomcat-embed-jasper</artifactId>
    </dependency>

    <!-- JSTL for Jakarta (Boot 3 uses jakarta.*) -->
    <dependency>
      <groupId>org.glassfish.web</groupId>
      <artifactId>jakarta.servlet.jsp.jstl</artifactId>
      <version>3.0.1</version>
    </dependency>

    <!-- Optional: compile-time only if you deploy as WAR to external container -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-tomcat</artifactId>
      <scope>provided</scope>
    </dependency>
  </dependencies>

  <build>
    <finalName>spring-mvc-jstl-topview</finalName>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

# application.properties

```properties
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp

# Optional: set a port
# server.port=8080
```

# TopViewApplication.java

```java
package com.example.topview;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class TopViewApplication {
    public static void main(String[] args) {
        SpringApplication.run(TopViewApplication.class, args);
    }
}
```

# HomeController.java

```java
package com.example.topview.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

import java.util.ArrayList;
import java.util.List;

@Controller
public class HomeController {

    @GetMapping("/")
    public String index() {
        // Renders index.jsp
        return "index";
    }

    @GetMapping("/top")
    public String top(Model model) {
        List<String> titles = new ArrayList<>();
        titles.add("Breaking Bad");
        titles.add("Stranger Things");
        titles.add("The Dark Knight");
        titles.add("Inception");
        titles.add("The Witcher");
        titles.add("Interstellar");
        titles.add("The Shawshank Redemption");
        titles.add("Game of Thrones");
        titles.add("The Godfather");
        titles.add("Oppenheimer");
        titles.add("The Boys");
        titles.add("Money Heist");

        model.addAttribute("titles", titles);
        return "result"; // renders result.jsp
    }
}
```

# index.jsp

> **Note:** With Spring Boot 3 (Jakarta), use `jakarta.tags.core` as the JSTL URI.

```jsp
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<!DOCTYPE html>
<html>
<head>
    <title>Top View – Home</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 40px; }
        a.btn { display: inline-block; padding: 10px 16px; background: #1976d2; color: #fff; text-decoration: none; border-radius: 6px; }
        a.btn:hover { background: #0d47a1; }
    </style>
</head>
<body>
    <h1>Top View</h1>
    <p>Click below to view the top series/movies list.</p>
    <a href="${pageContext.request.contextPath}/top" class="btn">Show Top Series/Movies</a>
</body>
</html>
```

# result.jsp (uses JSTL `<c:forEach>`)

```jsp
<%@ page contentType="text/html; charset=UTF-8" pageEncoding="UTF-8"%>
<%@ taglib prefix="c" uri="jakarta.tags.core" %>
<!DOCTYPE html>
<html>
<head>
    <title>Top Series / Movies</title>
    <style>
        body { font-family: Arial, sans-serif; margin: 40px; }
        ul { padding-left: 18px; }
        li { margin: 6px 0; }
        a { color: #1976d2; text-decoration: none; }
    </style>
</head>
<body>
    <h1>Top Series / Movies</h1>
    <ul>
        <c:forEach var="t" items="${titles}">
            <li>${t}</li>
        </c:forEach>
    </ul>

    <p><a href="${pageContext.request.contextPath}/">Back to Home</a></p>
</body>
</html>
```

---

## How to run

```bash
mvn clean spring-boot:run
```

Open: `http://localhost:8080/` → click **Show Top Series/Movies** → see the list rendered by JSTL.

---

### Notes / gotchas

* Spring Boot 3 uses **Jakarta** packages; hence the JSTL taglib URI is `jakarta.tags.core`.
* JSP files must live under `/src/main/webapp/WEB-INF/jsp/` to work with the configured prefix/suffix.
* If you deploy as a standalone JAR (embedded Tomcat), the given setup works. If you deploy as a WAR to an external servlet container, keep `spring-boot-starter-tomcat` as `provided`.


