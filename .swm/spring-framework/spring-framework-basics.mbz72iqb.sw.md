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

In Spring Framework, the primary **type of Inversion of Control (IoC)** used is Dependency Injection (DI). However, Spring also supports some other IoC patterns to a lesser extent. Let's break down the types of IoC used in Spring:

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

&nbsp;

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

- Spring AOP: Implements cross-cutting concerns like logging, security, and transactions.

- AspectJ integration: Provides more powerful AOP capabilities, allowing for compile-time weaving.

&nbsp;

### **JEE (Java Enterprise Edition)**

Java Enterprise Edition (JEE), formerly known as Java 2 Platform, Enterprise Edition (J2EE), is a platform that provides an API and runtime environment for developing and running large-scale, multi-tiered, reliable, and secure network applications. It is built on top of the Java Standard Edition (SE) platform and adds specifications for distributed computing and web services. JEE includes several APIs and tools for creating complex, component-based applications, including:

- `Servlets: For handling HTTP requests and generating dynamic content.`

- `JavaServer Pages (JSP): For embedding Java code within HTML pages.`

- `Enterprise JavaBeans (EJBs): For encapsulating business logic.`

- `Java Persistence API (JPA): For accessing databases in a standardized way.`

- `Java Transaction API (JTA): For managing transactions across multiple resources.`

- `Java Messaging Service (JMS): For asynchronous communication between components.`

- `JavaMail: For integrating email capabilities into applications.`

&nbsp;

&nbsp;

### **Web**

- Spring Web MVC: Model-View-Controller framework for building web applications.

- Framework Integration: Allows integration with other web frameworks.

- Struts: Integration with Apache Struts web framework.

- WebWork: Integration with WebWork framework (now part of Apache Struts).

- Tapestry: Integration with Apache Tapestry web framework.

- JSF (JavaServer Faces): Integration with JSF for building user interfaces.

- Rich View Support: Supports various view technologies.

- JSPs (JavaServer Pages): For creating dynamically generated web pages.

- Velocity: Integration with Apache Velocity template engine.

- FreeMarker: Another template engine for generating text output.

- PDF: Support for generating PDF documents.

- Excel: Support for generating Excel spreadsheets.

- Jasper Reports: Integration for report generation.

- Spring Portlet MVC: MVC framework specifically for portlet environments.

&nbsp;

&nbsp;

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBZGV2LWRvY3MtY29sbGVjdGlvbiUzQSUzQWFycGl0cGFyZWto" repo-name="dev-docs-collection"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
