<properties linkid="hdinsight-use-oozie-with-hdinsight" urlDisplayName="Use Oozie with HDInsight" pageTitle="Use Oozie with HDInsight | Azure" metaKeywords="" description="Use Oozie with HDInsight, a big data solution. Learn how to define an Oozie workflow, and submit an Oozie job." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Oozie with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Uso de Oozie con HDInsight
==========================

Aprenda a definir un flujo de trabajo y a ejecutarlo en HDInsight. Para conocer el coordinador de Oozie, consulte [Uso del coordinador de Oozie de tiempo con HDInsight](../hdinsight-use-oozie-coordinator-time/).

**Duración aproximada:** 40 minutos

En este artículo
----------------

1.  [Qué es Oozie](#whatisoozie)
2.  [Requisitos previos](#prerequisites)
3.  [Definición del archivo de trabajo de Oozie](#defineworkflow)
4.  [Implementación del proyecto de Oozie y preparación del tutorial](#deploy)
5.  [Ejecución del flujo de trabajo](#run)
6.  [Pasos siguientes](#nextsteps)

Qué es Oozie
------------

Oozie de Apache es un sistema de coordinación o flujo de trabajo que administra trabajos de Hadoop. Se integra con la pila de Hadoop y es compatible con los trabajos de Hadoop para MapReduce, Pig, Hive y Sqoop de Apache. También puede usarse para programar trabajos específicos de un sistema, como scripts de shell o programas Java.

El flujo de trabajo que se implementará consta de dos acciones:

![Diagrama de flujo de trabajo](./media/hdinsight-use-oozie/HDI.UseOozie.Workflow.Diagram.png)

1.  Una acción de Hive ejecuta un script de HiveQL para contar las apariciones de cada tipo de nivel de registro en un archivo de registro log4j. Cada registro log4j consta de una línea de campos que contiene uno llamado [LOG LEVEL] que muestra el tipo y la gravedad. Por ejemplo:

         2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
         2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
         2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
         ...

    El resultado del script de Hive será parecido al siguiente:

         [DEBUG] 434
         [ERROR] 3
         [FATAL] 1
         [INFO]  96
         [TRACE] 816
         [WARN]  4

    Para obtener más información acerca de Hive, consulte [Uso de Hive con HDInsight](/es-es/documentation/articles/hdinsight-use-hive/).

2.  Una acción de Sqoop exporta el resultado de la acción de HiveQL a una tabla en Base de datos SQL de Azure. Para obtener más información acerca de Sqoop, consulte [Uso de Sqoop con HDInsight](../hdinsight-use-sqoop/).

> [WACOM.NOTE] Para ver las versiones compatibles de Oozie con clústeres de HDInsight, consulte [Novedades en las versiones de clústeres proporcionadas por HDInsight](/es-es/documentation/articles/hdinsight-component-versioning/).

> [WACOM.NOTE] Estos tutoriales funcionan en la versión del clúster de HDInsight 2.1 y 3.0. Este artículo no se ha probado en un emulador de HDInsight.

Requisitos previos
------------------

Antes de empezar este tutorial, debe tener lo siguiente:

-   Una **estación de trabajo** con Azure PowerShell instalado y configurado. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](/es-es/manage/install-and-configure-windows-powershell/). Para ejecutar scripts de PowerShell, debe ejecutar Azure PowerShell como administrador y establecer la directiva de ejecución en *RemoteSigned*. Consulte [Running Windows PowerShell scripts](http://technet.microsoft.com/en-us/library/ee176949.aspx).
-   Un **clúster de HDInsight**. Para obtener información acerca de cómo crear un clúster de HDInsight, consulte [Aprovisionamiento de clústeres de HDInsight](/es-es/documentation/articles/hdinsight-provision-clusters/) o [Introducción a HDInsight](/es-es/documentation/articles/hdinsight-get-started/). Para completar el tutorial, necesitará los datos siguientes:
 <table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">Propiedad del cl&uacute;ster</th><th data-morhtml="true">Nombre de variable de PowerShell</th><th data-morhtml="true">Valor</th><th data-morhtml="true">Descripci&oacute;n</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nombre del cl&uacute;ster de HDInsight</td><td data-morhtml="true">$clusterName</td><td data-morhtml="true"></td><td data-morhtml="true">El cluster de HDInsight al que aplicar&aacute; este tutorial.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nombre de usuario del cl&uacute;ster de HDInsight</td><td data-morhtml="true">$clusterUsername</td><td data-morhtml="true"></td><td data-morhtml="true">El nombre de usuario del cl&uacute;ster de HDInsight. </td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Contrase&ntilde;a del usuario del cl&uacute;ster de HDInsight </td><td data-morhtml="true">$clusterPassword</td><td data-morhtml="true"></td><td data-morhtml="true">La contrase&ntilde;a de usuario del cl&uacute;ster de HDInsight.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nombre de la cuenta de almacenamiento de Azure</td><td data-morhtml="true">$storageAccountName</td><td data-morhtml="true"></td><td data-morhtml="true">Cuenta de almacenamiento de Azure disponible para el cl&uacute;ster de HDInsight. Para este tutorial, use la cuenta de almacenamiento predeterminada especificada durante el proceso de aprovisionamiento del cl&uacute;ster.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nombre del contenedor de blobs de Azure</td><td data-morhtml="true">$containerName</td><td data-morhtml="true"></td><td data-morhtml="true">Para este ejemplo, use el contenedor de almacenamiento de blobs de Azure utilizado para el sistema de archivos predeterminado del cl&uacute;ster de HDInsight. De manera predeterminada, tiene el mismo nombre que el del cl&uacute;ster de HDInsight.</td></tr>
  </table>

-   Una **Base de datos SQL de Azure**. Debe configurar una regla de firewall para que el servidor de Base de datos SQL permita el acceso desde la estación de trabajo. Para obtener instrucciones acerca de cómo crear una base de datos SQL y configurar el firewall, consulte [Introducción al uso de la base de datos SQL de Microsoft Azure](../sql-database-get-started/). En este artículo se proporciona un script de PowerShell para crear la tabla de base de datos SQL requerida para este tutorial.

<table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">Propiedad de la base de datos SQL</th><th data-morhtml="true">Nombre de variable de PowerShell</th><th data-morhtml="true">Valor</th><th data-morhtml="true">Descripci&oacute;n</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nombre del servidor de base de datos SQL</td><td data-morhtml="true">$sqlDatabaseServer</td><td data-morhtml="true"></td><td data-morhtml="true">El servidor de Base de datos SQL en el que Sqoop exportar&aacute; los datos. </td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nombre de inicio de sesi&oacute;n de la base de datos SQL</td><td data-morhtml="true">$sqlDatabaseLogin</td><td data-morhtml="true"></td><td data-morhtml="true">Nombre de inicio de sesi&oacute;n de la base de datos SQL.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Contrase&ntilde;a de inicio de sesi&oacute;n de la base de datos SQL</td><td data-morhtml="true">$sqlDatabaseLoginPassword</td><td data-morhtml="true"></td><td data-morhtml="true">Contrase&ntilde;a de inicio de sesi&oacute;n de la base de datos SQL.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Nombre de la base de datos SQL</td><td data-morhtml="true">$sqlDatabaseName</td><td data-morhtml="true"></td><td data-morhtml="true">Base de datos SQL de Azure en la que Sqoop exportar&aacute; los datos. </td></tr>
  </table>

    > [WACOM.NOTE] De forma predeterminada, una base de datos SQL de Azure permite realizar conexiones desde servicios de Azure como HDInsight. Si la configuración del firewall está deshabilitada, debe habilitarla en el Portal de administración de Azure. Para obtener instrucciones acerca de la creación de una base de datos SQL y la configuración de las reglas de firewall, consulte [Creación y configuración de una base de datos SQL](../sql-database-create-configure/).

> [WACOM.NOTE] Complete los valores de las tablas, le resultará útil para completar el tutorial.

Definición del flujo de trabajo de Oozie y el script de HiveQL relacionado
--------------------------------------------------------------------------

Las definiciones de los flujos de trabajo de Oozie se escriben en hPDL (un lenguaje de definición de proceso XML). El nombre de archivo de flujo de trabajo predeterminado es *workflow.xml*. Guardará el archivo de flujo de trabajo localmente y lo implementará en el clúster de HDInsight con Azure PowerShell posteriormente en este tutorial.

La acción de Hive en el flujo de trabajo llama a un archivo de script de HiveQL. El archivo de script contiene tres instrucciones de HiveQL:

1.  La instrucción **DROP TABLE** elimina la tabla de Hive log4j en caso de que exista.
2.  La instrucción **CREATE TABLE** crea una tabla externa de Hive log4j Hive que apunta a la
3.  ubicación del archivo de registro log4j. El delimitador de campo es ",". El delimitador de línea predeterminado es "\\n". La tabla externa de Hive se usa para evitar que el archivo de datos se quite de la ubicación original, en el caso de que desee ejecutar el flujo de trabajo de Oozie varias veces.
4.  La instrucción **INSERT OVERWRITE** cuenta las apariciones de cada tipo de nivel de registro desde la tabla de Hive log4j y guarda el resultado en una ubicación de blobs de almacenamiento de Azure (WASB).

Hay un problema conocido de la ruta de acceso de Hive. Se producirá este problema cuando envíe un trabajo de Oozie. Las instrucciones para solucionarlo se pueden encontrar en [TechNet Wiki](http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx).

**Para definir el archivo de script de HiveQL para que lo llame el flujo de trabajo:**

1.  Cree un archivo de texto con el siguiente contenido:

         DROP TABLE ${hiveTableName};
         CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
         INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;

    Existen tres variables que se usan en el script:

    -   \${hiveTableName}
    -   \${hiveDataFolder}
    -   \${hiveOutputFolder}

    El archivo de definición de flujo de trabajo (workflow.xml en este tutorial) pasará estos valores al script de HiveQL en tiempo de ejecución.

2.  Guarde el archivo como **C:\\Tutorials\\UseOozie\\useooziewf.hql** con la codificación **ANSI(ASCII)**. Use el Bloc de notas si el editor de texto no proporciona la opción. Este archivo de script se implementará en el clúster de HDInsight más tarde en el tutorial.

**Para definir un flujo de trabajo**

1.  Cree un archivo de texto con el siguiente contenido:

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
                     <param>hiveOutputFolder=${hiveOutputFolder}</param>
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
                 <arg>${hiveOutputFolder}</arg> 
                 <arg>-m</arg> 
                 <arg>1</arg>
                 <arg>--input-fields-terminated-by</arg>
                 <arg>" <workflow-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
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
                     <param>hiveOutputFolder=${hiveOutputFolder}</param>
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
                 <arg>${hiveOutputFolder}</arg> 
                 <arg>-m</arg> 
                 <arg>1</arg>
                 <arg>--input-fields-terminated-by</arg>
                 <arg>"\001"</arg>
                 </sqoop>
                 <ok to="end"/>
                 <error to="fail"/>
             </action>
            
             <kill name="fail">
                 <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
             </kill>
            
            <end name="end"/>
         </workflow-app>
        01"</arg>
                 </sqoop>
                 <ok to="end"/>
                 <error to="fail"/>
             </action>
            
             <kill name="fail">
                 <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
             </kill>
            
            <end name="end"/>
         </workflow-app>

    Existen dos acciones definidas en el flujo de trabajo. La acción de inicio es *RunHiveScript*. Si la acción ejecuta *ok*, la acción siguiente es *RunSqoopExport*.

    RunHiveScript tiene distintas variables. Pasará los valores cuando envíe el trabajo de Oozie desde la estación de trabajo con Azure PowerShell.

  <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><th data-morhtml="true">Variables de flujo de trabajo</th><th data-morhtml="true">Descripci&oacute;n</th></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${jobTracker}</td><td data-morhtml="true">Especifique la direcci&oacute;n URL del seguimiento de trabajo de Hadoop. Use <strong data-morhtml="true">jobtrackerhost:9010</strong> en la versi&oacute;n del cl&uacute;ster de HDInsight 2.0 y 3.0.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${nameNode}</td><td data-morhtml="true">Especifique la direcci&oacute;n URL del NameNode de Hadoop. Use la direcci&oacute;n predeterminada de WASB del sistema de archivos. Por ejemplo, <i data-morhtml="true">wasb://&lt;containerName&gt;@&lt;storageAccountName&gt;.blob.core.windows.net</i>.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${queueName}</td><td data-morhtml="true">Especifica el QueueName al que se enviar&aacute; el trabajo. Use <strong data-morhtml="true">default</strong>.</td></tr>
 </table>
  <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><th data-morhtml="true">Variable de acci&oacute;n de Hive</th><th data-morhtml="true">Descripci&oacute;n</th></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${hiveDataFolder}</td><td data-morhtml="true">El directorio de origen para el comando Create Table de Hive.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${hiveOutputFolder}</td><td data-morhtml="true">La carpeta de salida para la instrucci&oacute;n INSERT OVERWRITE.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${hiveTableName}</td><td data-morhtml="true">El nombre de la tabla de Hive que hace referencia a los archivos de datos log4j.</td></tr>
 </table>
 <table data-morhtml="true" border="1">
 <tr data-morhtml="true"><th data-morhtml="true">Variable de acci&oacute;n de Sqoop</th><th data-morhtml="true">Descripci&oacute;n</th></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${sqlDatabaseConnectionString}</td><td data-morhtml="true">Cadena de conexi&oacute;n de Base de datos SQL.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${sqlDatabaseTableName}</td><td data-morhtml="true">La tabla de Base de datos SQL donde se exportar&aacute;n los datos.</td></tr>
 <tr data-morhtml="true"><td data-morhtml="true">${hiveOutputFolder}</td><td data-morhtml="true">La carpeta de salida para la instrucci&oacute;n INSERT OVERWRITE de Hive. Esta es la misma carpeta para export-dir de exportaci&oacute;n de Sqoop.</td></tr>
 </table>

    Para obtener más información acerca del flujo de trabajo de Oozie y el uso de acciones de flujo de trabajo, consulte la [documentación de Oozie 4.0 de Apache](http://oozie.apache.org/docs/4.0.0/) (en inglés) para la versión del clúster de HDInsight 3.0 o la [documentación de Oozie 3.3.2 de Apache](http://oozie.apache.org/docs/3.3.2/) (en inglés) para la versión del clúster de HDInsight 2.1.

2.  Guarde el archivo como **C:\\Tutorials\\UseOozie\\workflow.xml** con la codificación ANSI(ASCII). Use el Bloc de notas si el editor de texto no proporciona la opción.

Implementación del proyecto de Oozie y preparación del tutorial
---------------------------------------------------------------

Ejecutará el script de Azure PowerShell para realizar las siguientes acciones:

-   Copiar el almacenamiento de blobs de Azure del script de HiveQL (useoozie.hql), wasb:///tutorials/useoozie/useoozie.hql.
-   Copiar workflow.xml en wasb:///tutorials/useoozie/workflow.xml.
-   Copiar el archivo de datos (/example/data/sample.log) en wasb:///tutorials/useoozie/data/sample.log.
-   Crear una tabla de Base de datos SQL para el almacenamiento de datos de exportación de Sqoop. El nombre de tabla es *log4jLogCount*.

**Descripción del almacenamiento de HDInsight**

HDInsight usa el almacenamiento de blobs de Azure para el almacenamiento de datos. Se llama *WASB* o *Almacenamiento de Azure - Blob*. WASB es la implementación del sistema de archivos distribuido de Hadoop (HDFS) de Microsoft en el almacenamiento de blobs de Azure. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](/es-es/documentation/articles/hdinsight-use-blob-storage/).

Cuando se aprovisiona un clúster de HDInsight, se designan una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs específico de dicha cuenta como sistema de archivos predeterminado, de la misma forma que en HDFS. Además de esta cuenta de almacenamiento, puede agregar más cuentas de almacenamiento desde la misma suscripción de Azure o desde otras diferentes durante el proceso de aprovisionamiento. Para obtener instrucciones acerca de cómo agregar más cuentas de almacenamiento, consulte [Aprovisionamiento de clústeres de HDInsight](/es-es/documentation/articles/hdinsight-provision-clusters/). Para simplificar el script de PowerShell que se utiliza en este tutorial, todos los archivos se almacenan en el contenedor del sistema de archivos predeterminado, ubicado en */tutorials/useoozie*. De forma predeterminada, este contenedor tiene el mismo nombre que el del clúster de HDInsight. La sintaxis de WASB es la siguiente:

    wasb[s]://<ContainerName>@<StorageAccountName>.blob.core.windows.net/<path>/<filename>

> [WACOM.NOTE] La sintaxis *wasb://* es la única compatible con la versión 3.0 del clúster de HDInsight. La sintaxis *asv://* anterior es compatible con los clústeres de las versiones 2.1 y 1.6 de HDInsight, pero no con los de la versión 3.0, y no será compatible con versiones posteriores.

> [WACOM.NOTE] La ruta WASB es una ruta de acceso virtual. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con HDInsight](/es-es/documentation/articles/hdinsight-use-blob-storage/).

Para tener acceso a un archivo almacenado en el contenedor del sistema de archivos predeterminado desde HDInsight se puede usar cualquiera de los URI siguientes (use workflow.xml como ejemplo):

    wasb://mycontainer@mystorageaccount.blob.core.windows.net/tutorials/useoozie/workflow.xml
    wasb:///tutorials/useoozie/workflow.xml
    /tutorials/useoozie/workflow.xml

Si desea obtener acceso al archivo directamente desde la cuenta de almacenamiento, el nombre de blob del archivo es:

    tutorials/useoozie/workflow.xml

**Descripción de la tabla interna y externa de Hive**

Existen determinados aspectos que debe conocer en relación con la tabla interna y externa de Hive:

-   El comando CREATE TABLE crea una tabla interna, también conocida como tabla administrada. El archivo de datos debe ubicarse en el contenedor predeterminado.
-   El comando CREATE TABLE mueve el archivo de datos a la carpeta /hive/warehouse/ en el contenedor predeterminado.
-   El comando CREATE EXTERNAL TABLE crea una tabla externa. El archivo de datos puede estar ubicado fuera del contenedor predeterminado.
-   El comando CREATE EXTERNAL TABLE no mueve el archivo de datos.
-   El comando CREATE EXTERNAL TABLE no permite a las subcarpetas en la carpeta especificada en la cláusula LOCATION. Este es el motivo por el que el tutorial hace una copia del archivo sample.log.

Para obtener más información, consulte [HDInsight: Hive Internal and External Tables Intro](http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx).

**Para preparar el tutorial**

1.  Abra Windows PowerShell ISE (en la pantalla Inicio de Windows 8, escriba **PowerShell\_ISE** y, a continuación, haga clic en **Windows PowerShell ISE**. Consulte [Start Windows PowerShell on Windows 8 and Windows](http://technet.microsoft.com/en-us/library/hh847889.aspx)).
2.  En el panel inferior, ejecute el comando siguiente para conectarse a su suscripción de Azure:

         Add-AzureAccount

    Se le pedirá que escriba las credenciales de la cuenta de Azure. Este método de agregar una conexión de suscripción expira y, transcurridas 12 horas, tendrá que volver a ejecutar el cmdlet.

    > [WACOM.NOTE] Si tiene varias suscripciones de Azure y no desea usar la predeterminada, utilice el cmdlet **Select-AzureSubscription** para seleccionar la suscripción actual.

3.  Copie el siguiente script en el panel de scripts y, a continuación, establezca las seis primeras variables.

         # Variables de WASB
         $storageAccountName = "<StorageAccountName>"
         $containerName = "<BlobStorageContainerName>"
            
         # Variables de Base de datos SQL
         $sqlDatabaseServer = "<SQLDatabaseServerName>"  
         $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
         $sqlDatabaseLoginPassword = "SQLDatabaseLoginPassword>"
         $sqlDatabaseName = "<SQLDatabaseName>"  
         $sqlDatabaseTableName = "log4jLogsCount"
            
         # Archivos de Oozie para el tutorial   
         $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
         $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
            
         # Carpeta de WASB para el almacenamiento de los archivos del tutorial de Oozie.
         $destFolder = "tutorials/useoozie"  # No usar la ruta de acceso larga aquí

    Para ver más descripciones de las variables, consulte la sección [Requisitos previos](#prerequisites) de este tutorial.

4.  Agregue lo siguiente al script en el panel de scripts:

         # Crear un objeto de contexto de almacenamiento
         $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
            
         function uploadOozieFiles()
         {      
             Write-Host "Copy workflow definition and HiveQL script file ..." -ForegroundColor Green
             Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
             Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
         }
                    
         function prepareHiveDataFile()
         {
             Write-Host "Make a copy of the sample.log file ... " -ForegroundColor Green
             Start-CopyAzureStorageBlob -SrcContainer $containerName -SrcBlob "example/data/sample.log" -Context $destContext -DestContainer $containerName -destBlob "$destFolder/data/sample.log" -DestContext $destContext
         }
                    
         function prepareSQLDatabase()
         {
             # Cadena de consulta de SQL para la creación de la tabla log4jLogsCount
             $cmdCreateLog4jCountTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                     [Level] [nvarchar](10) NOT NULL,
                     [Total] float,
                 CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED   
                 (
                 [Level] ASC
                 )
                 )"
                    
             #Crear la tabla log4jLogsCount
             Write-Host "Create Log4jLogsCount table ..." -ForegroundColor Green
             $conn = New-Object System.Data.SqlClient.SqlConnection
             $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
             $conn.open()
             $cmd = New-Object System.Data.SqlClient.SqlCommand
             $cmd.connection = $conn
             $cmd.commandtext = $cmdCreateLog4jCountTable
             $cmd.executenonquery()
                    
             $conn.close()
         }
                    
         # Cargar workflow.xml, coordinator.xml y ooziewf.hql
         uploadOozieFiles;
                    
         # Realizar una copia de example/data/sample.log para example/data/log4j/sample.log
         prepareHiveDataFile;
            
         # Crear tabla log4jlogsCount en Base de datos SQL
         prepareSQLDatabase;

5.  Haga clic en **Ejecutar script** o presione **F5** para ejecutar el script. La salida debe ser similar a:

    ![Resultado de preparación del tutorial](./media/hdinsight-use-oozie/HDI.UseOozie.Preparation.Output1.png)

Ejecución de proyecto de Oozie
------------------------------

Azure PowerShell no proporciona actualmente cmdlets para la definición de trabajos de Oozie. Puede usar el cmdlet de PowerShell Invoke-RestMethod para invocar los servicios web de Oozie. La API de servicios web de Oozie es una API HTTP REST JSON. Para obtener más información acerca de la API de servicios web de Oozie, consulte la [documentación de Oozie 4.0 de Apache](http://oozie.apache.org/docs/4.0.0/) (en inglés) para la versión del clúster de HDInsight 3.0 o la [documentación de Oozie 3.3.2 de Apache](http://oozie.apache.org/docs/3.3.2/) (en inglés) para la versión del clúster de HDInsight 2.1.

**Para enviar un trabajo de Oozie**

1.  Abra Windows PowerShell ISE (en la pantalla Inicio de Windows 8, escriba **PowerShell\_ISE** y, a continuación, haga clic en **Windows PowerShell ISE**. Consulte [Start Windows PowerShell on Windows 8 and Windows](http://technet.microsoft.com/en-us/library/hh847889.aspx)).

2.  Copie el siguiente script en el panel de scripts y, a continuación, configure las diez primeras variables (omita la sexta, \$storageUri).

         #Variables del clúster de HDInsight
         $clusterName = "<HDInsightClusterName>"
         $clusterUsername = "<HDInsightClusterUsername>"
         $clusterPassword = "<HDInsightClusterUserPassword>"
            
         #Variables de almacenamiento de blobs de Azure(WASB
         $storageAccountName = "<StorageAccountName>"
         $storageContainerName = "<BlobContainerName>"
         $storageUri="wasb://$storageContainerName@$storageAccountName.blob.core.windows.net"
            
         #Variables de Base de datos SQL de Azure
         $sqlDatabaseServer = "<SQLDatabaseServerName>"
         $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
         $sqlDatabaseLoginPassword = "<SQLDatabaseloginPassword>"
         $sqlDatabaseName = "<SQLDatabaseName>"  
            
         #Variables WF de Oozie
         $oozieWFPath="$storageUri/tutorials/useoozie"  # El nombre predeterminado es workflow.xml y no necesita especificar el nombre de archivo.
         $waitTimeBetweenOozieJobStatusCheck=10
            
         #Variables de acción de Hive
         $hiveScript = "$storageUri/tutorials/useoozie/useooziewf.hql"
         $hiveTableName = "log4jlogs"
         $hiveDataFolder = "$storageUri/tutorials/useoozie/data"
         $hiveOutputFolder = "$storageUri/tutorials/useoozie/output"
            
         #Variables de acción de Sqoop
         $sqlDatabaseConnectionString = "jdbc:sqlserver://$sqlDatabaseServer.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServer;password=$sqlDatabaseLoginPassword;database=$sqlDatabaseName"
         $sqlDatabaseTableName = "log4jLogsCount"

         $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
         $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    Para ver más descripciones de las variables, consulte la sección [Requisitos previos](#prerequisites) de este tutorial.

3.  Agregue lo siguiente al script. Esta parte define la carga de Oozie:

         #Carga de Oozie usada para el envío del servicio web de Oozie
         $OoziePayload =  @"
         <
         xml version="1.0" encoding="UTF-8"
         >
         <configuration>
            
            <property>
                <name>nameNode</name>
                <value>$storageUrI</value>
            </property>
            
            <property>
                <name>jobTracker</name>
                <value>jobtrackerhost:9010</value>
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
                <value>$hiveScript</value>
            </property>
            
            <property>
                <name>hiveTableName</name>
                <value>$hiveTableName</value>
            </property>
            
            <property>
                <name>hiveDataFolder</name>
                <value>$hiveDataFolder</value>
            </property>
            
            <property>
                <name>hiveOutputFolder</name>
                <value>$hiveOutputFolder</value>
            </property>
            
            <property>
                <name>sqlDatabaseConnectionString</name>
                <value>&quot;$sqlDatabaseConnectionString&quot;</value>
            </property>
            
            <property>
                <name>sqlDatabaseTableName</name>
                <value>$SQLDatabaseTableName</value>
            </property>
            
            <property>
                <name>user.name</name>
                <value>admin</value>
            </property>
            
            <property>
                <name>oozie.wf.application.path</name>
                <value>$oozieWFPath</value>
            </property>
            
         </configuration>
         "@

4.  Agregue lo siguiente al script. En esta parte se comprueba el estado del servicio web de Oozie:

         Write-Host "Checking Oozie server status..." -ForegroundColor Green
         $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
         $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus 
            
         $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
         $oozieServerSatus = $jsonResponse[0].("systemMode")
         Write-Host "Oozie server status is $oozieServerSatus..."

5.  Agregue lo siguiente al script. En esta parte se crea e inicia un trabajo de Oozie:

         # Crear trabajo de Oozie
         Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
         Write-Host "`n--------`n$OoziePayload`n--------"
         $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
         $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName #-debug

         $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
         $oozieJobId = $jsonResponse[0].("id")
         Write-Host "Oozie job id is $oozieJobId..."

         # Iniciar trabajo de Oozie
         Write-Host "Starting the Oozie job $oozieJobId..." -ForegroundColor Green
         $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "
         action=start"
         $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders #-debug

6.  Agregue lo siguiente al script. En esta parte se comprueba el estado del trabajo de Oozie:

         # Obtener el estado del trabajo
         Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
         Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck

         Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
         $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "
         show=info"
         $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
         $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
         $JobStatus = $jsonResponse[0].("status")

         while($JobStatus -notmatch "SUCCEEDED|KILLED")
         {
             Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state...waiting $waitTimeBetweenOozieJobStatusCheck seconds for the job to complete..."
             Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
             $response = Invoke-RestMethod -Method Get -Uri $clusterUriGetJobStatus -Credential $creds 
             $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
             $JobStatus = $jsonResponse[0].("status")
         }

         Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!" -ForegroundColor Green

7.  Si el clúster de HDinsight es la versión 2.1, reemplace "https://\$clusterName.azurehdinsight.net:443/oozie/v2/" por "https://\$clusterName.azurehdinsight.net:443/oozie/v1/". La versión del clúster de HDInsight 2.1 no es compatible con la versión 2 de los servicios web.

8.  Haga clic en **Ejecutar script** o presione **F5** para ejecutar el script. La salida debe ser similar a:

    ![Resultado del flujo de trabajo de ejecución del tutorial](./media/hdinsight-use-oozie/HDI.UseOozie.RunWF.Output.png)

9.  Conéctese a la Base de datos SQL para ver los datos exportados.

**Para comprobar el registro de errores del trabajo**

Para solucionar los problemas de un flujo de trabajo, puede encontrar el archivo de registro de Oozie en *C:\\apps\\dist\\oozie-3.3.2.1.3.2.0-05\\oozie-win-distro\\logs\\Oozie.log* o en *C:\\apps\\dist\\oozie-4.0.0.2.0.7.0-1528\\oozie-win-distro\\logs\\Oozie.log* desde el nodo principal del clúster. Para obtener información acerca de RDP, consulte [Administración de los clústeres de HDInsight mediante el Portal de administración](/es-es/documentation/articles/hdinsight-administer-use-management-portal/).

**Para volver a ejecutar el tutorial**

Para volver a ejecutar el flujo de trabajo, debe realizar lo siguiente:

-   eliminar el archivo de salida del script de Hive
-   eliminar los datos en la tabla log4jLogsCount

Aquí tiene un script de PowerShell de ejemplo que puede usar:

    $storageAccountName = "<WindowsAzureStorageAccountName>"
    $containerName = "<ContainerName>"

    #Variables de Base de datos SQL
    $sqlDatabaseServer = "<SQLDatabaseServerName>"
    $sqlDatabaseLogin = "<SQLDatabaseLoginName>"
    $sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>"
    $sqlDatabaseName = "<SQLDatabaseName>"
    $sqlDatabaseTableName = "log4jLogsCount"

    Write-host "Delete the Hive script output file ..." -ForegroundColor Green
    $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
    Remove-AzureStorageBlob -Context $destContext -Blob "tutorials/useoozie/output/000000_0" -Container $containerName

    Write-host "Delete all the records from the log4jLogsCount table ..." -ForegroundColor Green
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabaseLoginPassword;Encrypt=true;Trusted_Connection=false;"
    $conn.open()
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.connection = $conn
    $cmd.commandtext = "delete from $sqlDatabaseTableName"
    $cmd.executenonquery()

    $conn.close()

Pasos siguientes
----------------

En este tutorial ha aprendido a definir un flujo de trabajo de Oozie y a ejecutar un trabajo de Oozie mediante Azure PowerShell. Para obtener más información, consulte los artículos siguientes:

-   [Uso del coordinador de Oozie de tiempo con HDInsight](../hdinsight-use-oozie-coordinator-time/)
-   [Introducción a HDInsight](/es-es/documentation/articles/hdinsight-get-started/)
-   [Introducción al emulador de HDInsight](/es-es/documentation/articles/hdinsight-get-started-emulator/)
-   [Uso del almacenamiento de blobs de Azure con HDInsight](/es-es/documentation/articles/hdinsight-use-blob-storage/)
-   [Administración de HDInsight con PowerShell](/es-es/documentation/articles/hdinsight-administer-use-powershell/)
-   [Carga de datos en HDInsight](/es-es/documentation/articles/hdinsight-upload-data/)
-   [Uso de Sqoop con HDInsight](../hdinsight-use-sqoop/)
-   [Uso de Hive con HDInsight](/es-es/documentation/articles/hdinsight-use-hive/)
-   [Uso de Pig con HDInsight](/es-es/documentation/articles/hdinsight-use-pig/)
-   [Desarrollo de trabajos de streaming de Hadoop C\# para HDInsight](/es-es/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [Desarrollo de programas MapReduce de Java para HDInsight](/es-es/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)

