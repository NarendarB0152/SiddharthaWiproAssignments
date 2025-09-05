

### 1) Model classes

```java
// src/main/java/com/example/model/Test.java
package com.example.model;

public class Test {
    private String testId;
    private String testTitle;
    private int testMarks;

    // Getters & setters
    public String getTestId() { return testId; }
    public void setTestId(String testId) { this.testId = testId; }

    public String getTestTitle() { return testTitle; }
    public void setTestTitle(String testTitle) { this.testTitle = testTitle; }

    public int getTestMarks() { return testMarks; }
    public void setTestMarks(int testMarks) { this.testMarks = testMarks; }

    @Override
    public String toString() {
        return "Test{id='" + testId + "', title='" + testTitle + "', marks=" + testMarks + "}";
    }
}
```

```java
// src/main/java/com/example/model/Student.java
package com.example.model;

public class Student {
    private String studentId;
    private String studentName;
    private Test studentTest;  // composition

    // Getters & setters
    public String getStudentId() { return studentId; }
    public void setStudentId(String studentId) { this.studentId = studentId; }

    public String getStudentName() { return studentName; }
    public void setStudentName(String studentName) { this.studentName = studentName; }

    public Test getStudentTest() { return studentTest; }
    public void setStudentTest(Test studentTest) { this.studentTest = studentTest; }

    @Override
    public String toString() {
        return "Student{id='" + studentId + "', name='" + studentName + "', test=" + studentTest + "}";
    }
}
```

---

### 2) Spring XML configuration

`src/main/resources/spring-config.xml`

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
         http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- Student 1: Steve, Core Java Test, 80 -->
    <bean id="student1" class="com.example.model.Student">
        <property name="studentId" value="S001"/>
        <property name="studentName" value="Steve"/>
        <property name="studentTest">
            <bean class="com.example.model.Test">
                <property name="testId" value="T001"/>
                <property name="testTitle" value="Core Java Test"/>
                <property name="testMarks" value="80"/>
            </bean>
        </property>
    </bean>

    <!-- Student 2: (example name) Alex, Oracle Test, 83 -->
    <bean id="student2" class="com.example.model.Student">
        <property name="studentId" value="S002"/>
        <property name="studentName" value="Alex"/>
        <property name="studentTest">
            <bean class="com.example.model.Test">
                <property name="testId" value="T002"/>
                <property name="testTitle" value="Oracle Test"/>
                <property name="testMarks" value="83"/>
            </bean>
        </property>
    </bean>

</beans>
```

> If you want the second student’s name to be different, just change `Alex`—the key part is the test title/marks.

---

### 3) Client to display both students

```java
// src/main/java/com/example/App.java
package com.example;

import org.springframework.context.support.ClassPathXmlApplicationContext;
import com.example.model.Student;

public class App {
    public static void main(String[] args) {
        try (ClassPathXmlApplicationContext ctx =
                 new ClassPathXmlApplicationContext("spring-config.xml")) {

            Student s1 = ctx.getBean("student1", Student.class);
            Student s2 = ctx.getBean("student2", Student.class);

            // Display neatly
            printStudent(s1);
            System.out.println("-----");
            printStudent(s2);
        }
    }

    private static void printStudent(Student s) {
        System.out.println("Student ID   : " + s.getStudentId());
        System.out.println("Student Name : " + s.getStudentName());
        System.out.println("Test ID      : " + s.getStudentTest().getTestId());
        System.out.println("Test Title   : " + s.getStudentTest().getTestTitle());
        System.out.println("Test Marks   : " + s.getStudentTest().getTestMarks());
    }
}
```

---

### ✅ Expected Output

```
Student ID   : S001
Student Name : Steve
Test ID      : T001
Test Title   : Core Java Test
Test Marks   : 80
-----
Student ID   : S002
Student Name : Alex
Test ID      : T002
Test Title   : Oracle Test
Test Marks   : 83
```

That’s it—two students, each with their own `Test`, wired via Spring and printed from the client.
