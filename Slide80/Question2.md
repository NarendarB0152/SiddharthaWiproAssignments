Great exercise 👌 — this is exactly what **Constructor Injection** in Spring is meant to show. Let’s build it step by step.

---

### 1. `ConstructorMessage.java`

```java
package com.example;

public class ConstructorMessage {
    private String message;  // initialized as null by default

    // Parameterized constructor for Constructor Injection
    public ConstructorMessage(String message) {
        this.message = message;
    }

    public void showMessage() {
        System.out.println("Message: " + message);
    }
}
```

---

### 2. `spring-config.xml`

Place this in `src/main/resources`.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- Bean using constructor injection -->
    <bean id="constructorMessage" class="com.example.ConstructorMessage">
        <constructor-arg value="Hello from Spring Constructor Injection!"/>
    </bean>

</beans>
```

---

### 3. Client class `App.java`

```java
package com.example;

import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {
    public static void main(String[] args) {
        try (ClassPathXmlApplicationContext ctx =
                 new ClassPathXmlApplicationContext("spring-config.xml")) {

            ConstructorMessage cm = ctx.getBean("constructorMessage", ConstructorMessage.class);
            cm.showMessage();
        }
    }
}
```

---

### ✅ Expected Console Output

```
Message: Hello from Spring Constructor Injection!
```

---

