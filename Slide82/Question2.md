

---

# Project layout

```
springboot-mvc-topshows/
 ├─ pom.xml
 ├─ src/main/java/com/example/demo/
 │   ├─ DemoApplication.java
 │   └─ TopController.java
 ├─ src/main/resources/
 │   └─ application.properties
 ├─ src/main/webapp/WEB-INF/jsp/
 │   ├─ index.jsp
 │   └─ result.jsp
 └─ src/test/java/com/example/demo/
     ├─ DemoApplicationTests.java
     └─ TopControllerTest.java
```

---

## pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                             http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.example</groupId>
  <artifactId>springboot-mvc-topshows</artifactId>
  <version>1.0.0</version>

  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.3.2</version>
  </parent>

  <properties>
    <java.version>21</java.version>
  </properties>

  <dependencies>
    <!-- MVC -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- JSP support for embedded Tomcat -->
    <dependency>
      <groupId>org.apache.tomcat.embed</groupId>
      <artifactId>tomcat-embed-jasper</artifactId>
    </dependency>

    <!-- JSTL (Jakarta) for EL/JSTL tags -->
    <dependency>
      <groupId>jakarta.servlet.jsp.jstl</groupId>
      <artifactId>jakarta.servlet.jsp.jstl-api</artifactId>
      <version>3.0.0</version>
    </dependency>
    <dependency>
      <groupId>org.glassfish.web</groupId>
      <artifactId>jakarta.servlet.jsp.jstl</artifactId>
      <version>3.0.1</version>
    </dependency>

    <!-- Tests (JUnit 5 + Spring Test + MockMvc) -->
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

## src/main/resources/application.properties

```properties
spring.mvc.view.prefix=/WEB-INF/jsp/
spring.mvc.view.suffix=.jsp
```

---

## src/main/java/com/example/demo/DemoApplication.java

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

## src/main/java/com/example/demo/TopController.java

```java
package com.example.demo;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

import java.util.ArrayList;
import java.util.List;

@Controller
public class TopController {

  @GetMapping("/")
  public String index() {
    return "index";
  }

  @GetMapping("/top")
  public String topTitles(Model model) {
    List<String> titles = new ArrayList<>();
    titles.add("Breaking Bad");
    titles.add("Stranger Things");
    titles.add("Game of Thrones");
    titles.add("The Boys");
    titles.add("Peaky Blinders");
    titles.add("Money Heist");
    titles.add("Dark");
    titles.add("The Crown");
    titles.add("The Mandalorian");
    titles.add("Chernobyl");
    titles.add("Better Call Saul");
    titles.add("Narcos");

    model.addAttribute("topTitles", titles);
    return "result"; // resolves to /WEB-INF/jsp/result.jsp
  }
}
```

---

## JSPs (Jakarta JSTL URI)

### src/main/webapp/WEB-INF/jsp/index.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<!DOCTYPE html>
<html>
<head>
  <title>Top Series / Movies</title>
</head>
<body>
  <h2>Welcome</h2>
  <p><a href="${pageContext.request.contextPath}/top">Show Top Series / Movies</a></p>
</body>
</html>
```

### src/main/webapp/WEB-INF/jsp/result.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<%@ taglib prefix="c" uri="jakarta.tags.core" %>
<!DOCTYPE html>
<html>
<head>
  <title>Top Series / Movies</title>
</head>
<body>
  <h2>Top Series / Movies</h2>

  <c:if test="${empty topTitles}">
    <p>No titles available.</p>
  </c:if>

  <c:if test="${not empty topTitles}">
    <ol>
      <c:forEach var="t" items="${topTitles}">
        <li>${t}</li>
      </c:forEach>
    </ol>
  </c:if>

  <p><a href="${pageContext.request.contextPath}/">Back</a></p>
</body>
</html>
```

---

## Tests

### src/test/java/com/example/demo/DemoApplicationTests.java

```java
package com.example.demo;

import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class DemoApplicationTests {
  @Test
  void contextLoads() { }
}
```

### src/test/java/com/example/demo/TopControllerTest.java

```java
package com.example.demo;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.web.servlet.MockMvc;

import static org.hamcrest.Matchers.*;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

@WebMvcTest(TopController.class)
class TopControllerTest {

  @Autowired
  MockMvc mvc;

  @Test
  void topShouldReturnResultViewWithTitles() throws Exception {
    mvc.perform(get("/top"))
       .andExpect(status().isOk())
       .andExpect(view().name("result"))
       .andExpect(model().attributeExists("topTitles"))
       .andExpect(model().attribute("topTitles", hasSize(greaterThanOrEqualTo(10))));
  }
}
```

---

## How to run

```bash
mvn spring-boot:run
```

Open: `http://localhost:8080/` → click **Show Top Series / Movies** → list renders in JSP with EL ✅


