---
title: Consulta de Hive mediante el controlador JDBC - Azure HDInsight | Microsoft Docs
description: "Use el controlador JDBC desde una aplicación Java para enviar consultas de Hive a Hadoop en HDInsight. Conéctese mediante programación y desde el cliente de SQuirrel SQL."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 928f8d2a-684d-48cb-894c-11c59a5599ae
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/28/2017
ms.author: larryfr
ms.openlocfilehash: 7da4a7e0a60fd1e5c78f53b0a8e7ab333c5d2465
ms.sourcegitcommit: 4bd369fc472dced985239aef736fece42fecfb3b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/04/2018
---
# <a name="query-hive-through-the-jdbc-driver-in-hdinsight"></a>Consulta de Hive mediante el controlador JDBC en HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Aprenda a usar el controlador JDBC desde una aplicación Java para enviar consultas de Hive a Hadoop en Azure HDInsight. La información de este documento muestra cómo conectarse de forma programática y desde el cliente de SQuirrel SQL.

Para obtener más información sobre la interfaz JDBC de Hive, consulte [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>requisitos previos

* Hadoop en un clúster de HDInsight. Se admiten tantos los clústeres basados en Windows como en Linux.

  > [!IMPORTANT]
  > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, vea la sección [Retirada de HDInsight 3.3](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [SQL SQuirreL](http://squirrel-sql.sourceforge.net/). SQuirreL es una aplicación de cliente JDBC.

* El [Kit de desarrolladores de Java (JDK) versión 7](https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html) , o superior.

* [Apache Maven](https://maven.apache.org). Maven es un sistema de creación de proyectos para proyectos de Java que utiliza el proyecto asociado a este artículo.

## <a name="jdbc-connection-string"></a>Cadena de conexión JDBC

Las conexiones de JDBC a un clúster de HDInsight en Azure se realizan en el puerto 443 y el tráfico se protege mediante SSL. La puerta de enlace pública tras la que se encuentran los clústeres redirige el tráfico al puerto que HiveServer2 escucha. En la siguiente cadena de conexión puede verse el formato que se va a utilizar en HDInsight:

    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2

Reemplace `CLUSTERNAME` por el nombre del clúster de HDInsight.

## <a name="authentication"></a>Autenticación

Al establecer la conexión, tiene que utilizar el nombre y la contraseña de administrador del clúster de HDInsight para autenticar la puerta de enlace del clúster. Cuando se conecta desde los clientes JDBC como SQL SQuirreL, tiene que escribir el nombre de administrador y la contraseña en la configuración de cliente.

Desde una aplicación de Java, tiene que utilizar el nombre y la contraseña al establecer una conexión. Por ejemplo, el siguiente código Java abre una nueva conexión con la cadena de conexión, el nombre de administrador y la contraseña:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Conexión con el cliente SQL SQuirreL

SQL SQuirreL es un cliente JDBC que puede utilizarse para ejecutar consultas de Hive de manera remota con el clúster de HDInsight. En los pasos siguientes se da por hecho que ya ha instalado SQuirreL SQL.

1. Copie los controladores JDBC de Hive desde el clúster de HDInsight.

    * En el caso de los clústeres de **HDInsight basados en Linux** de la versión 3.5 o 3.6, siga los pasos que se indican a continuación para descargar los archivos jar necesarios.

        1. Cree un directorio que contenga la aplicación. Por ejemplo, `mkdir hivedriver`.

        2. En una línea de comandos, use los siguientes comandos para copiar los archivos en el clúster de HDInsight:

            ```bash
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-common.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/hadoop-auth.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/lib/log4j-*.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hadoop-client/lib/slf4j-*.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/hive-*-1.2*.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/httpclient-*.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/httpcore-*.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/libthrift-*.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/libfb*.jar .
            scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-logging-*.jar .
            ```

            Reemplace `USERNAME` por el nombre de la cuenta de usuario SSH del clúster. Reemplace `CLUSTERNAME` por el nombre del clúster de HDInsight.

    * Para **HDInsight basado en Windows**, siga estos pasos para descargar los archivos jar necesarios.

        1. En Azure Portal, seleccione el clúster de HDInsight y, luego, seleccione el icono **Escritorio remoto**.

            ![Icono de escritorio remoto](./media/apache-hadoop-connect-hive-jdbc-driver/remotedesktopicon.png)

        2. En la sección Escritorio remoto, utilice el botón **Conectar** para conectarse al clúster. Si Escritorio remoto no está habilitado, use el formulario para proporcionar un nombre de usuario y una contraseña y, después, seleccione **Habilitar** para habilitar Escritorio remoto para el clúster.

            ![Sección Escritorio remoto](./media/apache-hadoop-connect-hive-jdbc-driver/remotedesktopblade.png)

            Después de seleccionar **Conectar**, se descargará un archivo .RDP. Utilice este archivo para iniciar al cliente del escritorio remoto. Cuando se le solicite, utilice el nombre de usuario y la contraseña que especificó para el acceso de Escritorio remoto.

        3. Una vez conectado, copie los archivos siguientes de la sesión de escritorio remoto en el equipo local. Póngalos en un directorio local denominado `hivedriver`.

            * C:\apps\dist\hive-0.14.0.2.2.9.1-7\lib\hive-jdbc-0.14.0.2.2.9.1-7-standalone.jar
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\hadoop-common-2.6.0.2.2.9.1-7.jar
            * C:\apps\dist\hadoop-2.6.0.2.2.9.1-7\share\hadoop\common\lib\hadoop-auth-2.6.0.2.2.9.1-7.jar

            > [!NOTE]
            > Los números de versión que se incluyen en las rutas de acceso y nombres de archivo pueden ser diferentes para su clúster.

        4. Desconecte la sesión de escritorio remoto cuando haya terminado de copiar los archivos.

2. Inicie la aplicación SQL SQuirreL. A la izquierda de la ventana, seleccione **Drivers** (Controladores).

    ![Pestaña de controladores a la izquierda de la ventana](./media/apache-hadoop-connect-hive-jdbc-driver/squirreldrivers.png)

3. En los iconos en la parte superior del cuadro de diálogo **Drivers** (Controladores), seleccione el icono de **+** para crear un nuevo controlador.

    ![Iconos de controladores](./media/apache-hadoop-connect-hive-jdbc-driver/driversicons.png)

4. En el cuadro de diálogo Add driver (agregar controlador), agregue la siguiente información:

    * **Name** (Nombre): Hive.
    * **Example URL** (URL de ejemplo): `jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`
    * **Extra Class Path** (Ruta de acceso de clase adicional): use el botón Agregar para agregar los archivos jar que se descargaron anteriormente.
    * **Class Name** (Nombre de clase): org.apache.hive.jdbc.HiveDriver.

   ![cuadro de diálogo para agregar controlador](./media/apache-hadoop-connect-hive-jdbc-driver/adddriver.png)

   Haga clic en **Aceptar** para guardar la configuración.

5. En el lado izquierdo de la ventana de SQL SQuirreL, seleccione **Aliases**. A continuación, haga clic en el icono de **+** para crear un nuevo alias de conexión.

    ![agregar nuevo alias](./media/apache-hadoop-connect-hive-jdbc-driver/aliases.png)

6. Use los siguientes valores para el cuadro de diálogo **Add Alias** (Agregar alias).

    * **Name** (Nombre): Hive en HDInsight.

    * **Driver** (Controlador): use la lista desplegable para seleccionar el controlador **Hive**.

    * **Dirección URL**: `jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`

        Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight.

    * **User Name** (Nombre de usuario): nombre de la cuenta de inicio de sesión del clúster de HDInsight. El valor predeterminado es `admin`.

    * **Password** (Contraseña): contraseña para la cuenta de inicio de sesión del clúster.

 ![cuadro de diálogo para agregar alias](./media/apache-hadoop-connect-hive-jdbc-driver/addalias.png)

    Use el botón **Test** (Probar) para comprobar que la conexión funciona. Cuando aparezca el cuadro de diálogo **Connect to: Hive on HDInsight** (Conectar a: Hive en HDInsight), seleccione **Connect** (Conectar) para realizar la prueba. Si la prueba se realiza con éxito, verá un cuadro de diálogo **Connection successful** (Conexión correcta). Si se produce un error, consulte [Solución de problemas](#troubleshooting).

    Use el botón **OK** (Aceptar) situado en la parte inferior del cuadro de diálogo **Add Alias** (Agregar alias) para guardar el alias de conexión.

7. En la lista desplegable **Connect to** (Conectar a), en la parte superior de SQL SQuirreL, seleccione **Hive on HDInsight** (Hive en HDInsight). Cuando se le pida, seleccione **Connect** (Conectar).

    ![cuadro de diálogo de conexión](./media/apache-hadoop-connect-hive-jdbc-driver/connect.png)

8. Una vez conectado, escriba la siguiente consulta en el cuadro de diálogo de consulta SQL y seleccione el icono **Run** (Ejecutar). El área de resultados debe mostrar los resultados de la consulta.

        select * from hivesampletable limit 10;

    ![cuadro de diálogo de consulta de SQL, incluidos los resultados](./media/apache-hadoop-connect-hive-jdbc-driver/sqlquery.png)

## <a name="connect-from-an-example-java-application"></a>Conexión desde una aplicación de Java de ejemplo

Un ejemplo de uso de un cliente de Java para realizar una consulta Hive en HDInsight está disponible en [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc). Siga las instrucciones del repositorio para crear y ejecutar el ejemplo.

## <a name="troubleshooting"></a>solución de problemas

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Error inesperado al intentar abrir una conexión SQL

**Síntomas**: si se conecta a un clúster de HDInsight con la versión 3.3 u otra posterior, es posible que reciba un mensaje donde se indique que se produjo un error inesperado. Se iniciará el seguimiento de la pila para este error con las siguientes líneas:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Causa**: este error lo causa un archivo commons-codec.jar de una versión anterior que está incluido en SQuirreL.

**Resolución**: para corregir este error, siga estos pasos:

1. Descargue el archivo commons-codec.jar desde el clúster de HDInsight.

        scp USERNAME@CLUSTERNAME:/usr/hdp/current/hive-client/lib/commons-codec*.jar ./commons-codec.jar

2. Salga de SQuirreL y, luego, vaya al directorio donde está instalado SQuirreL en el sistema. En el directorio de SQuirreL, bajo el directorio `lib` , reemplace el archivo commons-codec.jar existente por el descargado desde el clúster de HDInsight.

3. Reinicie SQuirreL. El error ya no debería ocurrir al conectarse a Hive en HDInsight.

## <a name="next-steps"></a>pasos siguientes

Ahora que aprendió a usar JDBC para que funcione con Hive, utilice los siguientes vínculos para explorar otras formas de trabajar con HDInsight de Azure.

* [Visualize Hive data with Microsoft Power BI in Azure HDInsight](apache-hadoop-connect-hive-power-bi.md) (Visualización de datos de Hive con Microsoft Power BI en Azure HDInsight)
* [Visualización de datos de Interactive Query Hive con Power BI en Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Use Zeppelin para ejecutar consultas de Hive en Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Conexión de Excel a Hadoop en Azure HDInsight con Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Conexión de Excel a Hadoop con Power Query](apache-hadoop-connect-excel-power-query.md).
* [Conectarse a Azure HDInsight y ejecutar consultas de Hive con Herramientas de Data Lake para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Uso de Herramientas de Azure HDInsight para Visual Studio Code](../hdinsight-for-vscode.md).
* [Carga de datos en HDInsight](../hdinsight-upload-data.md)
* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de trabajos de MapReduce con HDInsight](hdinsight-use-mapreduce.md)
