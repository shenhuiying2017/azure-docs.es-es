---
title: "Transmisión de datos desde Event Hubs con Apache Spark en Azure HDInsight | Microsoft Docs"
description: "Instrucciones detalladas sobre cómo enviar una transmisión de datos a Azure Event Hubs y, luego, recibir dichos eventos en HDInsight Spark mediante una aplicación de Scala"
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 68894e75-3ffa-47bd-8982-96cdad38b7d0
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 91c60e944dd3b72f5bf1137d93ba2ae70537b2f7
ms.contentlocale: es-es
ms.lasthandoff: 03/29/2017


---
# <a name="spark-streaming-process-events-from-azure-event-hubs-with-apache-spark-cluster-on-hdinsight"></a>Streaming de Spark: procesado de eventos desde Azure Event Hubs con clústeres de Apache Spark en HDInsight

En este artículo, comprenderá algunos conceptos relacionados con el uso de streaming mediante Apache Spark y, a continuación, creará una solución de streaming que implica los pasos siguientes:

1. Usará una aplicación independiente para ingerir mensajes en un centro de eventos de Azure.

2. Recuperará los mensajes del centro de eventos en tiempo real con una aplicación que se ejecuta en el clúster de Spark en Azure HDInsight.

3. Enrutará los datos a salidas diferentes como Azure Storage Blob, una tabla de Hive o una tabla de SQL. 

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="spark-streaming-concepts"></a>Conceptos de streaming de Spark

Para una explicación detallada de cómo se controla el streaming en Apache Spark, consulte la [introducción al streaming en Apache Spark](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). HDInsight ofrece las mismas funciones de streaming para un clúster de Spark en Azure.  

## <a name="what-does-this-solution-do"></a>¿Cómo funciona la solución?

En este artículo, realizará los siguientes pasos para crear una solución de streaming:

1. Cree un Centro de eventos de Azure, que será el que reciba una transmisión de eventos.

2. Ejecute una aplicación local independiente que genere eventos y los inserte en el centro de eventos de Azure. La aplicación de ejemplo que lo hace se publica en [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples).

3. Ejecute una aplicación de streaming de forma remota en un clúster de Spark que lea los eventos de streaming del Centro de eventos de Azure y lo inserte en diferentes ubicaciones (Blob de Azure, tabla de Hive y tabla de Base de datos SQL). 

## <a name="create-azure-event-hub"></a>Creación de un Centro de eventos de Azure

1. Inicie sesión en [Azure Portal](https://manage.windowsazure.com) y haga clic en **Nuevo** en la parte superior izquierda de la pantalla.

2. Haga clic en **Internet de las cosas** y en **Event Hubs**.
   
    ![Creación de un centro de eventos](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub9.png)

3. En la hoja **Crear espacio de nombres** , especifique el nombre del espacio de nombres. Elija el plan de tarifa (Básico o Estándar). Elija también una suscripción de Azure, un grupo de recursos y la ubicación en la que se va a crear el recurso. Haga clic en **Crear** para crear el espacio de nombres.
   
    ![Creación de un centro de eventos](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub1.png)

    > [!NOTE]
       > Para reducir la latencia y los costos, debe seleccionar la misma **ubicación** que la del clúster Apache Spark en HDInsight.
       > 
       > 

4. En la lista de espacios de nombres de los Centros de eventos, haga clic en el espacio de nombres recién creado.      
   
    
5. En la hoja de espacio de nombres, haga clic en **Event Hubs** y luego en **+ Centro de eventos** para crear un nuevo centro de eventos.
   
    ![Creación de un centro de eventos](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub3.png)

6. Escriba un nombre para el centro de eventos, establezca el recuento de partición en 10 y la retención de mensajes en 1. En esta solución no vamos a archivar los mensajes, así que puede dejar el resto con los valores predeterminados y hacer clic en **Crear**.
   
    ![Creación de un centro de eventos](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub5.png)

7. El centro de eventos recién creado aparece en la hoja Centro de eventos.
    
     ![](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub6.png)

8. En la hoja del espacio de nombres (no en la hoja del Centro de eventos específico), haga clic en **Directivas de acceso compartido** y, luego, en **RootManageSharedAccessKey**.
    
     ![](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub7.png)

9. Haga clic en el botón Copiar para copiar la clave principal **RootManageSharedAccessKey** y la cadena de conexión en el portapapeles. Guárdelas, ya que las necesitará más tarde en el tutorial.
    
     ![](./media/hdinsight-apache-spark-eventhub-streaming/create-event-hub8.png)

## <a name="send-messages-to-an-azure-event-hub-using-a-scala-application"></a>Envío de mensajes a un centro de eventos de Azure mediante una aplicación de Scala

En esta sección se usa una aplicación de Scala local independiente que genera una transmisión de eventos y la envía al centro de eventos de Azure que creó en el paso anterior. Esta aplicación está disponible en GitHub en [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer). En los siguientes pasos se asume que ya ha bifurcado este repositorio de GitHub.

1. Asegúrese de tener instalados los siguientes elementos en el equipo donde se va a ejecutar la aplicación.

    * Kit de desarrollo de Oracle Java. Se puede instalar desde [aquí](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
    * Un IDE de Java. En este artículo se usa IntelliJ IDEA 15.0.1. Puede instalarlo desde [aquí](https://www.jetbrains.com/idea/download/).


2. Abra la aplicación, **EventhubsSampleEventProducer**, en IntelliJ IDEA.

3. Compile el proyecto. En el menú **Compilar**, haga clic en **Crear proyecto**. Según su configuración de IntelliJ IDEA, el archivo jar de salida se crea en **\classes\artifacts**.

    > [!TIP]
    > También puede usar una opción disponible en IntelliJ IDEA para crear el proyecto directamente desde un repositorio de GitHub. Para entender cómo usar dicho enfoque, siga las instrucciones de la siguiente sección. Tenga en cuenta que muchos de los pasos que se describen en la siguiente sección no se pueden aplicar a la aplicación de Scala que cree en este paso. Por ejemplo:
    > 
    > * No tiene que actualizar el archivo POM para incluir la versión de Spark, ya que no se depende de Spark para crear esta aplicación.
    > * No tiene que agregar ningún archivo JAR de dependencia a la biblioteca de proyectos, ya que el proyecto no requiere dichos archivos.
    > 
    > 

## <a name="receive-messages-from-the-event-hub-using-a-streaming-application-running-on-spark-cluster"></a>Recepción de mensajes del centro de eventos mediante una aplicación de streaming que se ejecuta en un clúster de Spark

En [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples)está disponible una aplicación de Scala de ejemplo para recibir el evento y enrutarlo a diferentes destinos. Siga los pasos que se indican a continuación para actualizar la aplicación y crear el archivo JAR de salida.

1. Inicie IntelliJ IDEA y en la pantalla de inicio, seleccione **Check out from Version Control** (Extraer del repositorio de control de versiones) y haga clic en **Git**.
   
    ![Obtener orígenes de Git](./media/hdinsight-apache-spark-eventhub-streaming/get-source-from-git.png)
2. En el cuadro de diálogo **Clone Repository** (Clonar repositorio), indique la dirección URL del repositorio de Git de origen y el directorio de destino de la clonación, y haga clic en **Clone** (Clonar).
   
    ![Clonar de Git](./media/hdinsight-apache-spark-eventhub-streaming/clone-from-git.png)
3. Siga las indicaciones hasta que el proyecto se haya clonado completamente. Presione **Alt + 1** para abrir la **Project View** (Vista de proyecto). Debería ser similar a la siguiente.
   
    ![Project View](./media/hdinsight-apache-spark-eventhub-streaming/project-view.png)
4. Asegúrese de que el código de aplicación se compila con Java8. Para asegurarse de esto, haga clic en **File** (Archivo), **Project Structure** (Estructura del proyecto) y la **Project** (Proyecto), asegúrese de que el nivel de lenguaje del proyecto se establece en **8 - Expresiones lambda, anotaciones de tipo, etc**.
   
    ![Estructura del proyecto](./media/hdinsight-apache-spark-eventhub-streaming/java-8-compiler.png)
5. Abra **pom.xml** y asegúrese de que la versión de Spark es correcta. En el `<properties>` nodo, busque el siguiente fragmento y compruebe la versión de Spark.
   
        <scala.version>2.11.8</scala.version>
        <scala.compat.version>2.11.8</scala.compat.version>
        <scala.binary.version>2.11</scala.binary.version>
        <spark.version>2.0.0</spark.version>

6. La aplicación requiere un archivo jar de dependencia llamado **jar del controlador JDBC**. Se requiere para escribir los mensajes recibidos desde el Centro de eventos en una Base de datos SQL de Azure. La versión 4.1, o las versiones posteriores de este archivo jar se pueden descargar desde [aquí](https://msdn.microsoft.com/sqlserver/aa937724.aspx). Agregue referencia a este archivo jar en la biblioteca de proyectos. Lleve a cabo los siguiente pasos:
     
     1. En la ventana de IntelliJ IDEA en la que la aplicación está abierta, haga clic en **File** (Archivo), **Project Structure** (Estructura de proyecto) y en **Libraries** (Bibliotecas). 
     2. Haga clic en el icono de agregar (![icono Agregar](./media/hdinsight-apache-spark-eventhub-streaming/add-icon.png)), haga clic en **Java**, y luego navegue hasta la ubicación en que descargó el archivo jar del controlador de JDBC. Siga las indicaciones para agregar el archivo JAR a la biblioteca de proyectos.
        
         ![agregar dependencias que faltan](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "Agregar archivos jar de dependencias que faltan")
     3. Haga clic en **Apply**.
7. Cree el archivo JAR de salida. Lleve a cabo los siguiente pasos.
   
   1. En el cuadro de diálogo **Project Structure** (Estructura de proyecto), haga clic en **Artifacts** (Artefactos) y en el signo más. En el cuadro de diálogo emergente, haga clic en **JAR** y en **From modules with dependencies** (Desde módulos con dependencias).
      
       ![Crear archivo JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-1.png)
   2. En el cuadro de diálogo **Create JAR** from Modules (Crear archivo JAR desde módulos), haga clic en el botón de puntos suspensivos (![puntos suspensivos](./media/hdinsight-apache-spark-eventhub-streaming/ellipsis.png)) en la **clase principal**.
   3. En el cuadro de diálogo **Select Main Class** (Seleccionar clase principal), seleccione cualquiera de las clases disponibles y haga clic en **OK** (Aceptar).
      
       ![Crear archivo JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-2.png)
   4. En el cuadro de diálogo **Create JAR from Modules** (Crear archivo JAR desde módulos), asegúrese de que está seleccionada la opción para **extraer al archivo JAR de destino** y haga clic en **OK** (Aceptar). Así se crea un archivo JAR único con todas las dependencias.
      
       ![Crear archivo JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-3.png)
   5. La pestaña **Output Layout** (Diseño de salida) enumera todos los archivos JAR que forman parte del proyecto Maven. Puede seleccionar y eliminar aquellos de los que la aplicación de Scala no tenga ninguna dependencia directa. En el caso de la aplicación que creamos aquí, puede quitar todas las instancias de (**salida de compilación de microsoft-spark-streaming-examples**) menos la última. Seleccione los archivos JAR que va a eliminar y haga clic en el icono **Eliminar** (![icono eliminar](./media/hdinsight-apache-spark-eventhub-streaming/delete-icon.png)).
      
       ![Crear archivo JAR](./media/hdinsight-apache-spark-eventhub-streaming/delete-output-jars.png)
      
       Asegúrese de que la casilla **Build on make** (Compilar al crear) está activada, lo que garantiza que el archivo jar se crea cada vez que el proyecto se compila o actualiza. Haga clic en **Apply**.
   6. En la ficha **Output Layout** (Diseño de salida), en la parte inferior del cuadro **Available Elements** (Elementos disponibles), tendrá los dos archivos jar de SQL JDBC que agregó anteriormente a la biblioteca de proyectos. Debe agregarlos a la pestaña **Output Layout** (Diseño de salida). Haga clic con el botón derecho en el archivo jar y, después, haga clic en **Extract Into Output Root**(Extraer en raíz de salida).
      
       ![Extraer archivo JAR de dependencia](./media/hdinsight-apache-spark-eventhub-streaming/extract-dependency-jar.png)  
      
       La pestaña **Output Layout** (Diseño de salida) debe ser como la siguiente.
      
       ![Pestaña de salida final](./media/hdinsight-apache-spark-eventhub-streaming/final-output-tab.png)        
      
       En el cuadro de diálogo **Project Structure** (Estructura de proyecto), haga clic en **Apply** (Aplicar) y en **OK** (Aceptar).    
   7. En la barra de menús, haga clic en **Build** (Compilar) y en **Make Project** (Crear proyecto). También puede hacer clic en **Build Artifacts** (Compilar artefactos) para crear el archivo JAR. El archivo jar de salida se crea en **\classes\artifacts**.
      
       ![Crear archivo JAR](./media/hdinsight-apache-spark-eventhub-streaming/output.png)

## <a name="run-the-applications-remotely-on-a-spark-cluster-using-livy"></a>Ejecución remota de aplicaciones en un clúster Spark mediante Livy

Usaremos Livy para ejecutar la aplicación de streaming de forma remota en un clúster de Spark. Para obtener una explicación detallada sobre cómo usar Livy con un clúster de HDInsight Spark, consulte [Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (Linux)](hdinsight-apache-spark-livy-rest-interface.md). Antes de empezar a ejecutar los trabajos remotos en los eventos de transmisión mediante Spark debe realizar dos operaciones:

1. Inicie la aplicación autónoma local para generar eventos y enviarlos al Centro de eventos. Utilice el siguiente comando para hacerlo:
   
        java -cp com-microsoft-azure-eventhubs-client-example.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. Copie el archivo jar de streaming (**spark-streaming-data-persistence-examples.jar**) en la instancia de Azure Blob Storage asociada con el clúster. Esto permite que Livy pueda acceder al archivo JAR. Puede usar [**AzCopy**](../storage/storage-use-azcopy.md), una utilidad de línea de comandos, para hacerlo. Hay muchos otros clientes que se pueden utilizar para cargar datos. Puede encontrar más información al respecto en [Carga de datos para trabajos de Hadoop en HDInsight](hdinsight-upload-data.md).
3. Instale CURL en el equipo desde el que se ejecutan estas aplicaciones. CURL se usa para invocar los puntos de conexión de Livy para ejecutar los trabajos de forma remota.

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-text"></a>Ejecución de las aplicaciones para recibir los eventos en un blob de Almacenamiento de Azure como texto

Abra un símbolo del sistema, navegue al directorio en que instaló CURL y ejecute el siguiente comando (reemplace el nombre de usuario y la contraseña, y el nombre del clúster):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

A continuación se definen los parámetros del archivo **inputBlob.txt** :

    { "file":"wasbs:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Estos son los parámetros del archivo de entrada:

* **file** es la ruta de acceso al archivo JAR de la aplicación en la cuenta de Almacenamiento de Azure asociada con el clúster.
* **className** es el nombre de la clase en el archivo JAR.
* **args** es la lista de argumentos requeridos por la clase
* **numExecutors** es el número de núcleos que usa Spark para ejecutar la aplicación de streaming. Siempre debe ser al menos dos veces el número de particiones del Centro de eventos.
* **executorMemory**, **executorCores** y **driverMemory** son los parámetros que se usan para asignar los recursos necesarios a la aplicación de streaming.

> [!NOTE]
> No es preciso crear las carpetas de salida (EventCheckpoint, EventCount/EventCount10) que se usan como parámetros. La aplicación de streaming las crea automáticamente.
> 
> 

Al ejecutar el comando, debería ver una salida similar a la siguiente:

    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /18
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Tue, 01 Dec 2015 05:39:10 GMT
    < Content-Length: 37
    <
    {"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

Anote el identificador de lote de la última línea de la salida (en este ejemplo es '1'). Para comprobar que la aplicación se ejecuta correctamente, puede examinar la cuenta de Almacenamiento de Azure asociada con el clúster, donde debería ver que se ha creado la carpeta **/EventCount/EventCount10** . Dicha carpeta debe contener blobs que capturen el número de eventos procesados en el período especificado para el parámetro **batch-interval-in-seconds**.

La aplicación seguirá ejecutándose hasta que la elimine. Para ello, utilice el siguiente comando:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### <a name="run-the-applications-to-receive-the-events-into-an-azure-storage-blob-as-json"></a>Ejecución de las aplicaciones para recibir los eventos en un blob de Almacenamiento de Azure como JSON
Abra un símbolo del sistema, navegue al directorio en que instaló CURL y ejecute el siguiente comando (reemplace el nombre de usuario y la contraseña, y el nombre del clúster):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

A continuación se definen los parámetros del archivo **inputJSON.txt** :

    { "file":"wasbs:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Los parámetros son similares a los que especificó para la salida de texto en el paso anterior. Una vez más, no es preciso crear las carpetas de salida (EventCheckpoint, EventCount/EventCount10) que se usan como parámetros. La aplicación de streaming las crea automáticamente.

 Tras ejecutar el comando puede examinar la cuenta de Almacenamiento de Azure asociada con el clúster, donde debería ver que se ha creado la carpeta **/EventStore10** . Si abre cualquier archivo con el prefijo **part-** , debería ver los eventos procesados en un formato JSON.

### <a name="run-the-applications-to-receive-the-events-into-a-hive-table"></a>Ejecución de las aplicaciones para recibir los eventos en una tabla de Hive
Para ejecutar la aplicación que transmite los eventos a una tabla de Hive se necesitan varios componentes adicionales. Dichos componentes son:

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-rdbms-3.2.9.jar
* datanucleus-core-3.2.10.jar
* hive-site.xml

Los archivos **.jar** están disponibles en el clúster de HDInsight Spark en `/usr/hdp/current/spark-client/lib`. El archivo **hive-site.xml** está disponible en `/usr/hdp/current/spark-client/conf`.

Puede usar [WinScp](http://winscp.net/eng/download.php) para copiar estos archivos desde el clúster al equipo local. A continuación, puede usan herramientas para copiar estos archivos en su cuenta de almacenamiento asociada con el clúster. Para más información acerca de cómo cargar archivos en la cuenta de almacenamiento, consulte [Carga de datos para trabajos de Hadoop en HDInsight](hdinsight-upload-data.md).

Una vez que haya copiado los archivos a la cuenta de Almacenamiento de Azure, abra un símbolo del sistema, navegue al directorio en que instaló CURL y ejecute el siguiente comando (reemplace el nombre de usuario y la contraseña, y el nombre del clúster):

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

A continuación se definen los parámetros del archivo **inputHive.txt** :

    { "file":"wasbs:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasbs:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasbs:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasbs:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasbs:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Los parámetros son similares a los que especificó para la salida de texto en los pasos anteriores. Una vez más, no es preciso crear las carpetas de salida (EventCheckpoint, EventCount/EventCount10) ni la tabla de Hive de salida (EventHiveTable10) que se usan como parámetros. La aplicación de streaming las crea automáticamente. Tenga en cuenta que la opción **jars** y **files** incluye rutas de acceso a los archivos .jar y hive-site.xml que copió a la cuenta de almacenamiento.

Para comprobar que la tabla de Hive se ha creado correctamente, puede usar SSH en el clúster y ejecutar consultas de Hive. Para obtener instrucciones, consulte [Uso de Hive con Hadoop en HDInsight con SSH](hdinsight-hadoop-use-hive-ssh.md). Una vez que se haya conectado mediante SSH, puede ejecutar el comando siguiente para comprobar que se ha creado la tabla de Hive, **EventHiveTable10**.

    show tables;

Debería ver una salida similar a la siguiente:

    OK
    eventhivetable10
    hivesampletable

También puede ejecutar una consulta SELECT para ver el contenido de la tabla.

    SELECT * FROM eventhivetable10 LIMIT 10;

Debe ver algo parecido a lo siguiente:

    ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
    sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
    o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
    TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
    HKCpPlWFWAJILwR69MAq863nCWYzDEw6
    Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
    85dRppSBSbZgThLr1s0GMgKqynDUqudr
    5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
    ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
    vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
    Time taken: 4.434 seconds, Fetched: 10 row(s)


### <a name="run-the-applications-to-receive-the-events-into-an-azure-sql-database-table"></a>Ejecución de las aplicaciones para recibir los eventos en una tabla de Base de datos SQL de Azure
Antes de realizar este paso, asegúrese de que ha creado una Base de datos SQL de Azure. Para instrucciones, consulte el artículo sobre la [creación de una base de datos SQL en cuestión de minutos](../sql-database/sql-database-get-started.md). Para completar esta acción, necesitará valores para el nombre de la base de datos, el nombre del servidor de la base de datos y las credenciales del administrador de la base de datos como parámetros. Sin embargo, no es preciso crear la tabla de base de datos. La aplicación de streaming la crea automáticamente.

Abra un símbolo del sistema, navegue al directorio en que instaló CURL y ejecute el siguiente comando:

    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

A continuación se definen los parámetros del archivo **inputSQL.txt** :

    { "file":"wasbs:///example/jars/spark-streaming-data-persistence-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Para comprobar que la aplicación se ejecuta correctamente, puede conectarse a la Base de datos SQL de Azure mediante SQL Server Management Studio. Para obtener instrucciones sobre cómo hacerlo, consulte [Conexión a la Base de datos SQL con SQL Server Management Studio](../sql-database/sql-database-connect-query-ssms.md). Una vez que se haya conectado a la base de datos, puede navegar a la tabla **EventContent** que creó la aplicación de streaming. Para obtener los datos de la tabla, puede ejecutar una consulta rápida. Ejecute la siguiente consulta:

    SELECT * FROM EventCount

Debería ver una salida similar a la siguiente:

    00046b0f-2552-4980-9c3f-8bba5647c8ee
    000b7530-12f9-4081-8e19-90acd26f9c0c
    000bc521-9c1b-4a42-ab08-dc1893b83f3b
    00123a2a-e00d-496a-9104-108920955718
    0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
    001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
    001vIZgOStka4DXtud0e3tX7XbfMnZrN
    00220586-3e1a-4d2d-a89b-05c5892e541a
    0029e309-9e54-4e1b-84be-cd04e6fce5ec
    003333cf-874f-4045-9da3-9f98c2b4ea49
    0043c07e-8d73-420a-9af7-1fcb94575356
    004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9


## <a name="seealso"></a>Otras referencias
* [Introducción a Apache Spark en HDInsight de Azure](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Creación de aplicaciones de Aprendizaje automático con Apache Spark en HDInsight de Azure](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark con Aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Website log analysis using Spark in HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Submit Spark jobs remotely using Livy with Spark clusters on HDInsight (Linux)](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Use HDInsight Tools Plugin for IntelliJ IDEA to create and submit Spark Scala applications (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones Scala Spark)](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Spark applications remotely (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Spark de forma remota)](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de cuadernos de Zeppelin con un clúster Spark en HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponibles para el cuaderno de Jupyter en el clúster Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](hdinsight-apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](hdinsight-apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/ 

