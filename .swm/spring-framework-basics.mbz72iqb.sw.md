---
title: Spring Framework Basics
---

![](/.swm/images/spring-2024-6-26-3-29-46-865.png)

Spring is an open-source application framework for the Java platform. It provides comprehensive infrastructure support for developing **Java applications, particularly web applications**.

The spring framework is a **layered architecture** which consists of several modules. all modules are built on the top of its core container. these modules provide everything that a developer may need for use in the enterprise application development. he is always free to choose what features he needs and eliminate the modules which are of no use. it's **modular architecture** enables integration with other frameworks without much hassle.

&nbsp;

| Core<br><br>The IoC container | **AOP**<br><br>Spring AOP<br>AspectJ integration | DAO<br><br>Spring JDBC<br>Transaction management | ORM<br><br>Hibernate<br>JPA<br>TopLink<br>JDO<br>OJB<br>iBatis | JEE<br><br>JMX<br>JMS<br>JCA<br>Remoting<br>EJBs<br>Email | Web<br><br>Spring Web MVC<br>Framework Integration<br>Struts<br>WebWork<br>Tapestry<br>JSF<br>Rich View Support<br>JSPs<br>Velocity<br>FreeMarker<br>PDF<br>Excel<br>Jasper Reports<br>Spring Portlet MVC |
| :---------------------------- | :----------------------------------------------- | :----------------------------------------------- | :------------------------------------------------------------- | :-------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

&nbsp;

### **Core: The IoC container**

- This is the foundation of the Spring Framework.

- IoC (Inversion of Control) container manages the lifecycle of beans and handles dependency injection

#### **IoC container**

Instead of the application controlling the flow of the program and making calls to reusable libraries, the framework controls the flow and calls into the custom code.

The Spring IoC container is responsible for instantiating, configuring, and assembling objects known as beans. It manages the lifecycle of these beans and injects dependencies between them.

`Decoupling: Reduces dependencies between components.`

`Modularity: Makes the application more modular and easier to manage.`

`Testability: Easier to unit test components in isolation. `

`Flexibility: Easier to switch implementations without changing the code. It's responsible for creating, configuring, and managing application objects.`

&nbsp;

#### Implementation in Spring:&nbsp;

`XML Configuration: Beans and their dependencies are defined in XML files.`

`Annotation-based Configuration: Using annotations like @Autowired, @Component, etc.`

`Java-based Configuration: Using @Configuration and @Bean annotations.`

&nbsp;

In Spring Framework, the primary **type of Inversion of Control (IoC)** used is Dependency Injection (DI). However, Spring also supports some other IoC patterns to a lesser extent. Let's break down the types of IoC used in Spring: hello

&nbsp;

#### **1. Dependency Injection (DI)**

&nbsp;&nbsp;&nbsp;This is the most prominent and widely used form of IoC in Spring. It has three main subtypes:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Dependencies are provided through a class constructor.

&nbsp;&nbsp;&nbsp;`a. Constructor Injection`

```java
@Component
public class MovieRecommender {
    private final MovieFinder movieFinder;

    @Autowired
    public MovieRecommender(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }
}
```

&nbsp;&nbsp;&nbsp;`b. Setter Injection`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Dependencies are provided through setter methods.

```java
@Component
public class MovieRecommender {
    private MovieFinder movieFinder;

    @Autowired
    public void setMovieFinder(MovieFinder movieFinder) {
        this.movieFinder = movieFinder;
    }
}
```

&nbsp;&nbsp;&nbsp;`c. Field Injection`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Dependencies are injected directly into fields (not recommended for production code due to testability issues).

```java
@Component
public class MovieRecommender {
    @Autowired
    private MovieFinder movieFinder;
}
```

&nbsp;

#### **2. Dependency Lookup**

&nbsp;&nbsp;&nbsp;While not as commonly used as DI, Spring does support dependency lookup:

&nbsp;&nbsp;&nbsp;`a. ApplicationContextAware interface`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Allows beans to access the ApplicationContext directly.

```java
@Component
public class MovieRecommender implements ApplicationContextAware {
    private ApplicationContext context;

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        this.context = applicationContext;
    }

    public void someMethod() {
        MovieFinder finder = context.getBean(MovieFinder.class);
        // Use finder
    }
}
```

&nbsp;&nbsp;&nbsp;`b. BeanFactory lookup`

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Direct lookup from the BeanFactory (less common).

&nbsp;

#### **3. Method Injection**

&nbsp;&nbsp;&nbsp;Used in specific scenarios where a bean needs to repeatedly call a method on a prototype bean.

&nbsp;&nbsp;&nbsp;`a. Lookup Method Injection`

```java
public abstract class CommandManager {
    @Lookup("myCommand")
    protected abstract Command createCommand();
}
```

#### **4. Template Method Pattern**

&nbsp;&nbsp;&nbsp;Used extensively in Spring's JdbcTemplate, TransactionTemplate, etc.

#### **5. Event-driven Programming**

&nbsp;&nbsp;&nbsp;Spring's ApplicationEvent and ApplicationListener provide event-driven capabilities.

#### **6. Aspect-Oriented Programming (AOP)**

&nbsp;&nbsp;&nbsp;While not strictly IoC, Spring's AOP capabilities invert control for cross-cutting concerns.

&nbsp;

Among these, Dependency Injection is by far the most commonly used and recommended approach in Spring applications. The other types are used in specific scenarios or for more advanced use cases. Spring's flexible architecture allows developers to choose the most appropriate type of IoC for their specific needs, with DI being the default and preferred method in most cases.

&nbsp;

#### **DAO (Data Access Object) \[Currenlty Replaced By JPA\]**

#### **Spring JDBC API**

simplifies database operations by reducing boilerplate code. It provides several classes that make working with JDBC easier, primarily through the JdbcTemplate class.

Key features:

- `Simplifies exception handling`

- `Manages resources (connections, statements, resultsets)`

- `Provides helpful utilities for parameter binding and result set extraction`

```java
@Repository
public class UserDaoImpl implements UserDao {

    private JdbcTemplate jdbcTemplate;

    @Autowired
    public UserDaoImpl(DataSource dataSource) {
        this.jdbcTemplate = new JdbcTemplate(dataSource);
    }

    @Override
    public User findById(long id) {
        String sql = "SELECT * FROM users WHERE id = ?";
        return jdbcTemplate.queryForObject(sql, new Object[]{id}, new UserRowMapper());
    }

    @Override
    public void save(User user) {
        String sql = "INSERT INTO users (name, email) VALUES (?, ?)";
        jdbcTemplate.update(sql, user.getName(), user.getEmail());
    }
}

class UserRowMapper implements RowMapper<User> {
    @Override
    public User mapRow(ResultSet rs, int rowNum) throws SQLException {
        User user = new User();
        user.setId(rs.getLong("id"));
        user.setName(rs.getString("name"));
        user.setEmail(rs.getString("email"));
        return user;
    }
}
```

&nbsp;

#### **Transaction management**

Spring provides a consistent programming model for handling transactions across different APIs (JDBC, JPA, Hibernate, etc.).

Key features:

- `Declarative transaction management (using annotations)`

- `Programmatic transaction management`

- `Supports both local and global transactions`

```java
@Service
@Transactional
public class UserServiceImpl implements UserService {

    private final UserDao userDao;

    @Autowired
    public UserServiceImpl(UserDao userDao) {
        this.userDao = userDao;
    }

    @Override
    public void registerUser(User user) {
        userDao.save(user);
        // If an exception occurs here, the transaction will be rolled back
        sendWelcomeEmail(user);
    }

    private void sendWelcomeEmail(User user) {
        // Logic to send welcome email
        // If this throws an exception, the userDao.save() will be rolled back
    }

    @Override
    @Transactional(readOnly = true)
    public User getUserById(long id) {
        return userDao.findById(id);
    }
}
```

Configuration (using Java-based configuration):

```java
@Configuration
@EnableTransactionManagement
public class DatabaseConfig {

    @Bean
    public DataSource dataSource() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName("com.mysql.jdbc.Driver");
        dataSource.setUrl("jdbc:mysql://localhost:3306/mydb");
        dataSource.setUsername("user");
        dataSource.setPassword("password");
        return dataSource;
    }

    @Bean
    public PlatformTransactionManager transactionManager(DataSource dataSource) {
        return new DataSourceTransactionManager(dataSource);
    }
}
```

&nbsp;

### **You can indeed replace Spring DAO with JPA**(Java Persistence API) **in most scenarios:**

#### **JPA(Java Persistence API)**

JPA is a Java specification for managing relational data in Java applications. It provides a framework for mapping Java objects to database tables (Object-Relational Mapping or ORM)

&nbsp;

| Difference                      | DAO                                                                                                          | JPA                                                                                                                |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------------------ |
| Abstraction Level               | Provides an abstraction layer for database operations, but you still write SQL queries or use JDBC directly. | Offers a higher level of abstraction. You work with Java objects (entities) instead of direct database operations. |
| Object-Relational Mapping (ORM) | Requires manual mapping between database tables and Java objects.                                            | Automatically handles the mapping between database tables and Java objects (entities).                             |
| Code Reduction                  | Often requires writing boilerplate code for CRUD operations.                                                 | Provides built-in CRUD operations, significantly reducing the amount of code you need to write.                    |
| Database Independence           | Can be database-independent, but requires more effort to achieve this.                                       | Offers better database independence out of the box. Changing databases often requires minimal code changes.        |
| Query Language                  | Typically uses native SQL or a query builder.                                                                | Uses JPQL (Java Persistence Query Language) or Criteria API, which are object-oriented query languages.            |
| Performance Optimization        | Allows fine-grained control over SQL queries for optimization.                                               | Provides various optimization techniques like lazy loading, caching, and batch processing.                         |
| Transaction Management          | Requires explicit transaction management.                                                                    | Integrates seamlessly with Spring's declarative transaction management.                                            |
| Learning Curve                  | Simpler to understand initially, especially for those familiar with SQL.                                     | Steeper learning curve but offers more powerful features once mastered.                                            |
| Standardization                 | No standard implementation; varies across projects.                                                          | A standardized API, part of the Java EE specification.                                                             |
| Testability                     | Can be easier to unit test as it's closer to the database.                                                   | Offers in-memory database testing and mocking capabilities.                                                        |

With JPA and Spring Data JPA:

```java
@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    // Other fields, getters, setters...
}

public interface UserRepository extends JpaRepository<User, Long> {
    // Additional custom query methods if needed
}
```

&nbsp;

### **ORM (Object-Relational Mapping)**

&nbsp;

#### **Hibernate**

Hibernate simplifies database interactions by **automatically mapping Java objects to database** tables. It manages the persistence layer of the application, allowing developers to focus on writing business logic rather than boilerplate code for database operations. Hibernate integrates seamlessly with Spring Boot, providing features like declarative transaction management, simplified configuration, and support for various database types. By using Hibernate with Spring Boot, developers can build robust, scalable, and maintainable applications with ease.

Features:

- `Lazy loading`

- `Caching mechanisms`

- `HQL (Hibernate Query Language)`

- Example:

  ```java
  @Entity
  public class User {
      @Id
      @GeneratedValue(strategy = GenerationType.IDENTITY)
      private Long id;
      private String name;
  }
  ```

&nbsp;

#### **JPA (Java Persistence API)**

Java Persistence API (JPA) is a specification for accessing, persisting, and managing data between Java objects and relational databases. It defines a set of interfaces and annotations that simplify database interactions and provide a standard way to perform CRUD (Create, Read, Update, Delete) operations

`A standard specification for ORM in Java`

`Defines a set of interfaces that ORM tools can implement`

`Allows for portability between different ORM implementations`

`Key components: EntityManager, JPQL (Java Persistence Query Language)`

Example:

```java
javaEntityManager em = entityManagerFactory.createEntityManager();
User user = em.find(User.class, 1L);
```

&nbsp;

#### **TopLink**

TopLink is an Object-Relational Mapping (ORM) framework that facilitates the mapping of Java objects to relational databases. Developed by Oracle, TopLink provides tools and APIs for database access, simplifying data manipulation and query operations within Java applications. It supports various database operations and allows developers to define mappings between Java classes and database tables through annotations or XML configurations.

`Oracle's proprietary ORM and persistence framework`

`Provides object-to-relational, object-to-XML, and EIS data access`

`Implements JPA specification`

Features

- `Caching`

- `Query optimization`

- `Distributed caching`

&nbsp;

#### DO (Java Data Objects)

Java Data Objects (JDO) is a specification for persistent storage of Java objects, providing a standardized API for transparent persistence. Unlike the Java Persistence API (JPA), which is mainly focused on relational databases, JDO is more general and can work with both relational and non-relational databases.

`Another Java specification for persistence`

`More general than JPA, can work with non-relational databases`

- `Features:`

  - `Transparent persistence`

  - `Database agnostic`

`Less popular than JPA in recent years`

&nbsp;

#### **OJB (ObJectRelationalB ridge)**

ObJectRelationalBridge (OJB) is an open-source Object-Relational Mapping (ORM) framework for Java. It facilitates the mapping of Java objects to relational database tables, enabling seamless database operations with Java objects. OJB provides a flexible and powerful mechanism to handle the persistence layer of applications, similar to other ORM tools like Hibernate and JDO.

`An Apache project for object-relational mapping`

`Provides persistence for Java objects against relational databases`

`Supports various databases and can be used with different APIs (JDO, ODMG, OTM)`

`Note: This project is no longer actively maintaine`

&nbsp;

#### **iBatis (now MyBatis)**

iBatis, now known as MyBatis, is a popular open-source persistence framework for Java. Unlike traditional Object-Relational Mapping (ORM) frameworks like Hibernate or JPA, MyBatis focuses on mapping SQL queries and results directly to Java objects. This approach offers more control over SQL execution and is particularly useful for applications that require complex or custom SQL operations.

`SQL mapper framework, different approach from full ORMs`

`Bridges objects and SQL statements using XML descriptors or annotations`

`Gives more control over SQL, beneficial for complex queries`

Example:

```xml
<select id="selectUser" resultType="User">
  SELECT * FROM users WHERE id = #{id}
</select>
```

In Java:

```java
User user = session.selectOne("selectUser", 1);
```

Key Differences:

Hibernate and TopLink are full-featured ORM solutions, while MyBatis is more of a SQL mapper.

JPA is a specification, while Hibernate is an implementation of JPA (among other things).

JDO is more general-purpose and can work with non-relational datastores, unlike JPA which is primarily for relational databases.

OJB and iBatis (original version) are older technologies, with MyBatis being the modern evolution of iBatis.

&nbsp;

### **AOP (Aspect-Oriented Programming)**

**Aspect-Oriented Programming (AOP)** is a programming paradigm that aims to increase modularity by allowing the separation of cross-cutting concerns. In simpler terms, AOP helps manage functionalities that cut across multiple points of an application, such as logging, security, and transaction management, without cluttering the core business logic.

#### Spring AOP

Spring AOP (Aspect-Oriented Programming) is part of the Spring Framework and allows you to implement cross-cutting concerns using aspects. Here’s a breakdown of its key features:

1. **Cross-cutting concerns**: These are functionalities that span multiple points of an application, such as logging, security, or transaction management. Instead of writing these functionalities in each class or method, you can define them separately and apply them where needed using aspects.

2. **Aspects**: An aspect is a module that encapsulates a cross-cutting concern. In Spring AOP, you can define aspects using regular Spring beans.

3. **Join points**: These are points in the execution of a program, such as method execution or exception handling, where an aspect can be applied.

4. **Pointcuts**: These are expressions that select one or more join points where an aspect should be applied. In Spring AOP, you can define pointcuts using expressions that match method signatures or annotations.

5. **Advice**: This is the action taken by an aspect at a particular join point. There are different types of advice, such as `before`, `after`, `around`, `after returning`, and `after throwing`.

#### AspectJ Integration

AspectJ is a more powerful and mature AOP framework that goes beyond the capabilities of Spring AOP. Spring provides integration with AspectJ to leverage its advanced features. Here are the main points of AspectJ integration:

1. **Compile-time weaving**: This means that the aspects are woven into the code during the compilation phase. This can result in more efficient code because the aspects are applied directly to the compiled bytecode.

2. **Load-time weaving**: This allows aspects to be woven into the code at runtime, which can be useful for dynamically adding aspects without needing to recompile the application.

3. **More powerful pointcut expressions**: AspectJ provides a richer syntax for defining pointcuts, allowing for more precise and flexible matching of join points.

4. **Inter-type declarations**: AspectJ allows you to add methods, fields, or interfaces to existing classes, enabling more advanced modularization.

#### Comparison

- **Spring AOP** is simpler to use and integrates seamlessly with the Spring Framework, making it suitable for most common use cases of AOP, such as method-level interceptors.

- **AspectJ** offers more advanced and powerful AOP features, suitable for complex scenarios where fine-grained control over the weaving process is required.

&nbsp;

### **JEE (Java Enterprise Edition)**

Java Enterprise Edition (JEE), formerly known as Java 2 Platform, Enterprise Edition (J2EE), is a platform that provides an API and runtime environment for developing and running large-scale, multi-tiered, reliable, and secure network applications. It is built on top of the Java Standard Edition (SE) platform and adds specifications for distributed computing and web services. JEE includes several APIs and tools for creating complex, component-based applications, including:

&nbsp;

#### Servlets

Servlets are Java programs that run on a web server to handle HTTP requests and responses. They are used to generate dynamic content, interact with databases, and process user input.

#### How It Works

- **Request Handling**: When a user sends a request (e.g., by clicking a link or submitting a form), the web server forwards this request to the appropriate servlet.

- **Processing**: The servlet processes the request, potentially interacting with databases or other services.

- **Response Generation**: The servlet generates a response, often in the form of HTML, which is sent back to the user's browser.

#### Example

```java
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("<html><body>");
        out.println("<h1>Hello, World!</h1>");
        out.println("</body></html>");
    }
}
```

In this example:

- The `@WebServlet` annotation maps the servlet to the URL pattern `/hello`.

- The `doGet` method handles GET requests. It sets the response type to HTML and sends a simple "Hello, World!" message to the browser.

&nbsp;

#### JavaServer Pages (JSP)

JSP allows embedding Java code within HTML pages. This makes it easy to create dynamic web content by combining static HTML with dynamic data generated by Java code.

#### How It Works

- **HTML Template**: JSP pages are essentially HTML pages with embedded Java code.

- **Compilation**: When a JSP page is requested, the server compiles it into a servlet.

- **Execution**: The generated servlet handles the request, executes the Java code, and generates the dynamic content.

#### Example

```html
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
  <head>
    <title>Hello JSP</title>
  </head>
  <body>
    <h1>Hello, JSP!</h1>
    <% String name = request.getParameter("name"); if (name != null) {
    out.println("
    <h2>Welcome, " + name + "!</h2>
    "); } %>
  </body>
</html>
```

In this example:

- The `@page` directive sets the content type and language.

- Java code within `<% ... %>` is executed on the server side. It checks if a `name` parameter is present in the request and displays a welcome message.

&nbsp;

#### Enterprise JavaBeans (EJBs)

EJBs encapsulate business logic in a modular and reusable way. They are designed for enterprise-level applications, providing services like transaction management, security, and concurrency.

#### Types of EJBs

- **Session Beans**: Handle business logic and can be stateless (do not maintain state) or stateful (maintain state across multiple method calls).

- **Message-Driven Beans**: Handle asynchronous processing of messages, typically from a JMS queue.

```java
@Stateless
public class OrderService {
    @PersistenceContext
    private EntityManager em;

    public void placeOrder(Order order) {
        em.persist(order);
    }
}
```

In this example:

- The `@Stateless` annotation defines a stateless session bean.

- The `EntityManager` is used to interact with the database, persisting the `Order` object.

&nbsp;

#### Java Persistence API (JPA)

JPA provides a standard way to manage relational data in Java applications using an object-relational mapping (ORM) approach.

#### How It Works

- **Entities**: Java classes annotated with JPA annotations represent database tables.

- **Entity Manager**: Manages the lifecycle of entity instances and interacts with the database.

```java
@Entity
public class Product {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String name;
    private Double price;

    // Getters and setters
}

public class ProductService {
    @PersistenceContext
    private EntityManager em;

    public void addProduct(Product product) {
        em.persist(product);
    }

    public Product findProduct(Long id) {
        return em.find(Product.class, id);
    }
}
```

In this example:

- The `Product` class is an entity mapped to a database table.

- The `ProductService` uses an `EntityManager` to persist and find `Product` instances.

&nbsp;

#### Java Transaction API (JTA)

JTA manages transactions across multiple resources, ensuring that a series of operations either all succeed or all fail, maintaining data consistency.

#### How It Works

- **Begin Transaction**: A transaction is started.

- **Commit/Rollback**: If all operations succeed, the transaction is committed; if any operation fails, the transaction is rolled back.

```java
@Stateless
public class BankingService {
    @Resource
    private UserTransaction utx;

    public void transferFunds(Account from, Account to, double amount) {
        try {
            utx.begin();
            from.debit(amount);
            to.credit(amount);
            utx.commit();
        } catch (Exception e) {
            utx.rollback();
            throw new RuntimeException("Transfer failed", e);
        }
    }
}
```

In this example:

- The `UserTransaction` is used to manage the transaction.

- Funds are transferred between accounts, and the transaction is either committed or rolled back based on the success of the operations.

&nbsp;

#### Java Messaging Service (JMS)

JMS facilitates asynchronous communication between different components of an application through messages.

#### How It Works

- **Producers**: Send messages to a destination (queue or topic).

- **Consumers**: Receive messages from the destination.

```java
@Stateless
public class MessageProducerService {
    @Resource(mappedName = "java:/jms/queue/TestQueue")
    private Queue queue;

    @Inject
    private JMSContext context;

    public void sendMessage(String message) {
        context.createProducer().send(queue, message);
    }
}

@MessageDriven(mappedName = "java:/jms/queue/TestQueue")
public class MessageConsumerBean implements MessageListener {
    public void onMessage(Message message) {
        try {
            String text = ((TextMessage) message).getText();
            System.out.println("Received message: " + text);
        } catch (JMSException e) {
            e.printStackTrace();
        }
    }
}
```

In this example:

- The `MessageProducerService` sends messages to a JMS queue.

- The `MessageConsumerBean` receives messages from the queue and processes them.

&nbsp;

#### JavaMail

JavaMail provides an API for sending and receiving email from within Java applications.

#### How It Works

- **Session**: Represents a mail session with properties and authentication.

- **Message**: Represents the email message to be sent.

```java
public class EmailService {
    public void sendEmail(String to, String subject, String body) {
        Properties properties = new Properties();
        properties.put("mail.smtp.host", "smtp.example.com");
        properties.put("mail.smtp.port", "25");

        Session session = Session.getDefaultInstance(properties, null);

        try {
            Message message = new MimeMessage(session);
            message.setFrom(new InternetAddress("no-reply@example.com"));
            message.setRecipients(Message.RecipientType.TO, InternetAddress.parse(to));
            message.setSubject(subject);
            message.setText(body);

            Transport.send(message);
            System.out.println("Email sent successfully");
        } catch (MessagingException e) {
            e.printStackTrace();
        }
    }
}
```

In this example:

- An `EmailService` class is created to send emails.

- SMTP properties are set, and an email message is created and sent using the `Transport` class.

&nbsp;

#### Summary

- **Servlets** handle HTTP requests and generate dynamic content.

- **JSP** allows embedding Java code in HTML for the presentation layer.

- **EJBs** encapsulate business logic and manage enterprise-level tasks.

- **JPA** standardizes database access using Java objects.

- **JTA** manages transactions across multiple resources for data consistency.

- **JMS** facilitates asynchronous communication between components.

- **JavaMail** integrates email capabilities into applications.

&nbsp;

### **Web**

&nbsp;

#### Spring Web MVC

#### Purpose

Spring Web MVC is a Model-View-Controller framework for building web applications. It helps separate the application logic, user interface, and data.

#### How It Works

- **Model**: Represents the data and the business logic.

- **View**: Represents the presentation layer (UI).

- **Controller**: Handles user input and updates the model.

#### Example

```java
@Controller
public class HelloController {
    @RequestMapping("/hello")
    public ModelAndView helloWorld() {
        String message = "Hello, Spring MVC!";
        return new ModelAndView("hello", "message", message);
    }
}
```

In this example:

- `@Controller` denotes a controller class.

- `@RequestMapping("/hello")` maps the `/hello` URL to the `helloWorld` method.

- The `ModelAndView` object specifies the view name (`hello`) and the model data (`message`).

####

**_Spring Web MVC allows integration with other web frameworks to leverage their specific features._**

&nbsp;

#### Struts

Struts is an open-source web application framework for developing Java EE web applications. It follows the Model-View-Controller (MVC) design pattern and has been widely used for building enterprise-level applications.

Spring provides integration with Struts to leverage Spring’s powerful features like dependency injection, transaction management, and more, while still using Struts as the web layer framework.

Example Integration:

```xml
<bean id="strutsController" class="org.springframework.web.struts.DelegatingRequestProcessor">
    <property name="applicationContext" ref="applicationContext"/>
</bean>
```

In this example:

- The `DelegatingRequestProcessor` integrates Spring with the Struts framework.

&nbsp;

#### WebWork

WebWork, now part of Apache Struts (Struts 2), is a web application framework for Java that follows the Model-View-Controller (MVC) design pattern. It simplifies the development of web applications by separating concerns of the application into different components: Model (data), View (presentation), and Controller (control logic).

Example Integration:

```xml
<bean id="webWorkController" class="org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping">
    <property name="alwaysUseFullPath" value="true"/>
</bean>
```

In this example:

- The `DefaultAnnotationHandlerMapping` integrates Spring with WebWork.

&nbsp;

#### Tapestry

**Tapestry** is a component-based framework for creating dynamic, robust, highly scalable web applications in Java. Unlike traditional MVC frameworks, Tapestry uses a component-based approach, where web pages are composed of reusable components.

Define Spring's `ContextLoaderListener` in your `web.xml` to initialize the Spring application context:

```xml
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>

<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/applicationContext.xml</param-value>
</context-param>
```

&nbsp;

#### JSF (JavaServer Faces)

**JavaServer Faces (JSF)** is a Java specification for building component-based user interfaces for web applications. It provides a powerful component model and a lifecycle for managing UI components and handling user events.

Integrating JSF with Spring allows you to leverage Spring's powerful features like dependency injection and transaction management within JSF managed beans.

Define Spring’s `ContextLoaderListener` in your `web.xml` to initialize the Spring application context:

```xml
<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>

<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/applicationContext.xml</param-value>
</context-param>
```

&nbsp;

**PDF Support**

Spring supports generating PDF documents using libraries like iText or Apache PDFBox.

```java
@Controller
public class PdfController {
    @RequestMapping("/pdf")
    public ModelAndView generatePdf() {
        return new ModelAndView("pdfView", "message", "Hello, PDF!");
    }
}

public class PdfView extends AbstractPdfView {
    @Override
    protected void buildPdfDocument(Map<String, Object> model, Document document, PdfWriter writer, HttpServletRequest request, HttpServletResponse response) throws Exception {
        String message = (String) model.get("message");
        document.add(new Paragraph(message));
    }
}
```

In this example:

- `PdfController` handles requests and generates a PDF view.

- `PdfView` extends `AbstractPdfView` to create the PDF document.

&nbsp;

#### Excel Support

Spring supports generating Excel spreadsheets using libraries like Apache POI.

```java
@Controller
public class ExcelController {
    @RequestMapping("/excel")
    public ModelAndView generateExcel() {
        return new ModelAndView("excelView", "message", "Hello, Excel!");
    }
}

public class ExcelView extends AbstractXlsView {
    @Override
    protected void buildExcelDocument(Map<String, Object> model, Workbook workbook, HttpServletRequest request, HttpServletResponse response) throws Exception {
        String message = (String) model.get("message");
        Sheet sheet = workbook.createSheet("Spring");
        Row row = sheet.createRow(0);
        Cell cell = row.createCell(0);
        cell.setCellValue(message);
    }
}
```

In this example:

- `ExcelController` handles requests and generates an Excel view.

- `ExcelView` extends `AbstractXlsView` to create the Excel spreadsheet.

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBZGV2LWRvY3MtY29sbGVjdGlvbiUzQSUzQWFycGl0cGFyZWto" repo-name="dev-docs-collection"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
