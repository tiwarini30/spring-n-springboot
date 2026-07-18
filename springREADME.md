# 🌱 Spring Framework Learning Guide

Welcome to the Spring Framework learning repository! This directory contains **4 beginner-friendly projects** that teach you the fundamentals of Spring Framework from zero to advanced concepts.

---

## 📚 Table of Contents

1. [Project Overview](#project-overview)
2. [Prerequisites](#prerequisites)
3. [Project 1: first-spring](#project-1-first-spring)
4. [Project 2: second-spring](#project-2-second-spring)
5. [Project 3: coupling-demo](#project-3-coupling-demo)
6. [Project 4: project-spring-crud](#project-4-project-spring-crud)
7. [How to Run](#how-to-run)
8. [Key Concepts Explained](#key-concepts-explained)

---

## 🎯 Project Overview

| Project | Level | Focus | Key Concepts |
|---------|-------|-------|--------------|
| **first-spring** | Beginner | Spring Basics | IoC Container, Bean Creation, XML Config |
| **second-spring** | Beginner-Intermediate | Advanced Configuration | Annotations, Java Config, Bean Lifecycle |
| **coupling-demo** | Beginner-Intermediate | Loose Coupling | Dependency Injection, Interface-based Design |
| **project-spring-crud** | Intermediate | Real Application | MVC Pattern, Layered Architecture, CRUD Operations |

---

## 🔧 Prerequisites

Before starting, make sure you have:

- **Java 21** or higher installed
- **Maven** (for building projects)
- **IDE** (VS Code, IntelliJ IDEA, or Eclipse)
- Basic knowledge of Java OOP concepts

### Required Dependencies

All projects use Spring 7.0.0-M9:
```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-core</artifactId>
    <version>7.0.0-M9</version>
</dependency>

<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context</artifactId>
    <version>7.0.0-M9</version>
</dependency>
```

---

## 📖 Project 1: first-spring

### What You'll Learn
- What is Spring Framework and IoC Container
- How to create and manage beans (objects)
- Spring configuration using XML files
- Dependency Injection basics

### Directory Structure
```
first-spring/
├── pom.xml                                 # Maven configuration
└── src/main/java/com/example/
    ├── demo/
    │   ├── MainApp.java                   # Entry point
    │   └── GreetingService.java           # Simple service class
    └── loose/
        ├── NotificationService.java       # Interface
        ├── EmailNotificationService.java  # Email implementation
        ├── SMSNotificationService.java    # SMS implementation
        └── UserService.java               # Service with dependency
```

### What Each File Does

#### **GreetingService.java**
```java
public class GreetingService {
    public void sayHello() {
        System.out.println("Hello from Spring!");
    }
}
```
**Explanation:** A simple service class that prints a greeting message.

#### **NotificationService.java (Interface)**
```java
public interface NotificationService {
    void send(String message);
}
```
**Explanation:** An interface that defines what notification services should be able to do - send a message.

#### **EmailNotificationService.java**
Implements `NotificationService` - sends notifications via email.

#### **SMSNotificationService.java**
Implements `NotificationService` - sends notifications via SMS.

#### **UserService.java**
```java
public class UserService {
    public NotificationService notificationService;
    
    // Constructor Injection - dependency passed through constructor
    public UserService(NotificationService notificationService) {
        this.notificationService = notificationService;
    }
    
    // Setter Injection - dependency set through setter method
    public void setNotificationService(NotificationService notificationService) {
        this.notificationService = notificationService;
    }
    
    public void notifyUser(String message) {
        notificationService.send("Notification hello");
    }
}
```
**Explanation:** A service that needs to send notifications. Instead of creating the notification service itself, it receives it from outside (Dependency Injection). This makes it flexible - you can easily switch between Email or SMS notifications!

#### **MainApp.java**
```java
public class MainApp {
    public static void main(String[] args) {
        // Create Spring container
        ApplicationContext context
            = new ClassPathXmlApplicationContext("applicationBeanContext.xml");
        
        // Get GreetingService bean from Spring
        GreetingService greetingService
            = (GreetingService) context.getBean("myBean");
        greetingService.sayHello();
        
        // Get UserService beans configured in XML
        UserService userService
            = (UserService) context.getBean("UserServiceSMS");
        userService.notifyUser("What's up!");
    }
}
```
**Explanation:** The main entry point where we:
1. Create a Spring ApplicationContext (IoC container) from XML config
2. Get beans (objects) from Spring instead of creating them ourselves
3. Use those beans to perform operations

### 🎓 Key Learning Points
- **IoC (Inversion of Control)**: Instead of creating objects yourself, Spring creates and manages them
- **Bean**: An object managed by Spring
- **XML Configuration**: Telling Spring how to create beans using XML file
- **Dependency Injection**: Objects receive their dependencies from outside instead of creating them

---

## 📖 Project 2: second-spring

### What You'll Learn
- **Java-based configuration** (using annotations instead of XML)
- **@Component, @Bean** annotations
- **Bean Lifecycle** - what happens when beans are created and destroyed
- **AppConfig class** - centralized configuration using Java code

### Directory Structure
```
second-spring/
├── pom.xml
└── src/main/java/com/example/
    ├── demo/
    │   ├── AppConfig.java           # Configuration class with @Bean
    │   ├── GreetingService.java     # Service class
    │   ├── LifecycleBean.java       # Bean with init/destroy methods
    │   └── MainApp.java             # Entry point using Java config
    └── loose/
        ├── NotificationService.java
        ├── EmailNotificationService.java
        ├── SMSNotificationService.java
        └── UserService.java
```

### What Each File Does

#### **AppConfig.java** (New Concept!)
```java
@Configuration
public class AppConfig {
    
    @Bean
    public GreetingService greetingService() {
        return new GreetingService();
    }
    
    @Bean
    public NotificationService emailService() {
        return new EmailNotificationService();
    }
    
    @Bean
    public NotificationService smsService() {
        return new SMSNotificationService();
    }
    
    @Bean
    public UserService userService() {
        return new UserService(smsService()); // Inject SMS service
    }
}
```
**Explanation:** Instead of XML configuration, we use Java code!
- `@Configuration` tells Spring this is a configuration class
- `@Bean` methods create and return beans
- This is cleaner and more flexible than XML

#### **LifecycleBean.java** (New Concept!)
```java
@Component
public class LifecycleBean {
    
    @PostConstruct
    public void init() {
        System.out.println("Bean is being initialized!");
        // Do setup work here (open connections, load data, etc.)
    }
    
    public void performTask() {
        System.out.println("Performing important task");
    }
    
    @PreDestroy
    public void destroy() {
        System.out.println("Bean is being destroyed!");
        // Do cleanup work here (close connections, release resources, etc.)
    }
}
```
**Explanation:** Shows the lifecycle of a bean:
1. **Birth** - `@PostConstruct` runs when bean is created (setup)
2. **Life** - Bean performs its tasks
3. **Death** - `@PreDestroy` runs when bean is destroyed (cleanup)

#### **MainApp.java**
```java
public class MainApp {
    public static void main(String[] args) {
        System.out.println("Starting Spring Application Context");
        
        // Using Java-based configuration instead of XML!
        ApplicationContext context
            = new AnnotationConfigApplicationContext(AppConfig.class);
        
        System.out.println("Retrieving Lifecycle Bean");
        LifecycleBean lifecycleBean = context.getBean(LifecycleBean.class);
        
        lifecycleBean.performTask();
        
        System.out.println("Closing Spring Context");
        // When context closes, @PreDestroy methods are called
    }
}
```

### 🎓 Key Learning Points
- **Annotations** (`@Configuration`, `@Bean`, `@Component`): Modern way to configure Spring
- **Java-based Config**: More flexible and powerful than XML
- **Bean Lifecycle**: Understand when beans are created and destroyed
- **@PostConstruct & @PreDestroy**: Hook methods for initialization and cleanup

---

## 📖 Project 3: coupling-demo

### What You'll Learn
- **Tight Coupling** - BAD: Classes depend directly on each other
- **Loose Coupling** - GOOD: Classes depend on interfaces, not concrete classes
- **Why Loose Coupling is Better**: Easy to test, change, and extend

### Directory Structure
```
coupling-demo/
├── coupling-demo.iml
├── pom.xml
└── src/
    ├── AppMain.java           # Demonstrates both approaches
    ├── tight/                 # BAD - Tightly coupled code
    │   ├── NotificationService.java
    │   ├── PushNotificationService.java
    │   └── UserService.java
    └── loose/                 # GOOD - Loosely coupled code
        ├── NotificationService.java (Interface)
        ├── EmailNotificationService.java
        ├── SMSNotificationService.java
        └── UserService.java
```

### Understanding Tight vs Loose Coupling

#### **❌ TIGHT COUPLING (WRONG WAY)**
```
User Service DIRECTLY depends on specific notification classes
↓
Problem: If you want to add a new notification type (Slack, Push, etc),
you must modify UserService code!
This is hard to maintain and test.
```

#### **✅ LOOSE COUPLING (RIGHT WAY)**
```
User Service depends on NotificationService INTERFACE
↓
EmailNotificationService, SMSNotificationService, 
PushNotificationService all implement NotificationService
↓
Benefit: Add new notification types WITHOUT changing UserService!
Easy to test and maintain.
```

### AppMain.java - Demonstrates Both Approaches
```java
// Tight Coupling (BAD)
tight.UserService userService = new tight.UserService();
userService.notifyUser("Order Placed!"); // Tightly coupled to one implementation

// Loose Coupling (GOOD) - Method 1: Constructor Injection
NotificationService smsService = new SMSNotificationService();
loose.UserService userServiceLoose = new loose.UserService(smsService);
userServiceLoose.notifyUser("Order Processed!");

// Loose Coupling (GOOD) - Method 2: Setter Injection
loose.UserService userServiceLooseSetter = new loose.UserService();
userServiceLooseSetter.setNotificationService(emailService);
// Can change which notification service is used anytime!
```

### 🎓 Key Learning Points
- **Tight Coupling**: Classes depend directly on concrete implementations (BAD)
- **Loose Coupling**: Classes depend on interfaces/abstractions (GOOD)
- **Dependency Injection Methods**:
  - **Constructor Injection**: Dependencies passed to constructor
  - **Setter Injection**: Dependencies set via setter methods
  - **Field Injection**: Dependencies assigned directly to fields
- **Benefits of Loose Coupling**: Testability, flexibility, maintainability

---

## 📖 Project 4: project-spring-crud

### What You'll Learn
- **Layered Architecture** - How real applications are structured
- **CRUD Operations** - Create, Read, Update, Delete operations
- **MVC Pattern** - Model-View-Controller pattern
- **Spring Annotations** - @Service, @Controller, @Repository
- **Dependency Injection in Real Applications**

### Directory Structure
```
project-spring-crud/
├── pom.xml
└── src/main/java/com/example/
    ├── MainApp.java                   # Entry point
    ├── config/
    │   └── AppConfig.java             # Spring configuration
    ├── controller/
    │   └── UserController.java        # Handles user requests
    ├── service/
    │   └── UserService.java           # Business logic
    ├── repository/
    │   └── UserRepository.java        # Data access
    └── db/
        └── DatabaseConnection.java    # Database utilities
```

### Application Architecture

```
┌─────────────────────────────────────────┐
│         MainApp.java                    │
│    (Starts the application)             │
└────────────────┬────────────────────────┘
                 ↓
┌─────────────────────────────────────────┐
│    UserController                       │
│  (Handles user requests)                │
│  - createUser(name)                     │
│  - listUsers()                          │
└────────────────┬────────────────────────┘
                 ↓ (calls)
┌─────────────────────────────────────────┐
│    UserService (Business Logic)         │
│  - addUser(name)                        │
│  - getAllUsers()                        │
└────────────────┬────────────────────────┘
                 ↓ (uses)
┌─────────────────────────────────────────┐
│    UserRepository (Database Access)     │
│  - save(user)                           │
│  - findAll()                            │
└────────────────┬────────────────────────┘
                 ↓
┌─────────────────────────────────────────┐
│    DatabaseConnection                   │
│  (Manages database connection)          │
└─────────────────────────────────────────┘
```

### What Each Layer Does

#### **1. Controller Layer (UserController.java)**
```java
@Controller
public class UserController {
    private UserService userService;
    
    public UserController(UserService userService) {
        this.userService = userService;
    }
    
    public void createUser(String name) {
        userService.addUser(name);  // Delegate to service layer
        System.out.println("User added: " + name);
    }
    
    public void listUsers() {
        List<String> users = userService.getAllUsers();
        System.out.println("All Users: " + users);
    }
}
```
**Purpose**: Handles requests from users/applications. Delegates business logic to service layer.

#### **2. Service Layer (UserService.java)**
```java
@Service
public class UserService {
    private UserRepository userRepository;
    
    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }
    
    public void addUser(String name) {
        userRepository.save(name);  // Delegate to repository
    }
    
    public List<String> getAllUsers() {
        return userRepository.findAll();
    }
}
```
**Purpose**: Contains business logic. Works with repository to access/manage data.

#### **3. Repository Layer (UserRepository.java)**
```java
@Repository
public class UserRepository {
    private List<String> users = new ArrayList<>();
    
    public void save(String user) {
        users.add(user);  // Store in database
    }
    
    public List<String> findAll() {
        return new ArrayList<>(users);  // Retrieve from database
    }
}
```
**Purpose**: Handles all database operations. Provides data to service layer.

#### **4. Configuration (AppConfig.java)**
```java
@Configuration
public class AppConfig {
    @Bean
    public UserRepository userRepository() {
        return new UserRepository();
    }
    
    @Bean
    public UserService userService() {
        return new UserService(userRepository());  // Inject repository
    }
    
    @Bean
    public UserController userController() {
        return new UserController(userService());  // Inject service
    }
}
```
**Purpose**: Configures which beans (objects) Spring should create and how to inject dependencies.

#### **5. MainApp.java**
```java
public class MainApp {
    public static void main(String[] args) {
        ApplicationContext context
            = new AnnotationConfigApplicationContext(AppConfig.class);
        
        UserController controller = context.getBean(UserController.class);
        
        controller.createUser("Alice");  // Add first user
        controller.createUser("Bob");     // Add second user
        controller.listUsers();           // Show all users
    }
}
```
**Purpose**: Entry point. Sets up Spring context and starts the application.

### 🎓 Key Learning Points
- **Layered Architecture**: Separates concerns into Controller → Service → Repository
- **Why Layers?** Easy to test each layer independently, easy to maintain and modify
- **@Service**: Marks class as business logic layer
- **@Controller**: Marks class as presentation/request handling layer
- **@Repository**: Marks class as data access layer
- **CRUD Operations**: Create, Read, Update, Delete - fundamental database operations
- **Dependency Injection**: Spring automatically injects dependencies between layers

---

## 🚀 How to Run

### Option 1: Run with Maven

```bash
# Navigate to a project directory
cd spring/first-spring

# Compile the project
mvn clean compile

# Run the application
mvn exec:java -Dexec.mainClass="com.example.demo.MainApp"
```

### Option 2: Run in IDE

1. Open the project in your IDE (IntelliJ, Eclipse, etc.)
2. Find the `MainApp.java` file
3. Right-click → Run or press `Ctrl+Shift+F10` (IntelliJ)
4. See the console output!

### Expected Outputs

#### **first-spring**
```
Hello from Spring!
Notification hello [from SMS]
Notification hello [from Email]
```

#### **second-spring**
```
Starting Spring Application Context
Bean is being initialized!
Retrieving Lifecycle Bean
Performing important task
Closing Spring Context
Bean is being destroyed!
```

#### **coupling-demo**
Shows examples of both tight and loose coupling approaches

#### **project-spring-crud**
```
User added: Alice
User added: Bob
All Users: [Alice, Bob]
```

---

## 🧠 Key Concepts Explained

### 1. **Inversion of Control (IoC)**
Instead of you creating objects, Spring creates and manages them.
```
OLD WAY:
UserService service = new UserService(new EmailNotificationService());

NEW WAY (with Spring):
UserService service = context.getBean(UserService.class);
// Spring already created it with proper dependencies!
```

### 2. **Dependency Injection (DI)**
Objects receive dependencies from outside instead of creating them.
```java
// Without DI (Bad):
public class UserService {
    private EmailService emailService = new EmailService();
    // Hard-coded to use EmailService, can't change!
}

// With DI (Good):
public class UserService {
    private NotificationService notificationService;
    
    public UserService(NotificationService notificationService) {
        this.notificationService = notificationService;
        // Can use any implementation!
    }
}
```

### 3. **Beans**
Objects created and managed by Spring container. Instead of `new`, Spring creates instances.

### 4. **ApplicationContext**
The Spring container. It creates objects, manages them, and provides them on demand.

### 5. **Annotations**
Markers that tell Spring what to do with classes.
- `@Configuration` - This class configures Spring
- `@Bean` - This method creates a bean
- `@Service` - This class contains business logic
- `@Controller` - This class handles requests
- `@Repository` - This class accesses data
- `@Autowired` - Inject dependency automatically
- `@Component` - Generic Spring-managed component
- `@PostConstruct` - Run after bean creation
- `@PreDestroy` - Run before bean destruction

### 6. **Constructor Injection vs Setter Injection**
```java
// Constructor Injection (Preferred)
public UserService(NotificationService service) {
    this.service = service;
}
// Pros: Dependencies visible, can't forget to set
// Cons: Long constructor if many dependencies

// Setter Injection
public void setService(NotificationService service) {
    this.service = service;
}
// Pros: Flexible, clear what's being set
// Cons: Dependencies not obvious, can be incomplete
```

---

## 📝 Summary Learning Path

```
START HERE (Easy to Hard)
↓
first-spring     - Learn IoC, Beans, XML Configuration
↓
coupling-demo    - Learn why loose coupling matters, Dependency Injection
↓
second-spring    - Learn Java-based Configuration, Annotations, Lifecycle
↓
project-spring-crud - Learn real application architecture, CRUD with layers
```

---

## 💡 Common Mistakes for Beginners

### ❌ Mistake 1: Creating objects manually in Spring project
```java
// WRONG:
UserService service = new UserService();

// RIGHT:
UserService service = context.getBean(UserService.class);
```

### ❌ Mistake 2: Tight coupling
```java
// WRONG:
public class UserService {
    EmailService emailService = new EmailService(); // Hard-coded!
}

// RIGHT:
public class UserService {
    NotificationService notificationService;
    public UserService(NotificationService service) {
        this.notificationService = service;
    }
}
```

### ❌ Mistake 3: Forgetting to configure beans
```java
// WRONG: Bean not configured
@Bean
public UserService userService() {
    return new UserService(); // Missing dependency!
}

// RIGHT: Dependencies injected
@Bean
public UserService userService(UserRepository repository) {
    return new UserService(repository);
}
```

---

## 📚 Additional Resources

- **Official Spring Documentation**: https://spring.io/
- **Spring Guides**: https://spring.io/guides
- **Spring Boot** (next step after Spring): https://spring.io/projects/spring-boot
- **Java Documentation**: https://docs.oracle.com/

---

## 🤝 Next Steps After Learning These Projects

1. **Spring Boot** - Simplified Spring development with auto-configuration
2. **Spring Data JPA** - Easy database access with less code
3. **Spring Web** - Build REST APIs and web applications
4. **Spring Security** - Add authentication and authorization
5. **Microservices** - Build scalable distributed systems

---

## ✨ Happy Learning!

Remember: Spring is all about **loose coupling , Tight coupling, dependency injection, and writing maintainable code**. 

Take time to understand each project, run the code, and modify it to see what happens. Learning by doing is the best way! 🚀

---


*For questions or issues, feel free to raise an issue in this repository.*

