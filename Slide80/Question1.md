

### 1) Model: `Shape` (abstract) and its subclasses

```java
// src/main/java/com/example/shapes/Shape.java
package com.example.shapes;

public abstract class Shape {
    public abstract void draw();
}
```

```java
// src/main/java/com/example/shapes/Rectangle.java
package com.example.shapes;

public class Rectangle extends Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Rectangle: ⬛ (width × height)");
    }
}
```

```java
// src/main/java/com/example/shapes/Triangle.java
package com.example.shapes;

public class Triangle extends Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Triangle: △ (base × height / 2)");
    }
}
```

```java
// src/main/java/com/example/shapes/Parallelogram.java
package com.example.shapes;

public class Parallelogram extends Shape {
    @Override
    public void draw() {
        System.out.println("Drawing a Parallelogram: ▱ (base × height)");
    }
}
```

### 2) Service using DI: `DrawShape`

```java
// src/main/java/com/example/shapes/DrawShape.java
package com.example.shapes;

public class DrawShape {

    private final Shape shape;   // dependency

    // Constructor injection (preferred)
    public DrawShape(Shape shape) {
        this.shape = shape;
    }

    public void render() {
        shape.draw();
    }
}
```

### 3) Spring XML configuration (`spring-config.xml`)

Put this on the classpath, e.g., `src/main/resources/spring-config.xml`.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
         http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- Shape options -->
    <bean id="rectangle" class="com.example.shapes.Rectangle"/>
    <bean id="triangle" class="com.example.shapes.Triangle"/>
    <bean id="parallelogram" class="com.example.shapes.Parallelogram"/>

    <!-- Choose which Shape to inject by changing the constructor-arg ref -->
    <bean id="drawShape" class="com.example.shapes.DrawShape">
        <!-- Swap 'triangle' with 'rectangle' or 'parallelogram' to change behavior -->
        <constructor-arg ref="triangle"/>
    </bean>
</beans>
```

### 4) Tiny client to run it

```java
// src/main/java/com/example/App.java
package com.example;

import org.springframework.context.support.ClassPathXmlApplicationContext;
import com.example.shapes.DrawShape;

public class App {
    public static void main(String[] args) {
        try (ClassPathXmlApplicationContext ctx =
                 new ClassPathXmlApplicationContext("spring-config.xml")) {
            DrawShape drawShape = ctx.getBean("drawShape", DrawShape.class);
            drawShape.render();
        }
    }
}
```

### ✅ What you’ll see

* With `<constructor-arg ref="triangle"/>`:

```
Drawing a Triangle: △ (base × height / 2)
```

* Change to `rectangle` or `parallelogram` in XML to switch the output—**no code changes** required (that’s the DI win).


