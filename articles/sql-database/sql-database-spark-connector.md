---
title: Conector de Spark con Azure SQL Database y SQL Server| Microsoft Docs
description: Obtener información acerca de cómo usar el conector de Spark para Azure SQL Database y SQL Server
services: sql-database
author: allenwux
manager: craigg
ms.service: sql-database
ms.custom: ''
ms.topic: article
ms.date: 04/17/2018
ms.author: xiwu
ms.openlocfilehash: 46849d551b6996caaf020caec1ab8104d5388c8f
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="accelerate-real-time-big-data-analytics-with-spark-connector-for-azure-sql-database-and-sql-server"></a>Aceleración del análisis de macrodatos en tiempo real con el conector de Spark para Azure SQL Database y SQL Server

El conector de Spark para Azure SQL Database y SQL Server permite que las bases de datos SQL, como Azure SQL Database y SQL Server, actúen como orígenes de datos de entrada o receptores de datos de salida para los trabajos de Spark. Permite usar datos transaccionales en tiempo real en análisis de macrodatos y conservar los resultados para informes o consultas ad hoc. En comparación con el conector JDBC integrado, este conector proporciona la capacidad para insertar datos de forma masiva en bases de datos SQL. Puede mejorar el rendimiento de la inserción de fila en fila, ya que puede insertar datos entre 10 y 20 veces más rápido. El conector de Spark para Azure SQL Database y SQL Server también admite la autenticación de AAD. Permite conectarse con seguridad a Azure SQL Database desde Azure Databricks con su cuenta de AAD. Proporciona interfaces similares con el conector JDBC integrado. Es fácil migrar los trabajos de Spark existentes para usar este nuevo conector.

## <a name="download"></a>Descargar
Para comenzar, descargue el conector de Spark a SQL DB del [repositorio azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) en GitHub.

## <a name="official-supported-versions"></a>Versiones oficiales compatibles

| Componente                            |Versión                  |
| :----------------------------------- | :---------------------- |
| Spark de Apache                         |2.0.2 o posterior           |
| Scala                                |2.10 o posterior            |
| Microsoft JDBC Driver para SQL Server |6.2 o posterior             |
| Microsoft SQL Server                 |SQL Server 2008 o posterior |
| Azure SQL Database                   |Compatible                |

El conector de Spark para Azure SQL Database y SQL Server usa Microsoft JDBC Driver para SQL Server para mover datos entre los nodos de trabajo de Spark y las bases de datos SQL:
 
El flujo de datos es el siguiente:
1. El nodo principal de Spark se conecta a SQL Server o Azure SQL Database y carga los datos desde una tabla específica o mediante una consulta específica de SQL.
2. El nodo principal de Spark distribuye los datos a los nodos de trabajo para la transformación. 
3. El nodo de trabajo se conecta a SQL Server o Azure SQL Database y escribe los datos en la base de datos. El usuario puede optar por usar la inserción de fila en fila o la inserción de forma masiva.

### <a name="build-the-spark-to-sql-db-connector"></a>Compilación del conector de Spark a SQL DB
Actualmente, el proyecto del conector usa Maven. Para crear el conector sin dependencias, puede ejecutar:

- mvn clean package
- Descargar las últimas versiones del archivo JAR desde la carpeta de versión
- Incluir el archivo JAR de Spark de SQL DB

## <a name="connect-spark-to-sql-db-using-the-connector"></a>Conexión de Spark a SQL DB mediante el conector
Puede conectarse a Azure SQL Database o SQL Server desde los trabajos de Spark, leer o escribir datos. También puede ejecutar una consulta DML o DDL en una base de datos de Azure SQL Database o SQL Server.

### <a name="read-data-from-azure-sql-database-or-sql-server"></a>Lectura de datos desde Azure SQL Database o SQL Server

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients"
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```
### <a name="reading-data-from-azure-sql-database-or-sql-server-with-specified-sql-query"></a>Lectura de datos desde Azure SQL Database o SQL Server con la consulta SQL especificada
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDb(config)
collection.show()
```

### <a name="write-data-to-azure-sql-database-or-sql-server"></a>Escritura de datos en Azure SQL Database o SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._
 
// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients"
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-database-or-sql-server"></a>Ejecución de una consulta DML o DDL en Azure SQL Database o SQL Server
```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City= 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.SqlDBQuery(config)
```

## <a name="connect-spark-to-azure-sql-database-using-aad-authentication"></a>Conexión de Spark a Azure SQL Database mediante la autenticación de AAD
Puede conectarse a Azure SQL Database mediante la autenticación de Azure Active Directory (AAD). Use la autenticación de AAD para administrar de forma centralizada las identidades de los usuarios de la base de datos y como una alternativa a la autenticación de SQL Server.
### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>Conexión con el modo de autenticación ActiveDirectoryPassword
#### <a name="setup-requirement"></a>Requisito de configuración
Si usa el modo de autenticación ActiveDirectoryPassword, necesitará descargar [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) y sus dependencias e incluirlos en la ruta de compilación Java.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username ",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

### <a name="connecting-using-access-token"></a>Conexión mediante el token de acceso
#### <a name="setup-requirement"></a>Requisito de configuración
Si usa el modo de autenticación basado en token de acceso, necesitará descargar [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) y sus dependencias e incluirlos en la ruta de compilación Java.

Consulte [Use Azure Active Directory Authentication for authentication with SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-aad-authentication) (Uso de la autenticación de Azure Active Directory con SQL Database ) para obtener información sobre cómo obtener un token de acceso para Azure SQL Database.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token ",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.SqlDB(config)
collection.show()
```

## <a name="write-data-to-azure-sql-database-or-sql-server-using-bulk-insert"></a>Escritura de datos en Azure SQL Database o SQL Server mediante inserción masiva
El conector JDBC tradicional escribe datos en Azure SQL Database o SQL Server mediante la inserción de fila en fila. Puede usar el conector de Spark a SQL DB para escribir datos en SQL Database mediante la inserción masiva. Mejora considerablemente el rendimiento de escritura al cargar grandes conjuntos de datos o cargar datos en tablas donde se usa un índice de almacenamiento de columnas.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/** 
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "databaseName"      -> "zeqisql",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>Pasos siguientes
Si aún no lo ha hecho, descargue el conector de Spark a Azure SQL Database y SQL Server del [repositorio de GitHub azure-sqldb-spark](https://github.com/Azure/azure-sqldb-spark) y explore los recursos adicionales del repositorio:

-   [Blocs de notas de ejemplo de Azure Databricks](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [Scripts de ejemplo (Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

Es posible que también quiera consultar [Apache Spark SQL, DataFrames, and Datasets Guide](http://spark.apache.org/docs/latest/sql-programming-guide.html) (Guía de Apache Spark SQL, DataFrames y conjuntos de datos) y la[documentación de Azure Databricks](https://docs.microsoft.com/en-us/azure/azure-databricks/).

