---
title: Uso de flujos de trabajo de Oozie de Hadoop en Azure HDInsight basado en Linux| Microsoft Docs
description: Use Oozie de Hadoop en HDInsight basado en Linux. Aprenda a definir un flujo de trabajo de Oozie y enviar un trabajo de Oozie.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d7603471-5076-43d1-8b9a-dbc4e366ce5d
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: larryfr
ms.openlocfilehash: 53c249c24af774499becdf18350ff75dbc2ab861
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2018
---
# <a name="use-oozie-with-hadoop-to-define-and-run-a-workflow-on-linux-based-azure-hdinsight"></a>Uso de Oozie con Hadoop para definir y ejecutar un flujo de trabajo en Azure HDInsight basado en Linux

[!INCLUDE [oozie-selector](../../includes/hdinsight-oozie-selector.md)]

Obtenga información sobre cómo usar Apache Oozie con Hadoop en Azure HDInsight. Oozie es un sistema de coordinación y flujos de trabajo que administra trabajos de Hadoop. Oozie se integra con la pila de Hadoop y admite los siguientes trabajos:

* Apache MapReduce
* Apache Pig
* Apache Hive
* Apache Sqoop

Oozie también puede usarse para programar trabajos específicos de un sistema, como scripts de shell o programas Java.

> [!NOTE]
> Otra opción para definir los flujos de trabajo con HDInsight es utilizar Azure Data Factory. Para obtener más información sobre Data Factory, consulte [Uso de Pig y Hive con Data Factory][azure-data-factory-pig-hive].

> [!IMPORTANT]
> Oozie no está habilitado en HDInsight unido a un dominio.

## <a name="prerequisites"></a>requisitos previos

* **Un clúster de HDInsight**: Consulte [Introducción a HDInsight en Linux](/hadoop/apache-hadoop-linux-tutorial-get-started.md)

> [!IMPORTANT]
> Los pasos descritos en este documento requieren un clúster de HDInsight que use Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las versiones posteriores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="example-workflow"></a>Flujo de trabajo de ejemplo

El flujo de trabajo usado en este documento contiene dos acciones. Las acciones son definiciones de tareas, como la ejecución de Hive, Sqoop, MapReduce o cualquier otro proceso:

![Diagrama de flujo de trabajo][img-workflow-diagram]

1. Una acción de Hive ejecuta un script de HiveQL para extraer los registros de la tabla **hivesampletable** incluida con HDInsight. Cada fila de datos describe una visita de un dispositivo móvil específico. El formato de registro es similar al texto siguiente:

        8       18:54:20        en-US   Android Samsung SCH-i500        California     United States    13.9204007      0       0
        23      19:19:44        en-US   Android HTC     Incredible      Pennsylvania   United States    NULL    0       0
        23      19:19:46        en-US   Android HTC     Incredible      Pennsylvania   United States    1.4757422       0       1

    El script de Hive utilizado en este documento cuenta el número total de visitas a cada plataforma (por ejemplo, Android o iPhone) y almacena los recuentos en una nueva tabla de Hive.

    Para más información sobre Hive, consulte [Uso de Hive con HDInsight][hdinsight-use-hive].

2. Una acción de Sqoop exporta el contenido de la nueva tabla de Hive a una tabla creada en Azure SQL Database. Para obtener más información sobre Sqoop, consulte [Uso de Hadoop Sqoop con HDInsight][hdinsight-use-sqoop].

> [!NOTE]
> Para ver las versiones de Oozie compatibles en los clústeres de HDInsight, consulte [Novedades en las versiones de clústeres de Hadoop proporcionadas por HDInsight][hdinsight-versions].

## <a name="create-the-working-directory"></a>Creación del directorio de trabajo

Oozie espera que almacene todos los recursos necesarios para un trabajo en el mismo directorio. Este ejemplo usa **wasb:///tutorials/useoozie**. Para crear el directorio, siga estos pasos:

1. Conéctese al clúster de HDInsight con SSH:

    ```bash
    ssh sshuser@clustername-ssh.azurehdinsight.net
    ```

    Reemplace `sshuser` por el nombre de usuario SSH del clúster. Reemplace `clustername` por el nombre del clúster. Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Para crear el directorio, use el comando siguiente:

    ```bash
    hdfs dfs -mkdir -p /tutorials/useoozie/data
    ```

    > [!NOTE]
    > El parámetro `-p` hace que todos los directorios de la ruta de acceso se creen. El directorio **data** se usará para almacenar los datos que usa el script **useooziewf.hql**.

3. Para asegurarse de que Oozie pueda suplantar su cuenta de usuario, use el comando siguiente:

    ```bash
    sudo adduser username users
    ```

    Reemplace `username` por su nombre de usuario de SSH.

    > [!NOTE]
    > Puede omitir los errores que indican que el usuario ya es miembro del grupo `users`.

## <a name="add-a-database-driver"></a>Adición de un controlador de base de datos

Puesto que este flujo de trabajo usa Sqoop para exportar datos a SQL Database, debe proporcionar una copia del controlador JDBC que se utiliza para interactuar con SQL Database. Para copiar el controlador JDBC en el directorio de trabajo, use el siguiente comando desde la sesión SSH:

```bash
hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc*.jar /tutorials/useoozie/
```

Si para el flujo de trabajo se han usado otros recursos, como un archivo jar que contiene una aplicación MapReduce, también tendrá que agregar estos recursos.

## <a name="define-the-hive-query"></a>Definición de la consulta de Hive

Siga estos pasos para crear un script de lenguaje de consulta de Hive (HiveQL) que defina una consulta. Usará la consulta en un flujo de trabajo de Oozie más adelante en este documento.

1. Desde la conexión SSH, use el comando siguiente para crear un archivo denominado `useooziewf.hql`:

    ```bash
    nano useooziewf.hql
    ```

3. Cuando se abra el editor nano de GNU, use la siguiente consulta como contenido del archivo:

    ```hiveql
    DROP TABLE ${hiveTableName};
    CREATE EXTERNAL TABLE ${hiveTableName}(deviceplatform string, count string) ROW FORMAT DELIMITED
    FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
    INSERT OVERWRITE TABLE ${hiveTableName} SELECT deviceplatform, COUNT(*) as count FROM hivesampletable GROUP BY deviceplatform;
    ```

    Estas son las dos variables que se usan en el script:

    * `${hiveTableName}`: contiene el nombre de la tabla que se va a crear.

    * `${hiveDataFolder}`: contiene la ubicación para almacenar los archivos de datos de la tabla.

    El archivo de definición de flujo de trabajo (workflow.xml en este tutorial) pasa estos valores a este script de HiveQL en tiempo de ejecución.

4. Para salir del editor, seleccione Ctrl+X. Cuando se le pida, seleccione `Y` para guardar el archivo, escriba `useooziewf.hql` como nombre de archivo y, a continuación, seleccione **Entrar**.

5. Use los comandos siguientes para copiar `useooziewf.hql` en `wasb:///tutorials/useoozie/useooziewf.hql`:

    ```bash
    hdfs dfs -put useooziewf.hql /tutorials/useoozie/useooziewf.hql
    ```

    Este comando almacena el archivo `useooziewf.hql` en el almacenamiento compatible con HDFS para el clúster.

## <a name="define-the-workflow"></a>Definición del flujo de trabajo

Las definiciones de flujo de trabajo de Oozie se escriben en lenguaje de definición de proceso de Hadoop (hPDL), que es un lenguaje de definición de proceso XML. Use los pasos siguientes para definir el flujo de trabajo:

1. Use la instrucción siguiente para crear y editar un archivo nuevo:

    ```bash
    nano workflow.xml
    ```

2. Cuando se abra el editor nano, escriba el siguiente XML como contenido del archivo:

    ```xml
    <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
        <start to = "RunHiveScript"/>
        <action name="RunHiveScript">
        <hive xmlns="uri:oozie:hive-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.job.queue.name</name>
                <value>${queueName}</value>
            </property>
            </configuration>
            <script>${hiveScript}</script>
            <param>hiveTableName=${hiveTableName}</param>
            <param>hiveDataFolder=${hiveDataFolder}</param>
        </hive>
        <ok to="RunSqoopExport"/>
        <error to="fail"/>
        </action>
        <action name="RunSqoopExport">
        <sqoop xmlns="uri:oozie:sqoop-action:0.2">
            <job-tracker>${jobTracker}</job-tracker>
            <name-node>${nameNode}</name-node>
            <configuration>
            <property>
                <name>mapred.compress.map.output</name>
                <value>true</value>
            </property>
            </configuration>
            <arg>export</arg>
            <arg>--connect</arg>
            <arg>${sqlDatabaseConnectionString}</arg>
            <arg>--table</arg>
            <arg>${sqlDatabaseTableName}</arg>
            <arg>--export-dir</arg>
            <arg>${hiveDataFolder}</arg>
            <arg>-m</arg>
            <arg>1</arg>
            <arg>--input-fields-terminated-by</arg>
            <arg>"\t"</arg>
            <archive>sqljdbc41.jar</archive>
            </sqoop>
        <ok to="end"/>
        <error to="fail"/>
        </action>
        <kill name="fail">
        <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
        </kill>
        <end name="end"/>
    </workflow-app>
    ```

    Existen dos acciones definidas en el flujo de trabajo:

   * `RunHiveScript`: esta acción es la acción de inicio y ejecuta el script de Hive `useooziewf.hql`.

   * `RunSqoopExport`: esta acción exporta los datos creados a partir del script de Hive a SQL Database con Sqoop. Esta acción solo se ejecutará si la acción `RunHiveScript` es correcta.

     El flujo de trabajo tiene varias entradas, como `${jobTracker}`. Estas entradas se reemplazarán por los valores que usa en la definición del trabajo. La definición del trabajo se creará posteriormente en este documento.

     Tenga en cuenta también la entrada `<archive>sqljdbc4.jar</archive>` de la sección de Sqoop. Esta entrada indica a Oozie que haga que este archivo esté disponible para Sqoop cuando se ejecuta esta acción.

3. Para guardar el archivo, seleccione Ctrl+X, escriba `Y` y, a continuación, seleccione **Entrar**. 

4. Use el comando siguiente para copiar el archivo `workflow.xml` en `/tutorials/useoozie/workflow.xml`:

    ```bash
    hdfs dfs -put workflow.xml /tutorials/useoozie/workflow.xml
    ```

## <a name="create-the-database"></a>Creación de la base de datos

Para crear una base de datos SQL Database, siga los pasos en el documento [Creación de una instancia de Azure SQL Database](../sql-database/sql-database-get-started.md). Al crear la base de datos, utilice `oozietest` como nombre de la base de datos. Además, tome nota del nombre del servidor de bases de datos.

### <a name="create-the-table"></a>Cree la tabla

> [!NOTE]
> Hay muchas maneras de conectarse a SQL Database para crear una tabla. En los siguientes pasos se utiliza [FreeTDS](http://www.freetds.org/) desde el clúster de HDInsight.


1. Use el siguiente comando para instalar FreeTDS en el clúster de HDInsight:

    ```bash
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    ```

2. Una vez se ha instalado FreeTDS, use el comando siguiente para conectarse al servidor de SQL Database que creó anteriormente:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <sqlLogin> -P <sqlPassword> -p 1433 -D oozietest
    ```

    Recibirá una salida como el texto siguiente:

        locale is "en_US.UTF-8"
        locale charset is "UTF-8"
        using default charset "UTF-8"
        Default database being set to oozietest
        1>

3. En el símbolo del sistema `1>` , introduzca las líneas siguientes:

    ```sql
    CREATE TABLE [dbo].[mobiledata](
    [deviceplatform] [nvarchar](50),
    [count] [bigint])
    GO
    CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(deviceplatform)
    GO
    ```

    Cuando se haya especificado la instrucción `GO`, se evaluarán las instrucciones anteriores. Estas instrucciones crean una tabla llamada **mobiledata** que usa el flujo de trabajo.

    Para comprobar que se ha creado la tabla, utilice los comandos siguientes:

    ```sql
    SELECT * FROM information_schema.tables
    GO
    ```

    El resultado debe ser parecido al texto siguiente:

        TABLE_CATALOG   TABLE_SCHEMA    TABLE_NAME      TABLE_TYPE
        oozietest       dbo     mobiledata      BASE TABLE

4. Para salir de la utilidad tsql, escriba `exit` en el símbolo `1>`.

## <a name="create-the-job-definition"></a>Creación de una definición de trabajo

La definición del trabajo describe dónde encontrar workflow.xml. También se describe dónde encontrar otros archivos utilizados por el flujo de trabajo, como `useooziewf.hql`. Además, define los valores de las propiedades usadas en el flujo de trabajo y los archivos asociados.

1. Use el comando siguiente para obtener la dirección completa del almacenamiento predeterminado. Esta dirección se usa en el archivo de configuración que cree en el paso siguiente.

    ```bash
    sed -n '/<name>fs.default/,/<\/value>/p' /etc/hadoop/conf/core-site.xml
    ```

    Este comando devuelve información similar al siguiente XML:

    ```xml
    <name>fs.defaultFS</name>
    <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
    ```

    > [!NOTE]
    > Si el clúster de HDInsight usa Azure Storage como almacenamiento predeterminado, el contenido del elemento `<value>` comenzará por `wasb://`. Si se usa Azure Data Lake Store en su lugar, comenzará por `adl://`.

    Guarde el contenido del elemento `<value>`, ya que se utiliza en los pasos siguientes.

2. Utilice el comando siguiente para crear la configuración de definición de trabajo de Oozie:

    ```bash
    nano job.xml
    ```

3. Cuando se abra el editor nano, use el siguiente XML como contenido del archivo:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>

        <property>
        <name>nameNode</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net</value>
        </property>

        <property>
        <name>jobTracker</name>
        <value>headnodehost:8050</value>
        </property>

        <property>
        <name>queueName</name>
        <value>default</value>
        </property>

        <property>
        <name>oozie.use.system.libpath</name>
        <value>true</value>
        </property>

        <property>
        <name>hiveScript</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/useooziewf.hql</value>
        </property>

        <property>
        <name>hiveTableName</name>
        <value>mobilecount</value>
        </property>

        <property>
        <name>hiveDataFolder</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/data</value>
        </property>

        <property>
        <name>sqlDatabaseConnectionString</name>
        <value>"jdbc:sqlserver://serverName.database.windows.net;user=adminLogin;password=adminPassword;database=oozietest"</value>
        </property>

        <property>
        <name>sqlDatabaseTableName</name>
        <value>mobiledata</value>
        </property>

        <property>
        <name>user.name</name>
        <value>YourName</value>
        </property>

        <property>
        <name>oozie.wf.application.path</name>
        <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
    </configuration>
    ```

   * Reemplace todas las instancias de `wasb://mycontainer@mystorageaccount.blob.core.windows.net` por el valor que recibió anteriormente para el almacenamiento predeterminado.

     > [!WARNING]
     > Si la ruta de acceso es una ruta `wasb`, deberá usar la ruta de acceso completa. No la acorte simplemente a `wasb:///`.

   * Reemplace `YourName` por su nombre de inicio de sesión para el clúster HDInsight.
   * Reemplace `serverName`, `adminLogin` y `adminPassword` por la información de Azure SQL Database.

     La mayor parte de la información de este archivo se usa para rellenar los valores utilizados en los archivos workflow.xml o ooziewf.hql, como `${nameNode}`.

     > [!NOTE]
     > La entrada `oozie.wf.application.path` define dónde se encuentra el archivo workflow.xml. Este archivo contiene el flujo de trabajo que se ha ejecutado este trabajo.

5. Para guardar el archivo, seleccione Ctrl+X, escriba `Y` y, a continuación, seleccione **Entrar**.

## <a name="submit-and-manage-the-job"></a>Envío y administración del trabajo

Los pasos siguientes usan el comando Oozie para enviar y administrar flujos de trabajo de Oozie en el clúster. El comando de Oozie es una interfaz sencilla sobre la [API de REST de Oozie](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html).

> [!IMPORTANT]
> Cuando se utiliza el comando Oozie, debe utilizar el nombre de dominio completo para el nodo principal de HDInsight. Este nombre de dominio completo solo es accesible desde el clúster, o si el clúster está en una instancia de Azure Virtual Network, desde otros equipos en la misma red.


1. Use el comando siguiente para obtener la dirección URL del servicio de Oozie:

    ```bash
    sed -n '/<name>oozie.base.url/,/<\/value>/p' /etc/oozie/conf/oozie-site.xml
    ```

    Esto devuelve información similar al siguiente XML:

    ```xml
    <name>oozie.base.url</name>
    <value>http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie</value>
    ```

    La parte `http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:11000/oozie` es la dirección URL que se usa con el comando de Oozie.

2. Use lo siguiente para crear una variable de entorno para la dirección URL, de manera que no tenga que escribirla para cada comando:

    ```bash
    export OOZIE_URL=http://HOSTNAMEt:11000/oozie
    ```

    Reemplace la dirección URL con la que se recibió anteriormente.
3. Para enviar el trabajo, use lo siguiente:

    ```bash
    oozie job -config job.xml -submit
    ```

    Este comando carga la información del trabajo de `job.xml` y la envía a Oozie, pero no la ejecuta.

    Una vez completado el comando, debe devolver el identificador del trabajo; por ejemplo, `0000005-150622124850154-oozie-oozi-W`. Este identificador se utiliza para administrar el trabajo.

4. Para ver el estado del trabajo, use el siguiente comando:

    ```bash
    oozie job -info <JOBID>
    ```

    > [!NOTE]
    > Reemplace `<JOBID>` por el identificador devuelto en el paso anterior.

    Esto devuelve información similar al siguiente texto:

        Job ID : 0000005-150622124850154-oozie-oozi-W
        ------------------------------------------------------------------------------------------------------------------------------------
        Workflow Name : useooziewf
        App Path      : wasb:///tutorials/useoozie
        Status        : PREP
        Run           : 0
        User          : USERNAME
        Group         : -
        Created       : 2015-06-22 15:06 GMT
        Started       : -
        Last Modified : 2015-06-22 15:06 GMT
        Ended         : -
        CoordAction ID: -
        ------------------------------------------------------------------------------------------------------------------------------------

    Este trabajo tiene un estado de `PREP`. Este estado indica que el trabajo se creó, pero no se inició.

5. Para iniciar el trabajo, utilice el comando siguiente:

    ```bash
    oozie job -start JOBID
    ```

    > [!NOTE]
    > Reemplace `<JOBID>` por el identificador devuelto anteriormente.

    Si comprueba el estado después de este comando, estará en estado de ejecución y se devolverá información para las acciones realizadas dentro del trabajo.

6. Cuando la tarea se completa correctamente, puede comprobar que los datos se han generado y exportado a la tabla de SQL Database a través del siguiente comando:

    ```bash
    TDSVER=8.0 tsql -H <serverName>.database.windows.net -U <adminLogin> -P <adminPassword> -p 1433 -D oozietest
    ```

    En el símbolo del sistema `1>`, escriba la siguiente consulta:

    ```sql
    SELECT * FROM mobiledata
    GO
    ```

    La información devuelta es similar al texto siguiente:

        deviceplatform  count
        Android 31591
        iPhone OS       22731
        proprietary development 3
        RIM OS  3464
        Unknown 213
        Windows Phone   1791
        (6 rows affected)

Para más información sobre el comando Oozie, vea [Herramienta de línea de comandos de Oozie](https://oozie.apache.org/docs/4.1.0/DG_CommandLineTool.html).

## <a name="oozie-rest-api"></a>API de REST de Oozie

La API de REST de Oozie le permite crear sus propias herramientas que funcionan con Oozie. Lo siguiente es información específica de HDInsight sobre el uso de la API de REST de Oozie:

* **URI**: se puede acceder a la API de REST desde fuera del clúster en `https://CLUSTERNAME.azurehdinsight.net/oozie`.

* **Autenticación**: autentíquese en la API con la cuenta HTTP del clúster (admin) y la contraseña. Por ejemplo: 

    ```bash
    curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/oozie/versions
    ```

Para obtener más información sobre el uso de la API de REST de Oozie, vea [Oozie Web Services API](https://oozie.apache.org/docs/4.1.0/WebServicesAPI.html) (API de servicios web de Oozie).

## <a name="oozie-web-ui"></a>Interfaz de usuario web de Oozie

La interfaz de usuario web de Oozie ofrece una vista basada en web en el estado de los trabajos de Oozie en el clúster. Con la interfaz de usuario web, puede ver la información siguiente:

   * Estado del trabajo
   * Definición del trabajo
   * Configuración
   * Un gráfico de las acciones en el trabajo
   * Registros para el trabajo

También puede ver detalles de las acciones dentro de un trabajo.

Para acceder a la interfaz de usuario web de Oozie, siga estos pasos:

1. Cree un túnel SSH para el clúster de HDInsight. Para más información, consulte el documento [Uso de un túnel SSH con HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

2. Después de crear un túnel, abra la interfaz de usuario web de Ambari en el explorador web. El URI para el sitio de Ambari es `https://CLUSTERNAME.azurehdinsight.net`. Reemplace `CLUSTERNAME` por el nombre del clúster HDInsight basado en Linux.

3. En el lado izquierdo de la página, seleccione **Oozie** > **Vínculos rápidos** > **Oozie Web UI** (IU web de Oozie).

    ![Imagen de los menús](./media/hdinsight-use-oozie-linux-mac/ooziewebuisteps.png)

4. La interfaz de usuario web de Oozie muestra de forma predeterminada los trabajos del flujo de trabajo en ejecución. Para ver todos los trabajos del flujo de trabajo, seleccione **All Jobs** (Todos los trabajos).

    ![Todos los trabajos mostrados](./media/hdinsight-use-oozie-linux-mac/ooziejobs.png)

5. Seleccione un trabajo para obtener más información sobre él.

    ![Job Info](./media/hdinsight-use-oozie-linux-mac/jobinfo.png)

6. En la pestaña **Job Info** (Información de trabajo), puede ver información básica de trabajo y las acciones individuales dentro del trabajo. En las pestañas en la parte superior puede ver la **Job Definition** (Definición del trabajo), **Job Configuration** (Configuración del trabajo), **Job Log** (Registro del trabajo) o un grafo acíclico dirigido (DAG) del trabajo en **Job DAG** (DAG del trabajo).

   * **Job Log** (Registro del trabajo): seleccione el botón **Get Logs** (Obtener registros) para obtener todos los registros del trabajo o use el campo **Enter Search Filter** (Escribir filtro de búsqueda) para filtrar los registros.

       ![Registro de trabajo](./media/hdinsight-use-oozie-linux-mac/joblog.png)

   * **Job DAG** (DAG del trabajo): el DAG incluye información general gráfica de las rutas de acceso de datos usadas en el flujo de trabajo.

       ![DAG del trabajo](./media/hdinsight-use-oozie-linux-mac/jobdag.png)

7. Si selecciona una de las acciones en la pestaña **Job Info** (Información del trabajo), aparecerá información de la acción. Por ejemplo, seleccione la acción **RunSqoopExport**.

    ![Información de acción](./media/hdinsight-use-oozie-linux-mac/action.png)

8. Puede ver detalles de la acción, incluido un vínculo a la **dirección URL de la consola**. Este vínculo se puede usar para ver la información de seguimiento del trabajo.

## <a name="schedule-jobs"></a>Programación de trabajos

Puede utilizar el coordinador para especificar un inicio, un fin y la frecuencia de repetición para los trabajos. Para definir una programación para el flujo de trabajo, siga estos pasos:

1. Use el comando siguiente para crear un nuevo archivo denominado **coordinator.xml**:

    ```bash
    nano coordinator.xml
    ```

    Use el siguiente XML como contenido del archivo:

    ```xml
    <coordinator-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
        <action>
        <workflow>
            <app-path>${workflowPath}</app-path>
        </workflow>
        </action>
    </coordinator-app>
    ```

    > [!NOTE]
    > Las variables `${...}` se reemplazarán por los valores de la definición del trabajo en tiempo de ejecución. Las variables son las siguientes:
    >
    > * `${coordFrequency}`: el tiempo entre las instancias del trabajo en ejecución.
    > * `${coordStart}`: la hora de inicio del trabajo.
    > * `${coordEnd}`: la hora de finalización del trabajo.
    > * `${coordTimezone}`: los trabajos del coordinador se encuentran en una zona horaria fija sin horario de verano (representado normalmente mediante UTC). Esta zona horaria se conoce como la *zona de horaria de procesamiento de Oozie*.
    > * `${wfPath}`: la ruta de acceso a workflow.xml.

2. Para guardar el archivo, seleccione Ctrl+X, escriba `Y` y, a continuación, seleccione **Entrar**.

3. Use el siguiente comando para copiar el archivo en el directorio de trabajo para este trabajo:

    ```bash
    hadoop fs -put coordinator.xml /tutorials/useoozie/coordinator.xml
    ```

4. Para modificar el archivo `job.xml`, use el comando siguiente:

    ```
    nano job.xml
    ```

    Se han realizado los siguientes cambios:

   * Para indicar a Oozie que ejecute el archivo de coordinador en lugar del flujo de trabajo, cambie `<name>oozie.wf.application.path</name>` por `<name>oozie.coord.application.path</name>`.

   * Para establecer la variable `workflowPath` utilizada por el coordinador, agregue el siguiente código XML:

        ```xml
        <property>
            <name>workflowPath</name>
            <value>wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie</value>
        </property>
        ```

       Reemplace el texto `wasb://mycontainer@mystorageaccount.blob.core.windows` por el valor utilizado en las demás entradas del archivo job.xml.

   * Para definir el inicio, el fin y la frecuencia del coordinador, agregue el siguiente código XML:

        ```xml
        <property>
            <name>coordStart</name>
            <value>2017-05-10T12:00Z</value>
        </property>

        <property>
            <name>coordEnd</name>
            <value>2017-05-12T12:00Z</value>
        </property>

        <property>
            <name>coordFrequency</name>
            <value>1440</value>
        </property>

        <property>
            <name>coordTimezone</name>
            <value>UTC</value>
        </property>
        ```

       Estos valores establecen la hora de inicio en las 12:00 del 10 de mayo de 2017 y la fecha de finalización el 12 de mayo de 2017. El intervalo para ejecutar este trabajo se configura diariamente. La frecuencia está en minutos, por lo que 24 horas x 60 minutos = 1440 minutos. Por último, la zona horaria se establece en UTC.

5. Para guardar el archivo, seleccione Ctrl+X, escriba `Y` y, a continuación, seleccione **Entrar**.

6. Para ejecutar el trabajo, use el siguiente comando:

    ```
    oozie job -config job.xml -run
    ```

    Este comando envía e inicia el trabajo.

7. Si visita la interfaz de usuario web de Oozie y selecciona la pestaña **Coordinator Jobs** (Trabajos de coordinador), verá información similar a la siguiente imagen:

    ![Pestaña de trabajos de coordinador](./media/hdinsight-use-oozie-linux-mac/coordinatorjob.png)

    La entrada **Next Materialization** (Siguiente materialización) contiene la siguiente hora a la que se ejecuta el trabajo.

8. De igual forma que la tarea de flujo de trabajo anterior, si se selecciona la entrada de trabajo en la interfaz de usuario web, se mostrará información sobre el trabajo:

    ![Información de trabajos de coordinador](./media/hdinsight-use-oozie-linux-mac/coordinatorjobinfo.png)

    > [!NOTE]
    > Esta imagen solo muestra ejecuciones correctas del trabajo, no acciones individuales dentro del flujo de trabajo programado. Para ver las acciones individuales, seleccione una de las entradas **Action** (Acción).

    ![Información de acción](./media/hdinsight-use-oozie-linux-mac/coordinatoractionjob.png)

## <a name="troubleshooting"></a>solución de problemas

Con la IU Oozie, puede ver registros de Oozie. La IU de Oozie también contiene vínculos a registros de JobTracker para tareas de MapReduce iniciadas por el flujo de trabajo. El patrón para la solución de problemas debe ser el siguiente:

   1. Ver el trabajo en la interfaz de usuario web de Oozie.

   2. Si hay un error en una acción determinada, seleccione la acción para ver si el campo **Error Message** ofrece más información sobre el error.

   3. Si está disponible, use la dirección URL de la acción para ver más detalles (por ejemplo, registros de JobTracker) para la acción.

Las siguientes son errores específicos que pueden surgir y cómo resolverlos.

### <a name="ja009-cannot-initialize-cluster"></a>JA009: No se puede inicializar el clúster

**Síntomas**: el estado del trabajo cambiará a **SUSPENDED** (SUSPENDIDO). Los detalles del trabajo mostrarán el estado de `RunHiveScript` como **START_MANUAL**. Si se selecciona la acción, aparecerá el mensaje de error siguiente:

    JA009: Cannot initialize Cluster. Please check your configuration for map

**Causa**: las direcciones de Azure Blob Storage usadas en el archivo **job.xml** no incluyen el nombre del contenedor de almacenamiento ni de la cuenta de almacenamiento. El formato de dirección de Blob Storage debe ser `wasb://containername@storageaccountname.blob.core.windows.net`.

**Resolución**: cambiar las direcciones de Blob Storage que usa el trabajo.

### <a name="ja002-oozie-is-not-allowed-to-impersonate-ltuser"></a>JA002: Oozie no tiene permiso para suplantar &lt;USER>

**Síntomas**: el estado del trabajo cambiará a **SUSPENDED** (SUSPENDIDO). Los detalles del trabajo mostrarán el estado de `RunHiveScript` como **START_MANUAL**. Al seleccionar la acción, se muestra el mensaje de error siguiente:

    JA002: User: oozie is not allowed to impersonate <USER>

**Causa**: la configuración actual de permisos no permite a Oozie suplantar la cuenta de usuario especificada.

**Resolución**: Oozie puede suplantar a los usuarios del grupo **users**. Utilice el `groups USERNAME` para ver los grupos de los que es miembro la cuenta de usuario. Si el usuario no es miembro del grupo **users** , use el siguiente comando para agregar el usuario al grupo:

    sudo adduser USERNAME users

> [!NOTE]
> Puede tardar varios minutos antes de que HDInsight reconozca que el usuario se ha agregado al grupo.

### <a name="launcher-error-sqoop"></a>ERROR del selector (Sqoop)

**Síntomas**: el estado del trabajo cambiará a **KILLED** (TERMINADO). Los detalles del trabajo mostrarán el estado de `RunSqoopExport` como **ERROR**. Al seleccionar la acción, se muestra el mensaje de error siguiente:

    Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]

**Causa**: Sqoop no puede cargar el controlador de base de datos necesario para acceder a la base de datos.

**Resolución**: al usar Sqoop desde un trabajo de Oozie, debe incluir el controlador de base de datos con los demás recursos (por ejemplo, workflow.xml) que usa el trabajo. Asimismo, haga referencia al archivo que contiene el controlador de base de datos desde la sección `<sqoop>...</sqoop>` de workflow.xml.

Por ejemplo, para el trabajo de este documento, se usarían los siguientes pasos:

1. Copie el archivo `sqljdbc4.1.jar` en el directorio **/tutorials/useoozie**:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_4.1/enu/sqljdbc41.jar /tutorials/useoozie/sqljdbc41.jar
    ```

2. Modifique `workflow.xml` para agregar el siguiente XML en una nueva línea encima de `</sqoop>`:

    ```xml
    <archive>sqljdbc41.jar</archive>
    ```

## <a name="next-steps"></a>pasos siguientes

En este tutorial ha aprendido a definir un flujo de trabajo de Oozie y a ejecutar un trabajo de Oozie. Para obtener más información sobre el trabajo con HDInsight, consulte los siguientes artículos:

* [Uso del coordinador de Oozie basado en tiempo con Hadoop en HDInsight][hdinsight-oozie-coordinator-time]
* [Carga de datos para trabajos de Hadoop en HDInsight][hdinsight-upload-data]
* [Uso de Sqoop con Hadoop en HDInsight][hdinsight-use-sqoop]
* [Uso de Hive con Hadoop en HDInsight][hdinsight-use-hive]
* [Uso de Pig con Hadoop en HDInsight][hdinsight-use-pig]
* [Desarrollo de programas MapReduce de Java para HDInsight][hdinsight-develop-mapreduce]

[hdinsight-cmdlets-download]: http://go.microsoft.com/fwlink/?LinkID=325563
[azure-data-factory-pig-hive]: ../data-factory/transform-data.md
[hdinsight-oozie-coordinator-time]: hdinsight-use-oozie-coordinator-time.md
[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-use-sqoop]:hadoop/apache-hadoop-use-sqoop-mac-linux.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]:hadoop/hdinsight-use-mapreduce.md
[hdinsight-use-hive]:hadoop/hdinsight-use-hive.md
[hdinsight-use-pig]:hadoop/hdinsight-use-pig.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-get-started-emulator]: hdinsight-get-started-emulator.md
[hdinsight-develop-mapreduce]:hadoop/apache-hadoop-develop-deploy-java-mapreduce-linux.md

[sqldatabase-create-configue]: sql-database-create-configure.md
[sqldatabase-get-started]: sql-database-get-started.md

[azure-create-storageaccount]:../storage/common/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/
[apache-oozie-400]: http://oozie.apache.org/docs/4.0.0/
[apache-oozie-332]: http://oozie.apache.org/docs/3.3.2/

[powershell-download]: http://azure.microsoft.com/downloads/
[powershell-about-profiles]: http://go.microsoft.com/fwlink/?LinkID=113729
[powershell-install-configure]: /powershell/azureps-cmdlets-docs
[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-script]: https://technet.microsoft.com/en-us/library/ee176961.aspx

[cindygross-hive-tables]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx

[img-workflow-diagram]: ./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png
[img-preparation-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png
[img-runworkflow-output]: ./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
