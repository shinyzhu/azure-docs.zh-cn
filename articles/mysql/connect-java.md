---
title: 使用 Java 进行连接 - Azure Database for MySQL
description: 本快速入门提供了一个 Java 代码示例，你可以使用它来连接到 Azure Database for MySQL 数据库并查询其中的数据。
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devcenter, seo-java-july2019, seo-java-august2019
ms.topic: quickstart
ms.devlang: java
ms.date: 3/18/2020
ms.openlocfilehash: b5f1cbf2f822f350b1eeba032199676651364d84
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80983817"
---
# <a name="quickstart-use-java-to-connect-to-and-query-data-in-azure-database-for-mysql"></a>快速入门：使用 Java 连接到 Azure Database for MySQL 并查询其中的数据

在本快速入门中，你将使用 Java 应用程序和 JDBC 驱动程序 MariaDB Connector/J 连接到 Azure Database for MySQL。 然后使用 SQL 语句在 Mac、Ubuntu Linux 和 Windows 平台的数据库中查询、插入、更新和删除数据。 

本主题假设你熟悉如何使用 Java 进行开发，但不熟悉 Azure Database for MySQL 的用法。

## <a name="prerequisites"></a>先决条件

- 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
- Azure Database for MySQL 服务器。 [使用 Azure 门户创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md)或[使用 Azure CLI 创建 Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-cli.md)。
- Azure Database for MySQL 连接安全性配置为打开防火墙，并为应用程序配置了 SSL 连接设置。

## <a name="obtain-the-mariadb-connector"></a>获取 MariaDB 连接器

使用以下方法之一获取 [MariaDB Connector/J](https://mariadb.com/kb/en/library/mariadb-connector-j/) 连接器：
   - 使用 Maven 包 [mariadb-java-client](https://search.maven.org/search?q=a:mariadb-java-client) 在项目的 POM 文件中包括 [mariadb-java-client 依赖项](https://mvnrepository.com/artifact/org.mariadb.jdbc/mariadb-java-client)。
   - 下载 JDBC 驱动程序 [MariaDB Connector/J](https://downloads.mariadb.org/connector-java/)，并将 JDBC jar 文件（例如 mariadb-java-client-2.4.3.jar）包含到应用程序类路径中。 请参阅环境文档（例如 [Apache Tomcat](https://tomcat.apache.org/tomcat-7.0-doc/class-loader-howto.html) 或 [Java SE](https://docs.oracle.com/javase/7/docs/technotes/tools/windows/classpath.html)），了解类路径具体信息

## <a name="get-connection-information"></a>获取连接信息

获取连接到 Azure Database for MySQL 所需的连接信息。 需要完全限定的服务器名称和登录凭据。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在 Azure 门户的左侧菜单中，选择“所有资源”  ，然后搜索已创建的服务器（例如 mydemoserver  ）。
3. 选择服务器名称。
4. 从服务器的“概览”面板中记下“服务器名称”和“服务器管理员登录名”。    如果忘记了密码，也可通过此面板来重置密码。
 ![Azure Database for MySQL 服务器名称](./media/connect-java/azure-database-mysql-server-name.png)

## <a name="connect-create-table-and-insert-data"></a>进行连接，创建表，然后插入数据

通过以下代码进行连接，然后使用函数和 **INSERT** SQL 语句加载数据。 [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) 方法用于连接到 MySQL。 [createStatement()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#creating-a-table-on-a-mariadb-or-mysql-server) 和 execute() 方法用于删除和创建表。 使用 prepareStatement 对象来生成 insert 命令，通过 setString() 和 setInt() 来绑定参数值。 executeUpdate() 方法为每组参数运行该命令，以便插入值。 

将 host、database、user 和 password 参数替换为创建你自己的服务器和数据库时指定的值。

```java
import java.sql.*;
import java.util.Properties;

public class CreateTableInsertRows {

    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables. 
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);

            // Set connection properties.
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");

            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database.", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Drop previous table of same name if one exists.
                Statement statement = connection.createStatement();
                statement.execute("DROP TABLE IF EXISTS inventory;");
                System.out.println("Finished dropping table (if existed).");
    
                // Create table.
                statement.execute("CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);");
                System.out.println("Created table.");
                
                // Insert some data into table.
                int nRowsInserted = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("INSERT INTO inventory (name, quantity) VALUES (?, ?);");
                preparedStatement.setString(1, "banana");
                preparedStatement.setInt(2, 150);
                nRowsInserted += preparedStatement.executeUpdate();

                preparedStatement.setString(1, "orange");
                preparedStatement.setInt(2, 154);
                nRowsInserted += preparedStatement.executeUpdate();

                preparedStatement.setString(1, "apple");
                preparedStatement.setInt(2, 100);
                nRowsInserted += preparedStatement.executeUpdate();
                System.out.println(String.format("Inserted %d row(s) of data.", nRowsInserted));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
    
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}

```

## <a name="read-data"></a>读取数据

使用以下代码，通过 **SELECT** SQL 语句读取数据。 [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) 方法用于连接到 MySQL。 使用 [createStatement()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#creating-a-table-on-a-mariadb-or-mysql-server) 和 executeQuery() 方法来连接和运行 select 语句。 使用 ResultSet 对象处理结果。 

将 host、database、user 和 password 参数替换为创建你自己的服务器和数据库时指定的值。

```java
import java.sql.*;
import java.util.Properties;

public class ReadTable {

    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables.
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);

            // Set connection properties.
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");
            
            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
    
                Statement statement = connection.createStatement();
                ResultSet results = statement.executeQuery("SELECT * from inventory;");
                while (results.next())
                {
                    String outputString = 
                        String.format(
                            "Data row = (%s, %s, %s)",
                            results.getString(1),
                            results.getString(2),
                            results.getString(3));
                    System.out.println(outputString);
                }
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database."); 
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="update-data"></a>更新数据

使用以下代码，通过 **UPDATE** SQL 语句更改数据。 [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) 方法用于连接到 MySQL。 使用 [prepareStatement()](https://docs.oracle.com/javase/tutorial/jdbc/basics/prepared.html) 和 executeUpdate() 方法来准备和运行 update 语句。 

将 host、database、user 和 password 参数替换为创建你自己的服务器和数据库时指定的值。

```java
import java.sql.*;
import java.util.Properties;

public class UpdateTable {
    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables. 
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);
            
            // set up the connection properties
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");

            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database.", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Modify some data in table.
                int nRowsUpdated = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("UPDATE inventory SET quantity = ? WHERE name = ?;");
                preparedStatement.setInt(1, 200);
                preparedStatement.setString(2, "banana");
                nRowsUpdated += preparedStatement.executeUpdate();
                System.out.println(String.format("Updated %d row(s) of data.", nRowsUpdated));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="delete-data"></a>删除数据

使用以下代码，通过 **DELETE** SQL 语句删除数据。 [getConnection()](https://mariadb.com/kb/en/library/about-mariadb-connector-j/#using-drivermanager) 方法用于连接到 MySQL。  使用 [prepareStatement()](https://docs.oracle.com/javase/tutorial/jdbc/basics/prepared.html) 和 executeUpdate() 方法来准备和运行 delete 语句。 

将 host、database、user 和 password 参数替换为创建你自己的服务器和数据库时指定的值。

```java
import java.sql.*;
import java.util.Properties;

public class DeleteTable {
    public static void main (String[] args)  throws Exception
    {
        // Initialize connection variables.
        String host = "mydemoserver.mysql.database.azure.com";
        String database = "quickstartdb";
        String user = "myadmin@mydemoserver";
        String password = "<server_admin_password>";

        Connection connection = null;

        // Initialize connection object
        try
        {
            String url = String.format("jdbc:mariadb://%s/%s", host, database);
            
            // set up the connection properties
            Properties properties = new Properties();
            properties.setProperty("user", user);
            properties.setProperty("password", password);
            properties.setProperty("useSSL", "true");
            properties.setProperty("verifyServerCertificate", "true");
            properties.setProperty("requireSSL", "false");
            
            // get connection
            connection = DriverManager.getConnection(url, properties);
        }
        catch (SQLException e)
        {
            throw new SQLException("Failed to create connection to database", e);
        }
        if (connection != null) 
        { 
            System.out.println("Successfully created connection to database.");
        
            // Perform some SQL queries over the connection.
            try
            {
                // Delete some data from table.
                int nRowsDeleted = 0;
                PreparedStatement preparedStatement = connection.prepareStatement("DELETE FROM inventory WHERE name = ?;");
                preparedStatement.setString(1, "orange");
                nRowsDeleted += preparedStatement.executeUpdate();
                System.out.println(String.format("Deleted %d row(s) of data.", nRowsDeleted));
    
                // NOTE No need to commit all changes to database, as auto-commit is enabled by default.
            }
            catch (SQLException e)
            {
                throw new SQLException("Encountered an error when executing given sql statement.", e);
            }       
        }
        else {
            System.out.println("Failed to create connection to database.");
        }
        System.out.println("Execution finished.");
    }
}
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用转储和还原将 MySQL 数据库迁移到 Azure Database for MySQL](concepts-migrate-dump-restore.md)
