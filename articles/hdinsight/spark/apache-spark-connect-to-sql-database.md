---
title: Uso de Apache Spark para leer y escribir datos en una base de datos SQL de Azure | Microsoft Docs
description: "Aprenda a configurar una conexión entre el clúster de HDInsight Spark y una base de datos SQL de Azure para leer, escribir y transmitir datos en una base de datos SQL."
services: hdinsight
documentationcenter: 
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/29/2018
ms.author: nitinme
ms.openlocfilehash: 28ed6b9774bb85c7ec806c7775c34f8bc3d66bde
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/01/2018
---
# <a name="use-hdinsight-spark-cluster-to-read-and-write-data-to-azure-sql-database"></a>Uso del clúster de HDInsight Spark para leer y escribir datos en la base de datos SQL de Azure

Aprenda a conectar un clúster de Apache Spark en Azure HDInsight con una base de datos SQL de Azure después, lea, escriba y transmita datos en dicha base de datos. Las instrucciones descritas en este artículo utilizan un cuaderno de Jupyter Notebook para ejecutar los fragmentos de código de Scala. Sin embargo, puede crear una aplicación independiente en Scala o Python y realizar las mismas tareas. 

## <a name="prerequisites"></a>requisitos previos

* **Clúster de Azure HDInsight Spark**.  Siga las instrucciones de [Creación de un clúster de Apache Spark en HDInsight](apache-spark-jupyter-spark-sql.md).

* **Base de datos SQL de Azure** Siga las instrucciones de [Creación de una instancia de Azure SQL Database en Azure Portal](../../sql-database/sql-database-get-started-portal.md). Asegúrese de crear una base de datos con los datos y el esquema del ejemplo **AdventureWorksLT**. Además, asegúrese de crear una regla de firewall a nivel de servidor para permitir que la dirección IP del cliente acceda a la base de datos SQL en el servidor. Las instrucciones para agregar la regla de firewall están disponibles en el mismo artículo. Una vez que haya creado la base de datos SQL de Azure, asegúrese de conservar los siguientes valores a mano. Los necesitará para conectarse a la base de datos desde un clúster de Spark.

    * Nombre del servidor que hospeda la base de datos SQL de Azure
    * Nombre de la base de datos SQL de Azure
    * Nombre de usuario y contraseña de administrador de base de datos SQL de Azure

* **SQL Server Management Studio**. Siga las instrucciones de [Azure SQL Database: uso de SQL Server Management Studio para conectarse a los datos y realizar consultas en ellos](../../sql-database/sql-database-connect-query-ssms.md).

## <a name="create-a-jupyter-notebook"></a>Creación de un cuaderno de Jupyter

Comience con la creación de un cuaderno de Jupyter Notebook asociado al clúster de Spark. Utilice ese cuaderno para ejecutar los fragmentos de código que se utilizan en este artículo. 

1. En [Azure Portal](https://portal.azure.com/), abra el clúster. 

2. Desde la sección **Vínculos rápidos**, haga clic en **Paneles de clúster** para abrir la vista **Paneles de clúster**.  Si no ve la sección **Vínculos rápidos**, haga clic en **Introducción** en el menú izquierdo de la hoja.

    ![Panel de clúster de Spark](./media/apache-spark-connect-to-sql-database/hdinsight-cluster-dashboard-on-spark.png "Cluster dashboard on Spark") 

3. Haga clic en **Jupyter Notebook**. Cuando se le pida, escriba las credenciales del clúster.

    ![Jupyter Notebook en Spark](./media/apache-spark-connect-to-sql-database/hdinsight-jupyter-notebook-on-spark.png "Jupyter Notebook en Spark")
   
   > [!NOTE]
   > También puede acceder al cuaderno de Jupyter Notebook en el clúster Spark si abre la siguiente dirección URL en el explorador. Reemplace **CLUSTERNAME** por el nombre del clúster:
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 

4. En el cuaderno de Jupyter, en la esquina superior derecha, haga clic en **Nuevo** y, a continuación, en **Spark** para crear un cuaderno de Scala. Los cuadernos de Jupyter en el clúster de HDInsight Spark también proporcionan el kernel **PySpark** para aplicaciones Python2 y el kernel **PySpark3** para aplicaciones Python3. En este artículo, vamos a crear un cuaderno de Scala.
   
    ![Kernels de Jupyter Notebook en Spark](./media/apache-spark-connect-to-sql-database/kernel-jupyter-notebook-on-spark.png "Kernels de Jupyter Notebook en Spark")

    Para más información sobre los kernels, consulte [Uso de kernels de Jupyter Notebook con clústeres de Apache Spark en HDInsight](apache-spark-jupyter-notebook-kernels.md).

   > [!NOTE]
   > En este artículo, utilizamos un kernel de Spark (Scala) porque de momento solo se admite el streaming de los datos de Spark a la base de datos SQL en Scala y Java. Aunque es posible leer y escribir en SQL mediante Python, por coherencia, en este artículo usamos Scala para las tres operaciones.
   >

5. Se abre un nuevo cuaderno con un nombre predeterminado, **Sin título**. Haga clic en el nombre del cuaderno y escriba un nombre de su elección.

    ![Proporcionar un nombre para el cuaderno](./media/apache-spark-connect-to-sql-database/hdinsight-spark-jupyter-notebook-name.png "Proporcionar un nombre para el cuaderno")

Ahora puede empezar a crear la aplicación.
    
## <a name="read-data-from-azure-sql-database"></a>Lectura de datos desde la base de datos SQL de Azure

En esta sección, va a leer los datos de una tabla (por ejemplo, **SalesLT.Address**) que existe en la base de datos AdventureWorks.

1. En un nuevo cuaderno de Jupyter Notebook, en una celda de código, pegue el fragmento de código siguiente y reemplace los valores del marcador de posición por los valores de la instancia de Azure SQL Database.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Presione **MAYÚS + ENTRAR** para ejecutar la celda de código.  

2. El fragmento de código siguiente crea una dirección URL de JDBC que puede pasar a las API de tramas de datos de Spark y crea un objeto `Properties` para guardar los parámetros. Pegue el fragmento de código en una celda de código y presione **MAYÚS + ENTRAR** para ejecutarlo.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")         

3. El fragmento de código siguiente crea una trama de datos con los datos de una tabla en la base de datos SQL de Azure. En este fragmento de código, utilizamos la tabla **SalesLT.Address** que está disponible como parte de la base de datos **AdventureWorksLT**. Pegue el fragmento de código en una celda de código y presione **MAYÚS + ENTRAR** para ejecutarlo.

       val sqlTableDF = spark.read.jdbc(jdbc_url, "SalesLT.Address", connectionProperties)

4. Ahora puede realizar operaciones en la trama de datos, como obtener el esquema de datos:

       sqlTableDF.printSchema
   
    Debe ver una salida similar a la siguiente:

    ![Proporcionar un nombre para el cuaderno](./media/apache-spark-connect-to-sql-database/read-from-sql-schema-output.png "Proporcionar un nombre para el cuaderno")

5. También puede realizar operaciones como recuperar las 10 primeras filas.

       sqlTableDF.show(10)

6. O bien, recuperar columnas específicas del conjunto de datos.

       sqlTableDF.select("AddressLine1", "City").show(10)

## <a name="write-data-into-azure-sql-database"></a>Escritura de datos en Azure SQL Database

En esta sección, utilizamos un archivo CSV de ejemplo disponible en el clúster para crear una tabla en la base de datos SQL de Azure y rellenarla con datos. El archivo CSV de ejemplo (**HVAC.csv**) está disponible en todos los clústeres de HDInsight en `HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv`.

1. En un nuevo cuaderno de Jupyter Notebook, en una celda de código, pegue el fragmento de código siguiente y reemplace los valores del marcador de posición por los valores de la instancia de Azure SQL Database.

       // Declare the values for your Azure SQL database

       val jdbcUsername = "<SQL DB ADMIN USER>"
       val jdbcPassword = "<SQL DB ADMIN PWD>"
       val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
       val jdbcPort = 1433
       val jdbcDatabase ="<AZURE SQL DB NAME>"

    Presione **MAYÚS + ENTRAR** para ejecutar la celda de código.  

2. El fragmento de código siguiente crea una dirección URL de JDBC que puede pasar a las API de tramas de datos de Spark y crea un objeto `Properties` para guardar los parámetros. Pegue el fragmento de código en una celda de código y presione **MAYÚS + ENTRAR** para ejecutarlo.

       import java.util.Properties

       val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=60;"
       val connectionProperties = new Properties()
       connectionProperties.put("user", s"${jdbcUsername}")
       connectionProperties.put("password", s"${jdbcPassword}")

3. El fragmento de código siguiente extrae el esquema de los datos de HVAC.csv y utiliza el esquema para cargar los datos del archivo CSV en una trama de datos, `readDf`. Pegue el fragmento de código en una celda de código y presione **MAYÚS + ENTRAR** para ejecutarlo.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readDf = spark.read.format("csv").schema(userSchema).load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

4. Use la trama de datos `readDf` para crear una tabla temporal, `temphvactable`. Después, utilice la tabla temporal para crear una tabla de hive, `hvactable_hive`.

       readDf.createOrReplaceTempView("temphvactable")
       spark.sql("create table hvactable_hive as select * from temphvactable")

5. Por último, utilice la tabla de hive para crear una tabla en la base de datos SQL de Azure. El fragmento de código siguiente crea `hvactable` en la base de datos SQL de Azure.

       spark.table("hvactable_hive").write.jdbc(jdbc_url, "hvactable", connectionProperties)

6. Conéctese a la base de datos SQL de Azure con SSMS y compruebe que ve una `dbo.hvactable` allí.

    a. Inicie SSMS y conéctese a la base de datos SQL de Azure proporcionando los detalles de conexión, tal como se muestra en la captura de pantalla siguiente.

    ![Conexión a una base de datos SQL con SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms.png "Connect to SQL database using SSMS")

    b. En el Explorador de objetos, expanda la base de datos SQL de Azure y el nodo de tabla para ver la tabla **dbo.hvactable** creada.

    ![Conexión a una base de datos SQL con SSMS](./media/apache-spark-connect-to-sql-database/connect-to-sql-db-ssms-locate-table.png "Connect to SQL database using SSMS")

## <a name="stream-data-into-azure-sql-database"></a>Transmisión de datos en Azure SQL Database

En esta sección, vamos a transmitir los datos a la tabla **hvactable** que ha creado en la base de datos SQL de Azure en la sección anterior.

1. Como primer paso, asegúrese de que no hay ningún registro en **hvactable**. Con SSMS, ejecute la siguiente consulta en la tabla.

       DELETE FROM [dbo].[hvactable]

2. Cree un nuevo cuaderno de Jupyter Notebook en el clúster de HDInsight Spark. En una celda de código, pegue el siguiente fragmento de código y presione **MAYÚS + ENTRAR**:

       import org.apache.spark.sql._
       import org.apache.spark.sql.types._
       import org.apache.spark.sql.functions._
       import org.apache.spark.sql.streaming._
       import java.sql.{Connection,DriverManager,ResultSet}

3. Vamos a transmitir los datos desde **HVAC.csv** a hvactable. El archivo HVAC.csv está disponible en el clúster en */HdiSamples/HdiSamples/SensorSampleData/HVAC/*. En el fragmento de código siguiente, obtenemos primero el esquema de los datos que se van a transmitir. Después, creamos una trama de datos de streaming con ese esquema. Pegue el fragmento de código en una celda de código y presione **MAYÚS + ENTRAR** para ejecutarlo.

       val userSchema = spark.read.option("header", "true").csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv").schema
       val readStreamDf = spark.readStream.schema(userSchema1).csv("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/") 
       readStreamDf.printSchema

4. La salida muestra el esquema de **HVAC.csv**. La tabla **hvactable** tiene también el mismo esquema. La salida muestra las columnas de la tabla.

    ![Esquema de tabla](./media/apache-spark-connect-to-sql-database/schema-of-table.png "Schema of table")

5. Por último, utilice el siguiente fragmento de código para leer los datos del archivo HVAC.csv y transmitirlos a la tabla **hvactable** en la base de datos SQL de Azure. Pegue el fragmento de código en una celda de código, reemplace los valores del marcador de posición por los valores de la instancia de Azure SQL Database y, después, presione **MAYÚS + ENTRAR** para ejecutarlo.

       val WriteToSQLQuery  = readStreamDf.writeStream.foreach(new ForeachWriter[Row] {
          var connection:java.sql.Connection = _
          var statement:java.sql.Statement = _
          
          val jdbcUsername = "<SQL DB ADMIN USER>"
          val jdbcPassword = "<SQL DB ADMIN PWD>"
          val jdbcHostname = "<SQL SERVER NAME HOSTING SDL DB>" //typically, this is in the form or servername.database.windows.net
          val jdbcPort = 1433
          val jdbcDatabase ="<AZURE SQL DB NAME>"
          val driver = "com.microsoft.sqlserver.jdbc.SQLServerDriver"
          val jdbc_url = s"jdbc:sqlserver://${jdbcHostname}:${jdbcPort};database=${jdbcDatabase};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;"
  
         def open(partitionId: Long, version: Long):Boolean = {
           Class.forName(driver)
           connection = DriverManager.getConnection(jdbc_url, jdbcUsername, jdbcPassword)
           statement = connection.createStatement
           true
         }
  
         def process(value: Row): Unit = {
           val Date  = value(0)
           val Time = value(1)
           val TargetTemp = value(2)
           val ActualTemp = value(3)
           val System = value(4)
           val SystemAge = value(5)
           val BuildingID = value(6)  
    
           val valueStr = "'" + Date + "'," + "'" + Time + "'," + "'" + TargetTemp + "'," + "'" + ActualTemp + "'," + "'" + System + "'," + "'" + SystemAge + "'," + "'" + BuildingID + "'"
           statement.execute("INSERT INTO " + "dbo.hvactable" + " VALUES (" + valueStr + ")")   
           }

         def close(errorOrNull: Throwable): Unit = {
            connection.close
          }
         })
        
         var streamingQuery = WriteToSQLQuery.start()

6. Para comprobar que los datos se están transmitiendo a la tabla **hvactable**, ejecute la consulta siguiente. Cada vez que se ejecuta la consulta, muestra el aumento del número de filas en la tabla.

        SELECT COUNT(*) FROM hvactable

## <a name="next-steps"></a>pasos siguientes

* [Uso de un clúster de HDInsight Spark para analizar los datos en Data Lake Store](apache-spark-use-with-data-lake-store.md)
* [Proceso de los eventos de flujo estructurado mediante EventHub](apache-spark-eventhub-structured-streaming.md)
* [Uso Spark Structured Streaming con Kafka en HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
