Got it! Here’s a clean Spring (Core) IoC example using **XML configuration** to wire up Countries, Players, and a simple Service to print everything and filter by country name.

# Project layout

```
spring-ioc-players/
├─ pom.xml
├─ src/main/java/com/example/ioc/
│  ├─ App.java
│  ├─ model/
│  │  ├─ Country.java
│  │  └─ Player.java
│  ├─ repo/
│  │  └─ PlayerRepository.java
│  └─ service/
│     └─ PlayerService.java
└─ src/main/resources/
   └─ beans.xml
```

---

# pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" 
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                             https://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>spring-ioc-players</artifactId>
  <version>1.0.0</version>

  <properties>
    <maven.compiler.source>17</maven.compiler.source>
    <maven.compiler.target>17</maven.compiler.target>
    <spring.version>5.3.39</spring.version>
  </properties>

  <dependencies>
    <!-- Spring Core + Context for IoC container -->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-context</artifactId>
      <version>${spring.version}</version>
    </dependency>
  </dependencies>
</project>
```

---

# Country.java

```java
package com.example.ioc.model;

public class Country {
    private String countryId;
    private String countryName;

    public Country() {}

    public Country(String countryId, String countryName) {
        this.countryId = countryId;
        this.countryName = countryName;
    }

    public String getCountryId() {
        return countryId;
    }
    public void setCountryId(String countryId) {
        this.countryId = countryId;
    }
    public String getCountryName() {
        return countryName;
    }
    public void setCountryName(String countryName) {
        this.countryName = countryName;
    }

    @Override
    public String toString() {
        return "Country{id='" + countryId + "', name='" + countryName + "'}";
    }
}
```

# Player.java

```java
package com.example.ioc.model;

public class Player {
    private String playerId;
    private String playerName;
    private Country country; // association

    public Player() {}

    public Player(String playerId, String playerName, Country country) {
        this.playerId = playerId;
        this.playerName = playerName;
        this.country = country;
    }

    public String getPlayerId() {
        return playerId;
    }
    public void setPlayerId(String playerId) {
        this.playerId = playerId;
    }
    public String getPlayerName() {
        return playerName;
    }
    public void setPlayerName(String playerName) {
        this.playerName = playerName;
    }
    public Country getCountry() {
        return country;
    }
    public void setCountry(Country country) {
        this.country = country;
    }

    @Override
    public String toString() {
        return "Player{id='" + playerId + "', name='" + playerName + "', country=" + country.getCountryName() + "}";
    }
}
```

# PlayerRepository.java

```java
package com.example.ioc.repo;

import com.example.ioc.model.Player;

import java.util.ArrayList;
import java.util.List;

public class PlayerRepository {
    private List<Player> players = new ArrayList<>();

    // IoC will inject this list from XML
    public void setPlayers(List<Player> players) {
        this.players = players;
    }

    public List<Player> findAll() {
        return players;
    }

    public List<Player> findByCountryName(String countryName) {
        String target = countryName == null ? "" : countryName.trim().toLowerCase();
        List<Player> out = new ArrayList<>();
        for (Player p : players) {
            if (p.getCountry() != null &&
                p.getCountry().getCountryName() != null &&
                p.getCountry().getCountryName().trim().toLowerCase().equals(target)) {
                out.add(p);
            }
        }
        return out;
    }
}
```

# PlayerService.java

```java
package com.example.ioc.service;

import com.example.ioc.model.Player;
import com.example.ioc.repo.PlayerRepository;

import java.util.List;

public class PlayerService {
    private PlayerRepository repository;

    // setter injection
    public void setRepository(PlayerRepository repository) {
        this.repository = repository;
    }

    public void printAllPlayers() {
        System.out.println("=== All Players ===");
        for (Player p : repository.findAll()) {
            System.out.printf("%s (%s) - Country: %s%n",
                    p.getPlayerName(), p.getPlayerId(), p.getCountry().getCountryName());
        }
        System.out.println();
    }

    public void printPlayersByCountry(String countryName) {
        System.out.println("=== Players from: " + countryName + " ===");
        List<Player> list = repository.findByCountryName(countryName);
        if (list.isEmpty()) {
            System.out.println("No players found.");
        } else {
            for (Player p : list) {
                System.out.printf("%s (%s)%n", p.getPlayerName(), p.getPlayerId());
            }
        }
        System.out.println();
    }
}
```

# beans.xml (IoC wiring with XML)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans 
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- Countries -->
    <bean id="countryIndia" class="com.example.ioc.model.Country">
        <property name="countryId" value="C001"/>
        <property name="countryName" value="India"/>
    </bean>

    <bean id="countryAustralia" class="com.example.ioc.model.Country">
        <property name="countryId" value="C002"/>
        <property name="countryName" value="Australia"/>
    </bean>

    <!-- Players (2 from India, 3 from Australia as per requirement) -->
    <bean id="player1" class="com.example.ioc.model.Player">
        <property name="playerId" value="P001"/>
        <property name="playerName" value="Sachin"/>
        <property name="country" ref="countryIndia"/>
    </bean>

    <bean id="player2" class="com.example.ioc.model.Player">
        <property name="playerId" value="P002"/>
        <property name="playerName" value="Dravid"/>
        <property name="country" ref="countryIndia"/>
    </bean>

    <bean id="player3" class="com.example.ioc.model.Player">
        <property name="playerId" value="P003"/>
        <property name="playerName" value="Ponting"/>
        <property name="country" ref="countryAustralia"/>
    </bean>

    <bean id="player4" class="com.example.ioc.model.Player">
        <property name="playerId" value="P004"/>
        <property name="playerName" value="Warne"/>
        <property name="country" ref="countryAustralia"/>
    </bean>

    <bean id="player5" class="com.example.ioc.model.Player">
        <property name="playerId" value="P005"/>
        <property name="playerName" value="Gilchrist"/>
        <property name="country" ref="countryAustralia"/>
    </bean>

    <!-- Repository with collection injection -->
    <bean id="playerRepository" class="com.example.ioc.repo.PlayerRepository">
        <property name="players">
            <list>
                <ref bean="player1"/>
                <ref bean="player2"/>
                <ref bean="player3"/>
                <ref bean="player4"/>
                <ref bean="player5"/>
            </list>
        </property>
    </bean>

    <!-- Service with dependency (setter injection) -->
    <bean id="playerService" class="com.example.ioc.service.PlayerService">
        <property name="repository" ref="playerRepository"/>
    </bean>

</beans>
```

# App.java (Client program)

```java
package com.example.ioc;

import com.example.ioc.service.PlayerService;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import java.util.Scanner;

public class App {
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("beans.xml");
        PlayerService service = ctx.getBean("playerService", PlayerService.class);

        // Display all players with country details
        service.printAllPlayers();

        // Optional: read a country name and print players from that country
        try (Scanner sc = new Scanner(System.in)) {
            System.out.print("Enter a country name to list its players (e.g., India or Australia): ");
            String input = sc.nextLine();
            service.printPlayersByCountry(input);
        }
    }
}
```

---

## How this demonstrates Spring Basics & IoC

* **Beans** are declared in `beans.xml` (Countries, Players, Repository, Service).
* **Dependency Injection**:

  * Players receive a `Country` via property injection.
  * `PlayerRepository` receives a **collection** of Players via property injection.
  * `PlayerService` receives the repository via setter injection.
* The **client** (`App`) asks the IoC container for `playerService` and uses it—no `new` keywords to wire dependencies.

---

## Sample console output

```
=== All Players ===
Sachin (P001) - Country: India
Dravid (P002) - Country: India
Ponting (P003) - Country: Australia
Warne (P004) - Country: Australia
Gilchrist (P005) - Country: Australia

Enter a country name to list its players (e.g., India or Australia): India
=== Players from: India ===
Sachin (P001)
Dravid (P002)
```

If you’d like the same with **Java-based configuration** instead of XML, say the word and I’ll drop an `@Configuration` version too.
