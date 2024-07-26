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

&nbsp;

The statement `Class.forName("com.mysql.cj.jdbc.Driver");` is used in JDBC (Java Database Connectivity) to load the JDBC driver class into the JVM.

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

&nbsp;

#### Connection

A `Connection` object represents a physical connection with a database. It is obtained from the `DriverManager` class and is used to create statements and manage the connection to the database.

**Example:**

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class DatabaseConnectionExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";

        try (Connection connection = DriverManager.getConnection(url, username, password)) {
            System.out.println("Connected to the database successfully.");
        } catch (SQLException e) {
            System.err.println("Failed to connect to the database: " + e.getMessage());
        }
    }
}
```

#### Statement

A `Statement` object is used to execute SQL queries. It is created from a `Connection` object. Statements are suitable for executing simple SQL queries but are less efficient for batch updates or inserts compared to `PreparedStatement`.

**Example:**

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;

public class ExecuteSQLQueryExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";
        String query = "CREATE TABLE IF NOT EXISTS employees (id INT PRIMARY KEY, name VARCHAR(100))";

        try (Connection connection = DriverManager.getConnection(url, username, password);
             Statement statement = connection.createStatement()) {

            statement.executeUpdate(query);
            System.out.println("Table created successfully.");

        } catch (SQLException e) {
            System.err.println("Failed to execute query: " + e.getMessage());
        }
    }
}
```

#### ResultSet

A `ResultSet` object represents the result of a database query. It contains the data returned by the query. You can navigate through the rows of the `ResultSet` using the `next()` method and retrieve column values using getter methods like `getInt()`, `getString()`, etc.

**Example:**

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class QueryDatabaseExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";
        String query = "SELECT * FROM employees";

        try (Connection connection = DriverManager.getConnection(url, username, password);
             Statement statement = connection.createStatement();
             ResultSet resultSet = statement.executeQuery(query)) {

            while (resultSet.next()) {
                int id = resultSet.getInt("id");
                String name = resultSet.getString("name");
                System.out.println("ID: " + id + ", Name: " + name);
            }

        } catch (SQLException e) {
            System.err.println("Failed to execute query: " + e.getMessage());
        }
    }
}
```

#### PreparedStatement

The `PreparedStatement` interface in JDBC is used when you plan to execute the same SQL statement repeatedly with high efficiency and/or when you need to prevent SQL injection attacks. Unlike the `Statement` interface, which executes SQL statements statically, `PreparedStatement` allows you to compile the SQL statement once and reuse it with different parameters, improving performance. Additionally, `PreparedStatement` helps in preventing SQL injection attacks by automatically escaping special characters in the input parameters.

**Example:**

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class UpdateEmployeeAge {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";
        String sql = "UPDATE Employees SET age = ? WHERE id = ?";

        try (Connection connection = DriverManager.getConnection(url, username, password);
             PreparedStatement preparedStatement = connection.prepareStatement(sql)) {

            preparedStatement.setInt(1, 30); // Sets the age to 30
            preparedStatement.setInt(2, 1); // Sets the employee ID to 1

            int rowsAffected = preparedStatement.executeUpdate(); // Executes the update
            System.out.println(rowsAffected + " record(s) updated.");
        } catch (SQLException e) {
            System.err.println("Error updating record: " + e.getMessage());
        }
    }
}

```

#### CallableStatement

The `CallableStatement` interface is used to call stored procedures in a database. It extends `PreparedStatement` and allows you to pass parameters to the stored procedure and retrieve results from it. Stored procedures are precompiled SQL routines stored in the database, which can perform complex operations and return results.

**Example:**

```java
import java.sql.CallableStatement;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Types;

public class CallStoredProc {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/mydatabase";
        String username = "root";
        String password = "password";
        String procName = "{call multiply(? , ?)}";

        try (Connection connection = DriverManager.getConnection(url, username, password)) {
            CallableStatement callableStatement = connection.prepareCall(procName);

            callableStatement.setInt(1, 5); // Passes 5 as the first parameter
            callableStatement.setInt(2, 10); // Passes 10 as the second parameter

            callableStatement.registerOutParameter(3, Types.INTEGER); // Registers the OUT parameter
            callableStatement.execute(); // Calls the stored procedure

            int result = callableStatement.getInt(3); // Retrieves the result
            System.out.println("Result: " + result);
        } catch (SQLException e) {
            System.err.println("Error calling stored procedure: " + e.getMessage());
        }
    }
}
```

#### ResultSetMetaData

`ResultSetMetaData` is an interface in the `java.sql` package of JDBC API, designed to provide metadata about a `ResultSet` object. Metadata refers to data about data, meaning it offers further information extracted from the data itself. When querying a database using a SELECT statement, the results are stored in a `ResultSet` object. Each `ResultSet` object is associated with a single `ResultSetMetaData` object, which contains metadata about the `ResultSet`, such as the schema name, table name, number of columns, column names, and data types of the columns.

Example:

```java
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.Statement;
import java.sql.ResultSetMetaData;

public class ResultSetMetaDataExample {
    public static void main(String[] args) {
        try {
            // Establish a connection to the database
            Connection con = DriverManager.getConnection("jdbc:mysql://localhost:3306/yourDatabase", "username", "password");

            // Create a Statement object
            Statement stmt = con.createStatement();

            // Execute a query and store the result in a ResultSet object
            ResultSet rs = stmt.executeQuery("SELECT * FROM yourTable");

            // Retrieve the ResultSetMetaData object
            ResultSetMetaData rsmd = rs.getMetaData();

            // Print the number of columns
            System.out.println("Number of columns: " + rsmd.getColumnCount());

            // Iterate over each column and print its details
            for (int i = 1; i <= rsmd.getColumnCount(); i++) {
                System.out.println("Column Name: " + rsmd.getColumnName(i));
                System.out.println("Data Type: " + rsmd.getColumnTypeName(i));
                System.out.println("Table Name: " + rsmd.getTableName(i));
                System.out.println("--------------------");
            }

            // Close resources
            rs.close();
            stmt.close();
            con.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

#### DatabaseMetaData

DatabaseMetaData is another interface in the JDBC API that provides comprehensive information about the entire database, rather than just a specific result set. It offers methods to retrieve metadata about the database as a whole, its structure, and capabilities.

```java
import java.sql.*;

public class DatabaseMetaDataExample {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/your_database";
        String user = "your_username";
        String password = "your_password";

        try (Connection conn = DriverManager.getConnection(url, user, password)) {
            DatabaseMetaData metaData = conn.getMetaData();

            // Print basic database information
            System.out.println("Database Product Name: " + metaData.getDatabaseProductName());
            System.out.println("Database Product Version: " + metaData.getDatabaseProductVersion());
            System.out.println("Driver Name: " + metaData.getDriverName());
            System.out.println("Driver Version: " + metaData.getDriverVersion());

            // List all tables
            String[] types = {"TABLE"};
            ResultSet rs = metaData.getTables(null, null, "%", types);
            
            System.out.println("\nTables in the database:");
            while (rs.next()) {
                String tableName = rs.getString("TABLE_NAME");
                String tableType = rs.getString("TABLE_TYPE");
                System.out.println(tableName + " (" + tableType + ")");
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}
```

Store an image in a MySQL database and then retrieve it using JDBC. This example uses a BLOB (Binary Large Object) to store the image data.

First, let's create a table in MySQL to store the images:

```sql
CREATE TABLE images (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255),
    data LONGBLOB
);
```

Now, here's the Java code to store and retrieve images:

```java
import java.sql.*;
import java.io.*;
import javax.swing.JFileChooser;

public class ImageDatabaseExample {
    // JDBC URL, username and password of MySQL server
    private static final String URL = "jdbc:mysql://localhost:3306/your_database";
    private static final String USER = "your_username";
    private static final String PASSWORD = "your_password";

    public static void main(String[] args) {
        try {
            // Store image
            storeImage();
            
            // Retrieve image
            retrieveImage();
        } catch (SQLException | IOException e) {
            e.printStackTrace();
        }
    }

    public static void storeImage() throws SQLException, IOException {
        String sql = "INSERT INTO images (name, data) VALUES (?, ?)";
        
        JFileChooser fileChooser = new JFileChooser();
        fileChooser.showOpenDialog(null);
        File file = fileChooser.getSelectedFile();
        
        try (Connection conn = DriverManager.getConnection(URL, USER, PASSWORD);
             PreparedStatement pstmt = conn.prepareStatement(sql)) {
            
            pstmt.setString(1, file.getName());
            
            FileInputStream fis = new FileInputStream(file);
            pstmt.setBinaryStream(2, fis, file.length());
            
            pstmt.executeUpdate();
            System.out.println("Image stored successfully!");
        }
    }

    public static void retrieveImage() throws SQLException, IOException {
        String sql = "SELECT name, data FROM images WHERE id = ?";
        
        try (Connection conn = DriverManager.getConnection(URL, USER, PASSWORD);
             PreparedStatement pstmt = conn.prepareStatement(sql)) {
            
            pstmt.setInt(1, 1); // Assuming we want to retrieve the image with id = 1
            
            try (ResultSet rs = pstmt.executeQuery()) {
                if (rs.next()) {
                    String name = rs.getString("name");
                    InputStream inputStream = rs.getBinaryStream("data");
                    FileOutputStream fos = new FileOutputStream("retrieved_" + name);
                    
                    byte[] buffer = new byte[1024];
                    while (inputStream.read(buffer) > 0) {
                        fos.write(buffer);
                    }
                    System.out.println("Image retrieved successfully: retrieved_" + name);
                }
            }
        }
    }
}
```

<SwmMeta version="3.0.0" repo-id="Z2l0aHViJTNBJTNBZGV2LWRvY3MtY29sbGVjdGlvbiUzQSUzQWFycGl0cGFyZWto" repo-name="dev-docs-collection"><sup>Powered by [Swimm](https://app.swimm.io/)</sup></SwmMeta>
