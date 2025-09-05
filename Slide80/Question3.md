

---

## 1. `SetterMessage.java`

```java
package com.example;

public class SetterMessage {
    private String message;   // initialized to null by default

    // Setter method for DI
    public void setMessage(String message) {
        this.message = message;
    }

    public void showMessage() {
        System.out.println("Message: " + message);
    }
}
```

---

## 2. `spring-config.xml`

(Place in `src/main/resources`)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- Bean with Setter Injection -->
    <bean id="setterMessage" class="com.example.SetterMessage">
        <property name="message" value="Hello from Spring Setter Injection!"/>
    </bean>

</beans>
```

---

## 3. Client Class: `App.java`

```java
package com.example;

import org.springframework.context.support.ClassPathXmlApplicationContext;

public class App {
    public static void main(String[] args) {
        try (ClassPathXmlApplicationContext ctx =
                 new ClassPathXmlApplicationContext("spring-config.xml")) {

            SetterMessage sm = ctx.getBean("setterMessage", SetterMessage.class);
            sm.showMessage();
        }
    }
}
```

---

## ✅ Expected Output

```
Message: Hello from Spring Setter Injection!
```

---

✨ This demonstrates **Spring Basics**:

* **Inversion of Control (IoC):** Object creation & wiring is done by Spring container, not manually with `new`.
* **Setter Injection:** Dependency (`message`) is injected via a setter method.

---


