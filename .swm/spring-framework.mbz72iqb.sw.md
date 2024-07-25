---
title: Spring Framework
---
Spring is an open-source application framework for the Java platform. It provides comprehensive infrastructure support for developing **Java applications, particularly web applications**.

The spring framework is a **layered architecture** which consists of several modules. all modules are built on the top of its core container. these modules provide everything that a developer may need for use in the enterprise application development. he is always free to choose what features he needs and eliminate the modules which are of no use. it's **modular architecture** enables integration with other frameworks without much hassle.

| Core<br><br>The IoC container | **AOP**<br><br>Spring AOP<br>AspectJ integration | DAO<br><br>Spring JDBC<br>Transaction management | ORM<br><br>Hibernate<br>JPA<br>TopLink<br>JDO<br>OJB<br>iBatis | JEE<br><br>JMX<br>JMS<br>JCA<br>Remoting<br>EJBs<br>Email | Web<br><br>Spring Web MVC<br>Framework Integration<br>Struts<br>WebWork<br>Tapestry<br>JSF<br>Rich View Support<br>JSPs<br>Velocity<br>FreeMarker<br>PDF<br>Excel<br>Jasper Reports<br>Spring Portlet MVC |
| :---------------------------- | :----------------------------------------------- | :----------------------------------------------- | :------------------------------------------------------------- | :-------------------------------------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

&nbsp;

### **Core: The IoC container** 

- This is the foundation of the Spring Framework.

- IoC (Inversion of Control) container manages the lifecycle of beans and handles dependency injection

#### **IoC container** 

Instead of the application controlling the flow of the program and making calls to reusable libraries, the framework controls the flow and calls into the custom code.

The Spring IoC container is responsible for instantiating, configuring, and assembling objects known as beans. It manages the lifecycle of these beans and injects dependencies between them.

Decoupling: Reduces dependencies between components.

Modularity: Makes the application more modular and easier to manage.

Testability: Easier to unit test components in isolation.&nbsp;

Flexibility: Easier to switch implementations without changing the code. It's responsible for creating, configuring, and managing application objects.

#### Implementation in Spring:&nbsp;

XML Configuration: Beans and their dependencies are defined in XML files.

Annotation-based Configuration: Using annotations like @Autowired, @Component, etc.

Java-based Configuration: Using @Configuration and @Bean annotations.

### **DAO (Data Access Object):** 

- Spring JDBC: Simplifies JDBC database operations, reducing boilerplate code.

- Transaction management: Provides a consistent programming model for handling database transactions across different transaction APIs.

### **ORM (Object-Relational Mapping):** 

- Hibernate: Popular ORM tool for mapping Java objects to database tables.

- JPA (Java Persistence API): Standard Java specification for ORM.

- TopLink: Oracle's ORM and persistence framework.

- JDO (Java Data Objects): Another Java specification for persistence.

- OJB (ObJectRelationalBridge): Apache project for object-relational mapping.

- iBatis (now MyBatis): SQL mapper framework.

**AOP (Aspect-Oriented Programming):** 

- Spring AOP: Implements cross-cutting concerns like logging, security, and transactions.

- AspectJ integration: Provides more powerful AOP capabilities, allowing for compile-time weaving.

**JEE (Java Enterprise Edition):** 

- JMX (Java Management Extensions): For managing and monitoring applications.

- JMS (Java Message Service): API for sending messages between clients.

- JCA (Java Connector Architecture): For connecting Java applications to enterprise information systems.

- Remoting: Supports various remoting technologies for distributed systems.

- EJBs (Enterprise JavaBeans): Component architecture for building modular enterprise applications.

- Email: Simplifies sending emails from Java applications.

**Web:** 

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

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBZGV2LWRvY3MtY29sbGVjdGlvbiUzQSUzQWFycGl0cGFyZWto" repo-name="dev-docs-collection"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
