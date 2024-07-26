---
title: 'Java Database Connectivity (JDBC) '
---
**JDBC (Java Database Connectivity)** is a Java API that enables Java applications to interact with databases. It provides a standard interface for connecting to relational databases and executing SQL queries and updates.

#### Purpose:

- To establish a connection with a database
- To send SQL statements to the database
- To process the results returned by the database

**Key Components:**

a) JDBC Drivers:

- JDBC-ODBC Bridge Driver (legacy, not recommended)
- Native-API Driver (partly Java, partly native code)
- Network Protocol Driver (pure Java, uses middleware)
- Thin Driver (pure Java, directly converts JDBC calls into database-specific protocol)

&nbsp;b) Core Interfaces:

- **Driver**: Manages database connections
- **Connection**: Represents a connection to the database
- **Statement**: Used to execute SQL queries
- **PreparedStatement**: Precompiled SQL statement
- **CallableStatement**: Used to execute stored procedures
- **ResultSet**: Represents the result set of a query

&nbsp;c) DriverManager:&nbsp;

Manages a list of database drivers and handles establishing a connection

![](/.swm/images/JDBC_Cycle-2024-6-26-8-53-1-599.png)

Basic Steps for JDBC Operations:&nbsp;

`Load the JDBC driver `

`Establish a connection to the database `

`Create a Statement object `

`Execute SQL queries or updates `

`Process the results (for queries) `

`Close the connection`

To connect java application with the mysql database, **mysqlconnector.jar** file is required to be loaded.

```java
import java.sql.*;

public class JDBCExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydb";
        String user = "username";
        String password = "password";

        try {
            // 1. Load the JDBC driver
            Class.forName("com.mysql.jdbc.Driver");

            // 2. Establish the connection
            Connection conn = DriverManager.getConnection(url, user, password);

            // 3. Create a statement
            Statement stmt = conn.createStatement();

            // 4. Execute a query
            String sql = "SELECT * FROM users";
            ResultSet rs = stmt.executeQuery(sql);

            // 5. Process the results
            while (rs.next()) {
                int id = rs.getInt("id");
                String name = rs.getString("name");
                System.out.println("ID: " + id + ", Name: " + name);
            }

            // 6. Close resources
            rs.close();
            stmt.close();
            conn.close();
        } catch (SQLException se) {
            se.printStackTrace();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

Benefits of JDBC:&nbsp;

`Platform Independence: Works across different databases with minimal code changes`

`Simplifies Database Programming: Provides a consistent way to work with different databases`

`Leverages Java Features: Exception handling, multithreading, etc.`

&nbsp;

#### **Driver Manager**

The DriverManager class in Java is a crucial component of the Java Database Connectivity (JDBC) API, serving as the backbone for managing JDBC drivers within an application. It plays a pivotal role in establishing connections between a Java application and a database. Here's a comprehensive explanation of its functionality, usage, and key methods based on the provided sources.

- **Purpose**: The DriverManager class manages a list of database drivers available to an application. It acts as an intermediary between the application and the drivers, facilitating the establishment of connections to databases.
- **Singleton Nature**: It is a singleton class, meaning only one instance of this class exists within a JVM at any time.
- **Thread Safety**: The DriverManager class is not thread-safe, implying that care must be taken when using it in multi-threaded environments.

#### Key Methods of DriverManager class

- **registerDriver(Driver driver)**: Registers a new JDBC driver with the DriverManager. This is typically called by the driver itself upon initialization.

```java
import java.sql.Driver;
import java.sql.DriverManager;
import java.sql.SQLException;

public class RegisterDriverExample {

    public static void main(String[] args) {
        try {
            // Assuming MyDriver is a custom JDBC driver
            Driver myDriver = new MyDriver();
            DriverManager.registerDriver(myDriver);
            System.out.println("Driver registered successfully.");
        } catch (SQLException e) {
            System.err.println("Failed to register driver: " + e.getMessage());
        }
    }
}
```

- **deregisterDriver(Driver driver)**: Removes a previously registered driver from the DriverManager.

```java
import java.sql.Driver;
import java.sql.DriverManager;
import java.sql.SQLException;

public class DeregisterDriverExample {

    public static void main(String[] args) {
        try {
            // Assuming MyDriver is a custom JDBC driver
            Driver myDriver = new MyDriver();
            DriverManager.deregisterDriver(myDriver);
            System.out.println("Driver deregistered successfully.");
        } catch (SQLException e) {
            System.err.println("Failed to deregister driver: " + e.getMessage());
        }
    }
}
```

- **getConnection(String url)**: Establishes a connection to a database using the provided URL. The DriverManager selects a suitable driver from those registered and attempts to establish a connection.


- **getConnection(String url, String userName, String password)**: Similar to the above, but also accepts username and password for databases requiring authentication.

  ```java
  import java.sql.Connection;
  import java.sql.DriverManager;
  import java.sql.SQLException;
  
  public class ConnectDatabaseExample {
  
      public static void main(String[] args) {
          try {
              String url = "jdbc:mysql://localhost:3306/mydatabase";
              String username = "myusername";
              String password = "mypassword";
              Connection connection = DriverManager.getConnection(url, username, password);
              System.out.println("Connected successfully!");
              connection.close(); // Always remember to close the connection
          } catch (SQLException e) {
              System.err.println("Failed to connect to database: " + e.getMessage());
          }
      }
  }
  ```

- **getDriver(String url)**: Returns a Driver object for the URL if a suitable driver is found among those registered with the DriverManager.

```java
import java.sql.Driver;
import java.sql.DriverManager;
import java.sql.SQLException;

public class GetDriverExample {

    public static void main(String[] args) {
        try {
            String url = "jdbc:mysql://localhost:3306/exampleDB"; // Example URL
            Driver driver = DriverManager.getDriver(url);
            if (driver == null) {
                System.out.println("No driver found for URL: " + url);
            } else {
                System.out.println("Found driver: " + driver.getClass().getName());
            }
        } catch (SQLException e) {
            System.err.println("Failed to get driver: " + e.getMessage());
        }
    }
}
```

- **getLoginTimeout() and setLoginTimeout(int seconds)**: Controls the maximum time drivers will wait for a database request to complete.

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class SetLoginTimeoutExample {

    public static void main(String[] args) {
        try {
            // Set login timeout to 5 seconds
            DriverManager.setLoginTimeout(5);
            
            // Attempt to connect to the database
            String url = "jdbc:mysql://localhost:3306/exampleDB";
            String username = "dbUser";
            String password = "dbPassword";
            Connection connection = DriverManager.getConnection(url, username, password);
            
            System.out.println("Connected successfully!");
            connection.close(); // Always remember to close the connection
        } catch (SQLException e) {
            System.err.println("Failed to connect to database: " + e.getMessage());
        }
    }
}
```

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBZGV2LWRvY3MtY29sbGVjdGlvbiUzQSUzQWFycGl0cGFyZWto" repo-name="dev-docs-collection"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
