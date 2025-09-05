

---

# Project layout

```
springboot-mvc-marks/
 ├─ pom.xml
 ├─ src/main/java/com/example/demo/
 │   ├─ DemoApplication.java
 │   ├─ HomeController.java
 │   ├─ MarksController.java
 │   └─ MarksForm.java
 ├─ src/main/resources/
 │   └─ application.properties
 └─ src/main/webapp/WEB-INF/jsp/
     ├─ marks-form.jsp
     └─ result.jsp
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
  <artifactId>springboot-mvc-marks</artifactId>
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
    <!-- Spring MVC -->
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- JSP support for embedded Tomcat -->
    <dependency>
      <groupId>org.apache.tomcat.embed</groupId>
      <artifactId>tomcat-embed-jasper</artifactId>
    </dependency>

    <!-- JSTL (Jakarta) for JSP/EL (optional but handy) -->
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

    <!-- Tests (optional) -->
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
server.port=8080
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

## src/main/java/com/example/demo/HomeController.java

```java
package com.example.demo;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class HomeController {

  // Redirect root to the marks form to avoid ambiguous mapping
  @GetMapping("/")
  public String home() {
    return "redirect:/marks";
  }
}
```

## src/main/java/com/example/demo/MarksForm.java

```java
package com.example.demo;

public class MarksForm {
  private Integer science;
  private Integer maths;
  private Integer english;

  public Integer getScience() { return science; }
  public void setScience(Integer science) { this.science = science; }

  public Integer getMaths() { return maths; }
  public void setMaths(Integer maths) { this.maths = maths; }

  public Integer getEnglish() { return english; }
  public void setEnglish(Integer english) { this.english = english; }
}
```

## src/main/java/com/example/demo/MarksController.java

```java
package com.example.demo;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.*;

@Controller
public class MarksController {

  // Show the form at /marks (NOT at /)
  @GetMapping("/marks")
  public String showForm(Model model) {
    model.addAttribute("marksForm", new MarksForm());
    return "marks-form"; // -> /WEB-INF/jsp/marks-form.jsp
  }

  // Handle submission, compute total, and show result
  @PostMapping("/marks")
  public String submit(@ModelAttribute("marksForm") MarksForm form, Model model) {
    int sci = form.getScience() == null ? 0 : form.getScience();
    int mat = form.getMaths()   == null ? 0 : form.getMaths();
    int eng = form.getEnglish() == null ? 0 : form.getEnglish();
    int total = sci + mat + eng;

    model.addAttribute("science", sci);
    model.addAttribute("maths", mat);
    model.addAttribute("english", eng);
    model.addAttribute("total", total);

    return "result"; // -> /WEB-INF/jsp/result.jsp
  }
}
```

---

## src/main/webapp/WEB-INF/jsp/marks-form.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<!DOCTYPE html>
<html>
<head>
  <title>Enter Marks</title>
</head>
<body>
  <h2>Enter Marks</h2>
  <form action="${pageContext.request.contextPath}/marks" method="post">
    <label>Science:</label>
    <input type="number" name="science" min="0" max="100" required /><br/><br/>

    <label>Maths:</label>
    <input type="number" name="maths"   min="0" max="100" required /><br/><br/>

    <label>English:</label>
    <input type="number" name="english" min="0" max="100" required /><br/><br/>

    <button type="submit">Submit</button>
  </form>
</body>
</html>
```

## src/main/webapp/WEB-INF/jsp/result.jsp

```jsp
<%@ page contentType="text/html;charset=UTF-8" %>
<!DOCTYPE html>
<html>
<head>
  <title>Total Marks</title>
</head>
<body>
  <h2>Total Marks</h2>

  <p>Science: ${science}</p>
  <p>Maths  : ${maths}</p>
  <p>English: ${english}</p>
  <hr/>
  <h3>Total : ${total}</h3>

  <p><a href="${pageContext.request.contextPath}/marks">Back</a></p>
</body>
</html>
```

---

## Run it

```bash
mvn spring-boot:run
```

Open `http://localhost:8080/` → you’ll be redirected to `/marks` → submit → see the total 🎉


