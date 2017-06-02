---
title: "Conexión a Azure SQL Database mediante Java | Microsoft Docs"
description: "En este tema se presenta un ejemplo de código Java que puede usar para conectarse a Azure SQL Database y realizar consultas."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: quick start connect, mvc
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 05/23/2017
ms.author: andrela
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 63ab7345b70d456d9d1ad23255d9ca7e777e161d
ms.contentlocale: es-es
ms.lasthandoff: 05/31/2017


---
# <a name="azure-sql-database-use-java-to-connect-and-query-data"></a>Azure SQL Database: uso de Java para conectar y consultar datos

En esta guía de inicio rápido se muestra cómo usar [Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) para conectarse a una base de datos de Azure SQL Database y, después, usar instrucciones Transact-SQL para consultar, insertar, actualizar y eliminar datos de la base de datos desde las plataformas Mac OS, Ubuntu Linux y Windows.

## <a name="prerequisites"></a>Requisitos previos

En esta guía de inicio rápido se utilizan como punto de partida los recursos creados en una de las siguientes guías:

- [Creación de la base de datos: Azure Portal](sql-database-get-started-portal.md)
- [Creación de la base de datos: CLI](sql-database-get-started-cli.md)
- [Creación de la base de datos: PowerShell](sql-database-get-started-powershell.md)

## <a name="install-java-software"></a>Instalación del software de Java

En esta sección se da por hecho que está familiarizado con el desarrollo mediante Java y que nunca ha utilizado Azure SQL Database. Si no está familiarizado con el desarrollo con Java, vaya a [Build an app using SQL Server](https://www.microsoft.com/en-us/sql-server/developer-get-started/) (Crear una aplicación con SQL Server), seleccione **Java** y, a continuación, seleccione el sistema operativo.

### <a name="mac-os"></a>**Mac OS**
Abra el terminal y desplácese hasta el directorio donde planea crear su proyecto de Java. Escriba los siguientes comandos para instalar **brew** y **Maven**: 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew update
brew install maven
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Abra el terminal y desplácese hasta el directorio donde planea crear su proyecto de Java. Escriba los siguientes comandos para instalar **Maven**:

```bash
sudo apt-get install maven
```

### <a name="windows"></a>**Windows**
Instale [Maven](https://maven.apache.org/download.cgi) con el instalador oficial. Use Maven para ayudar a administrar las dependencias, compilar, probar y ejecutar el proyecto de Java. 

## <a name="get-connection-information"></a>Obtención de información sobre la conexión

Obtención de la información de conexión necesaria para conectarse a Azure SQL Database. En los procedimientos siguientes, necesitará el nombre completo del servidor, el nombre de la base de datos y la información de inicio de sesión.

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).
2. Seleccione **Bases de datos SQL** en el menú de la izquierda y haga clic en la base de datos en la página **Bases de datos SQL**. 
3. En la página **Introducción** de la base de datos, revise el nombre completo del servidor, tal como se muestra en la imagen siguiente. Mantenga el puntero sobre el nombre del servidor hasta que aparezca la opción **Haga clic para copiar**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Si olvida la información de inicio de sesión del servidor, navegue hasta la página del servidor de SQL Database para ver el nombre de administrador del servidor y, si es necesario, restablecer la contraseña.
5. Haga clic en **Mostrar las cadenas de conexión de la base de datos**.

6. Revise la cadena de conexión de**JDBC** completa.

    ![Cadena de conexión JDBC](./media/sql-database-connect-query-jdbc/jdbc-connection-string.png)    

### <a name="create-maven-project"></a>**Creación del proyecto de Maven**
En el terminal, cree un nuevo proyecto de Maven. 
```bash
mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=SqlDbSample" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
```

Agregue **Microsoft JDBC Driver para SQL Server** a sus dependencias en ***pom.xml***. 

```xml
<dependency>
    <groupId>com.microsoft.sqlserver</groupId>
    <artifactId>mssql-jdbc</artifactId>
    <version>6.1.0.jre8</version>
</dependency>
```

## <a name="select-data"></a>Selección de datos

Utilice el código siguiente para consultar los 20 primeros productos por categoría mediante la clase de [conexión](https://docs.microsoft.com/sql/connect/jdbc/working-with-a-connection) con una instrucción [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql) de Transact-SQL. Reemplace los parámetros de nombre de host, nombre de base de datos, usuario y contraseña por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.Statement;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Query data example:");
                System.out.println("=========================================");

                // Create and execute a SELECT SQL statement.
                String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName " 
                    + "FROM [SalesLT].[ProductCategory] pc "  
                    + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";
                
                try (Statement statement = connection.createStatement();
                    ResultSet resultSet = statement.executeQuery(selectSql)) {

                        // Print results from select statement
                        System.out.println("Top 20 categories:");
                        while (resultSet.next())
                        {
                            System.out.println(resultSet.getString(1) + " "
                                + resultSet.getString(2));
                        }
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## <a name="insert-data"></a>Insertar datos

Use el código siguiente para insertar un producto nuevo en la tabla SalesLT.Product mediante la clase [Prepared Statements](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) con una instrucción [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) de Transact-SQL. Reemplace los parámetros de nombre de host, nombre de base de datos, usuario y contraseña por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Insert data example:");
                System.out.println("=========================================");

                // Prepared statement to insert data
                String insertSql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, " 
                    + " StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

                java.util.Date date = new java.util.Date();
                java.sql.Timestamp sqlTimeStamp = new java.sql.Timestamp(date.getTime());

                try (PreparedStatement prep = connection.prepareStatement(insertSql)) {
                        prep.setString(1, "BrandNewProduct");
                        prep.setInt(2, 200989);
                        prep.setString(3, "Blue");
                        prep.setDouble(4, 75);
                        prep.setDouble(5, 80);
                        prep.setTimestamp(6, sqlTimeStamp);

                        int count = prep.executeUpdate();
                        System.out.println("Inserted: " + count + " row(s)");
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```
## <a name="update-data"></a>Actualización de datos

Use el código siguiente para actualizar el nuevo producto que agregó anteriormente mediante la clase [Prepared Statements](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) con una instrucción [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql) de Transact-SQL para actualizar los datos de Azure SQL Database. Reemplace los parámetros de nombre de host, nombre de base de datos, usuario y contraseña por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Update data example:");
                System.out.println("=========================================");

                // Prepared statement to update data
                String updateSql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?";

                try (PreparedStatement prep = connection.prepareStatement(updateSql)) {
                        prep.setString(1, "500");
                        prep.setString(2, "BrandNewProduct");

                        int count = prep.executeUpdate();
                        System.out.println("Updated: " + count + " row(s)")
                }
        }
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```



## <a name="delete-data"></a>Eliminación de datos

Utilice el código siguiente para eliminar el nuevo producto que ha agregado anteriormente con [Prepared Statements](https://docs.microsoft.com/sql/connect/jdbc/using-statements-with-sql) con una instrucción [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql) de Transact-SQL. Reemplace los parámetros de nombre de host, nombre de base de datos, usuario y contraseña por los valores que especificó cuando creó la base de datos con los datos de ejemplo AdventureWorksLT. 

```java
package com.sqldbsamples;

import java.sql.Connection;
import java.sql.PreparedStatement;
import java.sql.DriverManager;

public class App {

    public static void main(String[] args) {
    
        // Connect to database
        String hostName = "your_server";
        String dbName = "your_database";
        String user = "your_username";
        String password = "your_password";
        String url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
        Connection connection = null;

        try {
                connection = DriverManager.getConnection(url);
                String schema = connection.getSchema();
                System.out.println("Successful connection - Schema: " + schema);

                System.out.println("Delete data example:");
                System.out.println("=========================================");

                // Prepared statement to delete data
                String deleteSql = "DELETE SalesLT.Product WHERE Name = ?";

                try (PreparedStatement prep = connection.prepareStatement(deleteSql)) {
                        prep.setString(1, "BrandNewProduct");

                        int count = prep.executeUpdate();
                        System.out.println("Deleted: " + count + " row(s)");
                }
        }        
        catch (Exception e) {
                e.printStackTrace();
        }
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
- [Diseño de su primera base de datos SQL de Azure](sql-database-design-first-database.md)
- [Controlador JDBC de Microsoft para SQL Server](https://github.com/microsoft/mssql-jdbc)
- [Informe de los problemas y realización de preguntas](https://github.com/microsoft/mssql-jdbc/issues)


