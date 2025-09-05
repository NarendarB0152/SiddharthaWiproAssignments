
# Project structure

```
spring-mvc-login/
 ├─ pom.xml
 ├─ src/main/java/com/example/login/
 │   ├─ LoginApplication.java
 │   └─ controller/LoginController.java
 └─ src/main/resources/
     ├─ application.properties
     └─ templates/
         ├─ login.html
         ├─ success.html
         └─ failure.html
```

---

### pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>spring-mvc-login</artifactId>
  <version>1.0.0</version>
  <properties>
    <java.version>21</java.version>
    <spring.boot.version>3.3.2</spring.boot.version>
  </properties>

  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-dependencies</artifactId>
        <version>${spring.boot.version}</version>
        <type>pom</type>
        <scope>import</scope>
      </dependency>
    </dependencies>
  </dependencyManagement>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-thymeleaf</artifactId>
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

### src/main/java/com/example/login/LoginApplication.java

```java
package com.example.login;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class LoginApplication {
  public static void main(String[] args) {
    SpringApplication.run(LoginApplication.class, args);
  }
}
```

---

### src/main/java/com/example/login/controller/LoginController.java

```java
package com.example.login.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.util.StringUtils;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
public class LoginController {

  private static final String VALID_USERNAME = "wipro";     // case-insensitive
  private static final String VALID_PASSWORD = "Wipro@123"; // exact match

  @GetMapping({"/", "/login"})
  public String showLogin(Model model) {
    return "login";
  }

  @PostMapping("/login")
  public String handleLogin(@RequestParam String username,
                            @RequestParam String password,
                            Model model) {

    boolean okUser = StringUtils.hasText(username) &&
                     VALID_USERNAME.equalsIgnoreCase(username.trim());
    boolean okPass = VALID_PASSWORD.equals(password);

    if (okUser && okPass) {
      model.addAttribute("user", username.trim());
      return "success";
    } else {
      model.addAttribute("error", "Invalid username or password");
      return "failure";
    }
  }
}
```

---

### src/main/resources/application.properties

```properties
spring.thymeleaf.cache=false
server.port=8080
```

---

### src/main/resources/templates/login.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="UTF-8"/>
  <title>Login</title>
  <style>
    body{font-family:system-ui,Arial,sans-serif;background:#f6f8fb;margin:0}
    .card{max-width:380px;margin:10vh auto;background:#fff;padding:28px;border-radius:16px;
          box-shadow:0 10px 25px rgba(0,0,0,.08)}
    h1{margin:0 0 16px;font-size:22px}
    label{display:block;margin:12px 0 6px;font-weight:600}
    input{width:100%;padding:10px 12px;border:1px solid #d7dbe3;border-radius:10px;font-size:15px}
    button{margin-top:16px;width:100%;padding:12px;border:0;border-radius:10px;cursor:pointer;font-weight:700}
    .primary{background:#2d6cdf;color:#fff}
    .hint{margin-top:14px;color:#6b7280;font-size:12px}
  </style>
</head>
<body>
  <div class="card">
    <h1>Login</h1>
    <form method="post" th:action="@{/login}">
      <label for="username">Username</label>
      <input id="username" name="username" placeholder="Enter username" required>

      <label for="password">Password</label>
      <input id="password" name="password" type="password" placeholder="Enter password" required>

      <button class="primary" type="submit">Sign in</button>
    </form>
    <div class="hint">
      Demo creds → username: <b>wipro</b> &nbsp; password: <b>Wipro@123</b>
    </div>
  </div>
</body>
</html>
```

---

### src/main/resources/templates/success.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="UTF-8"/>
  <title>Success</title>
  <style>
    body{font-family:system-ui,Arial,sans-serif;background:#ecfdf5}
    .box{max-width:540px;margin:14vh auto;background:#fff;border-radius:16px;
         padding:28px;box-shadow:0 10px 24px rgba(0,0,0,.06);text-align:center}
    a{display:inline-block;margin-top:18px;text-decoration:none;padding:10px 14px;border-radius:10px;background:#2d6cdf;color:#fff}
  </style>
</head>
<body>
  <div class="box">
    <h2>Login Successful 🎉</h2>
    <p th:text="'Welcome, ' + ${user} + '!'">Welcome, user!</p>
    <a th:href="@{/login}">Back to Login</a>
  </div>
</body>
</html>
```

---

### src/main/resources/templates/failure.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="UTF-8"/>
  <title>Login Failed</title>
  <style>
    body{font-family:system-ui,Arial,sans-serif;background:#fef2f2}
    .box{max-width:540px;margin:14vh auto;background:#fff;border-radius:16px;
         padding:28px;box-shadow:0 10px 24px rgba(0,0,0,.06);text-align:center}
    .error{color:#b91c1c;margin:8px 0 0}
    a{display:inline-block;margin-top:18px;text-decoration:none;padding:10px 14px;border-radius:10px;background:#2d6cdf;color:#fff}
  </style>
</head>
<body>
  <div class="box">
    <h2>Login Failed</h2>
    <p class="error" th:text="${error}">Invalid username or password</p>
    <a th:href="@{/login}">Try Again</a>
  </div>
</body>
</html>
```

---

## Run

```bash
mvn spring-boot:run
# open http://localhost:8080/login
```


