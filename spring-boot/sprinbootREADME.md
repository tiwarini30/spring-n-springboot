# Spring Boot — Quick Guide for Interview Preparation

This README is a focused, interview-friendly, hands-on guide for the `spring-boot` package in this repository. It explains core Spring Boot concepts, shows a minimal REST example you can run quickly, includes common interview questions with short answers, and suggests practice exercises.

> File path: spring-boot/sprinbootREADME.md

---

## Table of contents
- Overview
- Prerequisites
- What you'll learn
- Typical project layout (what to look for in this package)
- Minimal example: REST API (code snippets)
- How to build & run (Maven + run commands)
- cURL examples
- Common Spring Boot interview topics & concise answers
- Practice tasks for interviews
- Troubleshooting tips

---

## Overview
Spring Boot is a convention-over-configuration framework that makes it fast to build standalone, production-grade Spring applications. This guide focuses on the essentials you are likely to be asked about in interviews and gives a small runnable example so you can demonstrate knowledge during live coding or take-home tasks.

---

## Prerequisites
- JDK 11 or newer (JAVA_HOME set)
- Maven (or Gradle) installed
- Basic knowledge of Java and HTTP

---

## What you'll learn
- How to create a minimal Spring Boot REST service
- Key annotations: `@SpringBootApplication`, `@RestController`, `@Service`
- How to build and run the app
- How to write simple requests with `curl`
- Short answers to common interview questions

---

## Typical project layout (look in this folder)
Look for these files and folders inside `spring-boot`:
- `pom.xml` or `build.gradle` — build definition
- `src/main/java/.../Application.java` — main class annotated with `@SpringBootApplication`
- `src/main/java/.../controller/` — controllers for REST endpoints
- `src/main/java/.../service/` — business logic
- `src/main/resources/application.properties` — configuration

If the package already contains code, open the main app class to find the base package used for component scanning.

---

## Minimal example: simple Greeting REST API (in-memory)
Create these classes under package `com.example.demo`.

1) Application (entry point)

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

2) Model: Greeting

```java
package com.example.demo.model;

public class Greeting {
    private Long id;
    private String message;

    public Greeting() {}

    public Greeting(Long id, String message) {
        this.id = id;
        this.message = message;
    }

    public Long getId() { return id; }
    public void setId(Long id) { this.id = id; }
    public String getMessage() { return message; }
    public void setMessage(String message) { this.message = message; }
}
```

3) Service: GreetingService

```java
package com.example.demo.service;

import com.example.demo.model.Greeting;
import org.springframework.stereotype.Service;

import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.atomic.AtomicLong;

@Service
public class GreetingService {
    private final ConcurrentHashMap<Long, Greeting> store = new ConcurrentHashMap<>();
    private final AtomicLong idGen = new AtomicLong(1);

    public Greeting create(String message) {
        long id = idGen.getAndIncrement();
        Greeting g = new Greeting(id, message);
        store.put(id, g);
        return g;
    }

    public Greeting get(long id) {
        return store.get(id);
    }
}
```

4) Controller: GreetingController

```java
package com.example.demo.controller;

import com.example.demo.model.Greeting;
import com.example.demo.service.GreetingService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/greetings")
public class GreetingController {
    private final GreetingService service;

    public GreetingController(GreetingService service) {
        this.service = service;
    }

    @PostMapping
    public ResponseEntity<Greeting> create(@RequestParam(defaultValue = "Hello") String message) {
        return ResponseEntity.ok(service.create(message));
    }

    @GetMapping("/{id}")
    public ResponseEntity<Greeting> get(@PathVariable long id) {
        Greeting g = service.get(id);
        return g != null ? ResponseEntity.ok(g) : ResponseEntity.notFound().build();
    }
}
```

5) Minimal pom.xml (if you need to create one)

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>demo</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.1.4</version>
    <relativePath/>
  </parent>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>
</project>
```

---

## How to build & run
From the project root (where `pom.xml` lives):

- Build and package:
  - mvn clean package
- Run the JAR:
  - java -jar target/demo-0.0.1-SNAPSHOT.jar

Or run directly from your IDE by running `DemoApplication.main()`.

Server runs on port 8080 by default. To change port, add `src/main/resources/application.properties` with `server.port=9090` (for example).

---

## cURL examples
Create a greeting (POST):

curl -X POST "http://localhost:8080/api/greetings?message=Hello%20from%20curl"

Response example:

{
  "id": 1,
  "message": "Hello from curl"
}

Get a greeting (GET):

curl http://localhost:8080/api/greetings/1

---

## Common Spring Boot interview topics (short answers)
- What is Spring Boot?
  - A framework that simplifies Spring application setup with auto-configuration, starters, and embedded servers.

- What does `@SpringBootApplication` do?
  - It's a meta-annotation: `@Configuration`, `@EnableAutoConfiguration`, and `@ComponentScan` combined.

- What are starters?
  - Opinionated dependency aggregations (e.g., `spring-boot-starter-web`) that bring in a typical set of libraries.

- How does auto-configuration work?
  - Spring Boot inspects the classpath and existing beans; `@Conditional` annotations determine which configuration beans load.

- Differences between `@Component`, `@Service`, `@Repository`, `@Controller`:
  - Stereotype annotations with semantic roles. `@Repository` also applies persistence exception translation.

- How to externalize configuration?
  - `application.properties` or `application.yml`; use `@Value` or `@ConfigurationProperties` to bind values.

- What is `ApplicationContext`?
  - The Spring IoC container that holds bean definitions and manages lifecycle and dependency injection.

- How to test controllers?
  - Use `@WebMvcTest` for slice tests or `@SpringBootTest` for full integration tests; use `MockMvc` or `TestRestTemplate`.

- What is Spring Boot Actuator?
  - Provides production-ready endpoints such as `/actuator/health`, `/metrics` when added as a dependency.

---

## Practice tasks (high ROI for interviews)
1. Implement CRUD endpoints for a simple entity using H2 in-memory DB and Spring Data JPA.
2. Add request validation (`@Valid`, `@NotNull`) and custom error handling.
3. Add an integration test using `@SpringBootTest` and `TestRestTemplate`.
4. Add Actuator and query `/actuator/health`.
5. Create a Dockerfile and run the service in a container.

---

## Troubleshooting & tips
- Port conflicts: change `server.port` in `application.properties`.
- `NoSuchBeanDefinitionException`: ensure your main application class is in a parent package so `@ComponentScan` finds beans.
- Use `--debug` or set logging levels in `application.properties` for more detailed startup logs.

---

If you want, I can now:
- Commit this README file to `spring-boot/sprinbootREADME.md` in this repository (I will do that if you confirm), or
- Inspect the actual `spring-boot` folder in the repo and tailor the README to the existing classes and package names.

Tell me which option you prefer; if you want me to commit, I will create the file now in the repository.
