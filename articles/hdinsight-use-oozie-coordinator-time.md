<properties  linkid="hdinsight-use-time-based-oozie-coordinator-with-hdinsight" urlDisplayName="Use time-based Oozie Coordinator with HDInsight" pageTitle="Use time-based Oozie Coordinator with HDInsight | Microsoft Azure" metaKeywords="" description="Use time-based Oozie Coordinator with HDInsight, a big data solution. Learn how to define Oozie workflows and coordinators, and submit Oozie coordinator jobs." metaCanonical="" services="hdinsight" documentationCenter="" title="Use time-based Oozie Coordinator with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

# Uso del coordinador de Oozie de tiempo con HDInsight

Obtenga más información acerca de cómo definir los flujos de trabajo y los coordinadores, así como el modo de desencadenar los trabajos del coordinador basados en el tiempo. Le resultará útil repasar el [Uso de Oozie con HDInsight](../hdinsight-use-oozie/) antes de leer este artículo.

**Duración aproximada:** 40 minutos

## En este artículo

1.  [&iquest;Qué es Oozie?](#whatisoozie) 
2.  [Requisitos previos](#prerequisites) 
3.  [Definición del archivo de flujo de trabajo de Oozie](#defineworkflow)
4.  [Implementación del proyecto de Oozie y preparación del tutorial](#deploy)
5.  [Ejecución del flujo de trabajo](#run) 
6.  [Pasos siguientes](#nextsteps)

## <a id="whatisoozie" ></a>&iquest;Qué es Oozie?

Oozie de Apache es un sistema de coordinación o flujo de trabajo que administra trabajos de Hadoop. Se integra con la pila de Hadoop y es compatible con los trabajos de Hadoop para MapReduce, Pig, Hive y Sqoop de Apache. También puede usarse para programar trabajos específicos de un sistema, como scripts de shell o programas Java.

El flujo de trabajo que se implementará consta de dos acciones:

![Diagrama de flujo de trabajo](./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Workflow.Diagram.png)

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

## <a id="prerequisites" ></a>Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

* Una **estación de trabajo** con Azure PowerShell instalado y configurado. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](/es-es/manage/install-and-configure-windows-powershell/).
  Para ejecutar scripts de PowerShell, debe ejecutar Azure PowerShell como administrador y establecer la directiva de ejecución en *RemoteSigned*. Consulte [Running Windows PowerShell scripts][1].
* Un **clúster de HDInsight**. Para obtener información acerca de cómo crear un clúster de HDInsight, consulte [Aprovisionamiento de clústeres de HDInsight](/es-es/documentation/articles/hdinsight-provision-clusters/) o [Introducción a HDInsight](/es-es/documentation/articles/hdinsight-get-started/). Para completar el tutorial, necesitará los datos siguientes:
  
  <table  border="1">
    <tr><th>Propiedad del clúster</th>
  <th>Nombre de variable de PowerShell</th>
  <th>Valor</th>
  <th>Descripción</th>
  </tr>
  
    <tr><td>Nombre del clúster de HDInsight</td>
  <td>$clusterName</td>
  <td  />
  <td>El cluster de HDInsight al que aplicará este tutorial.</td>
  </tr>
  
    <tr><td>Nombre de usuario del clúster de HDInsight</td>
  <td>$clusterUsername</td>
  <td  />
  <td>El nombre de usuario del clúster de HDInsight. </td>
  </tr>
  
    <tr><td>Contraseña del usuario del clúster de HDInsight </td>
  <td>$clusterPassword</td>
  <td  />
  <td>La contraseña de usuario del clúster de HDInsight.</td>
  </tr>
  
    <tr><td>Nombre de la cuenta de almacenamiento de Azure</td>
  <td>$storageAccountName</td>
  <td  />
  <td>Cuenta de almacenamiento de Azure disponible para el clúster de HDInsight. Para este tutorial, use la cuenta de almacenamiento predeterminada especificada durante el proceso de aprovisionamiento del clúster.</td>
  </tr>
  
    <tr><td>Nombre del contenedor de blobs de Azure</td>
  <td>$containerName</td>
  <td  />
  <td>Para este ejemplo, use el contenedor de almacenamiento de blobs de Azure utilizado para el sistema de archivos predeterminado del clúster de HDInsight. De manera predeterminada, tiene el mismo nombre que el del clúster de HDInsight.</td>
  </tr>
  
    </table>

* Una **Base de datos SQL de Azure**. Debe configurar una regla de firewall para que el servidor de Base de datos SQL permita el acceso desde la estación de trabajo. Para obtener instrucciones acerca de cómo crear una base de datos SQL y configurar el firewall, consulte [Introducción al uso de la base de datos SQL de Microsoft Azure](../sql-database-get-started/). En este artículo se proporciona un script de PowerShell para crear la tabla de base de datos SQL requerida para este tutorial.
  
  <table  border="1">
    <tr><th>Propiedad de la base de datos SQL</th>
  <th>Nombre de variable de PowerShell</th>
  <th>Valor</th>
  <th>Descripción</th>
  </tr>
  
    <tr><td>Nombre del servidor de base de datos SQL</td>
  <td>$sqlDatabaseServer</td>
  <td  />
  <td>El servidor de Base de datos SQL en el que Sqoop exportará los datos. </td>
  </tr>
  
    <tr><td>Nombre de inicio de sesión de la base de datos SQL</td>
  <td>$sqlDatabaseLogin</td>
  <td  />
  <td>Nombre de inicio de sesión de la base de datos SQL.</td>
  </tr>
  
    <tr><td>Contraseña de inicio de sesión de la base de datos SQL</td>
  <td>$sqlDatabaseLoginPassword</td>
  <td  />
  <td>Contraseña de inicio de sesión de la base de datos SQL.</td>
  </tr>
  
    <tr><td>Nombre de la base de datos SQL</td>
  <td>$sqlDatabaseName</td>
  <td  />
  <td>Base de datos SQL de Azure en la que Sqoop exportará los datos. </td>
  </tr>
  
    </table>


 
  > [WACOM.NOTE] De forma predeterminada, una base de datos SQL de Azure permite realizar conexiones desde servicios de Azure como HDInsight.
  > Si la configuración del firewall está deshabilitada, debe
  > habilitarla en el Portal de administración de Azure. Para obtener
  > instrucciones acerca de la creación de una base de datos SQL y la
  > configuración de las reglas de firewall, consulte [Creación y
  > configuración de una base de datos
  > SQL](../sql-database-create-configure/).

> [WACOM.NOTE] Complete los valores de las tablas, le resultará útil para completar el tutorial.

## <a id="defineworkflow" ></a>Definición del flujo de trabajo de Oozie y el script de HiveQL relacionado

Las definiciones de los flujos de trabajo de Oozie se escriben en hPDL (un lenguaje de definición de proceso XML). El nombre de archivo de flujo de trabajo predeterminado es *workflow.xml*. Guardará el archivo de flujo de trabajo localmente y lo implementará en el clúster de HDInsight con Azure PowerShell posteriormente en este tutorial.

La acción de Hive en el flujo de trabajo llama a un archivo de script de HiveQL. El archivo de script contiene tres instrucciones de HiveQL:

1.  La instrucción **DROP TABLE** elimina la tabla de Hive log4j en caso de que exista.
2.  La instrucción **CREATE TABLE** crea una tabla externa de Hive log4j que apunta a la ubicación del archivo de registro log4j. El delimitador de campo es ",". El delimitador de línea predeterminado es "\n". La tabla externa de Hive se usa para evitar que el archivo de datos se quite de la ubicación original, en el caso de que desee ejecutar el flujo de trabajo de Oozie varias veces.
4.  La instrucción **INSERT OVERWRITE** cuenta las apariciones de cada tipo de nivel de registro desde la tabla de Hive log4j y guarda el resultado en una ubicación de blobs de almacenamiento de Azure (WASB).

Hay un problema conocido de la ruta de acceso de Hive. Se producirá este problema cuando envíe un trabajo de Oozie. Las instrucciones para solucionarlo se pueden encontrar en [TechNet Wiki][2].

**Para definir el archivo de script de HiveQL para que lo llame el flujo
de trabajo:**

1.  Cree un archivo de texto con el siguiente contenido:
    
		DROP TABLE ${hiveTableName};
		CREATE EXTERNAL TABLE ${hiveTableName}(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION '${hiveDataFolder}';
		INSERT OVERWRITE DIRECTORY '${hiveOutputFolder}' SELECT t4 AS sev, COUNT(*) AS cnt FROM ${hiveTableName} WHERE t4 LIKE '[%' GROUP BY t4;
    
    Existen tres variables que se usan en el script:
    
    * ${hiveTableName}
    * ${hiveDataFolder}
    * ${hiveOutputFolder}
    
    El archivo de definición de flujo de trabajo (workflow.xml en este tutorial) pasará estos valores al script de HiveQL en tiempo de ejecución.
    
2.  Guarde el archivo como **C:\Tutorials\UseOozie\useooziewf.hql** con la codificación **ANSI(ASCII)**. Use el Bloc de notas si el editor de texto no proporciona la opción. Este archivo de script se implementará en el clúster de HDInsight más tarde en el tutorial.

**Para definir un flujo de trabajo**

1.  Cree un archivo de texto con el siguiente contenido:
    
         <workflow data-morhtml="true"-app name="useooziewf" xmlns="uri:oozie:workflow:0.2">
             <start  to = "RunHiveScript"/> 
        		
             <a ction  name="RunHiveScript">
                 <hive  xmlns="uri:oozie:hive-action:0.2">
                     <job data-morhtml="true"-tracker>${jobTracker}</job-tracker>
                     <name data-morhtml="true"-node>${nameNode}</name-node>
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
                 <ok  to="RunSqoopExport"/>
                 <error  to="fail"/>
             </action>
        	
             <a ction  name="RunSqoopExport">
                 <sqoop  xmlns="uri:oozie:sqoop-action:0.2">
                     <job data-morhtml="true"-tracker>${jobTracker}</job-tracker>
                     <name data-morhtml="true"-node>${nameNode}</name-node>
                     <configuration>
                         <property>
                             <name>mapred.compress.map.output</name>
                             <value>true</value>
                         </property>
                     </configuration>
                 <a rg>export</arg>
                 <a rg>--connect</arg> 
                 <a rg>${sqlDatabaseConnectionString}</arg> 
                 <a rg>--table</arg>
                 <a rg>${sqlDatabaseTableName}</arg> 
                 <a rg>--export-dir</arg> 
                 <a rg>${hiveOutputFolder}</arg> 
                 <a rg>-m</arg> 
                 <a rg>1</arg>
                 <a rg>--input-fields-terminated-by</arg>
                 <a rg>"\001"</arg>
                 </sqoop>
                 <ok  to="end"/>
                 <error  to="fail"/>
             </action>
        	
             <kill  name="fail">
                 <message>Job failed, error message[${wf:errorMessage(wf:lastErrorNode())}] </message>
             </kill>
        	
            <end  name="end"/>
         </workflow-app>
    
    Existen dos acciones definidas en el flujo de trabajo. La acción de inicio es *RunHiveScript*. Si la acción ejecuta *ok*, la acción siguiente es *RunSqoopExport*.
    
    RunHiveScript tiene distintas variables. Pasará los valores cuando envíe el trabajo de Oozie desde la estación de trabajo con Azure PowerShell.
    
    <table  border="1">
     <tr><th>Variables de flujo de trabajo</th>
    <th>Descripción</th>
    </tr>
    
     <tr><td>${jobTracker}</td>
    <td>Especifique la dirección URL del seguimiento de trabajo de Hadoop. Use <strong>jobtrackerhost:9010</strong>
     en la versión del clúster de HDInsight 2.0 y 3.0.</td>
    </tr>
    
     <tr><td>${nameNode}</td>
    <td>Especifique la dirección URL del NameNode de Hadoop. Utilice la dirección WASB del sistema de archivos predeterminado. Por ejemplo, <i>wasb://<containerName>@<storageAccountName>.blob.core.windows.net</i>
    .</td>
    </tr>
    
     <tr><td>${queueName}</td>
    <td>Especifica el QueueName al que se enviará el trabajo. Use <strong>default</strong>
    .</td>
    </tr>
    
     </table>
    
    <table  border="1">
     <tr><th>Variable de acción de Hive</th>
    <th>Descripción</th>
    </tr>
    
     <tr><td>${hiveDataFolder}</td>
    <td>El directorio de origen para el comando Create Table de Hive.</td>
    </tr>
    
     <tr><td>${hiveOutputFolder}</td>
    <td>La carpeta de salida para la instrucción INSERT OVERWRITE.</td>
    </tr>
    
     <tr><td>${hiveTableName}</td>
    <td>El nombre de la tabla de Hive que hace referencia a los archivos de datos log4j.</td>
    </tr>
    
     </table>
    
    <table  border="1">
     <tr><th>Variable de acción de Sqoop</th>
    <th>Descripción</th>
    </tr>
    
     <tr><td>${sqlDatabaseConnectionString}</td>
    <td>Cadena de conexión de Base de datos SQL.</td>
    </tr>
    
     <tr><td>${sqlDatabaseTableName}</td>
    <td>La tabla de Base de datos SQL donde se exportarán los datos.</td>
    </tr>
    
     <tr><td>${hiveOutputFolder}</td>
    <td>La carpeta de salida para la instrucción INSERT OVERWRITE de Hive. Se trata de la misma carpeta para Sqoop Export export-dir.</td>
    </tr>
    
     </table>
    
    Para obtener más información acerca del flujo de trabajo de Oozie y el uso de acciones de flujo de trabajo, consulte la [documentación de Oozie 4.0 de Apache][3] (en inglés) para la versión del clúster de HDInsight 3.0 o la [documentación de Oozie 3.3.2 de Apache][4] (en inglés) para la versión del clúster de HDInsight 2.1.

2.  Guarde el archivo como **C:\Tutorials\UseOozie\workflow.xml** con la
    codificación ANSI(ASCII). Use el Bloc de notas si el editor de texto no proporciona la opción.

**Para definir el coordinador**

1.  Cree un archivo de texto con el siguiente contenido:
    
         <coordinator data-morhtml="true"-app name="my_coord_app" frequency="${coordFrequency}" start="${coordStart}" end="${coordEnd}" timezone="${coordTimezone}" xmlns="uri:oozie:coordinator:0.4">
            <a ction>
               <workflow>
                  <a pp data-morhtml="true"-path>${wfPath}</app-path>
               </workflow>
            </action>
         </coordinator-app>
    
    En el archivo de definición se usan cinco variables:
    
	Variable          | Descripción
	------------------|------------
    ${coordFrequency} | Hora de pausa del trabajo. La frecuencia se expresa siempre en minutos.
	${coordStart} | Hora de inicio del trabajo.
	${coordEnd} | Hora de finalización del trabajo.
    ${coordTimezone} | Oozie procesa los trabajos del coordinador en una zona horaria concreta que no sea DST (normalmente UTC), esta zona horaria se denomina "Zona horaria de procesamiento de Oozie".
    ${wfPath} | Ruta de acceso de workflow.xml. Si el nombre del archivo del flujo de trabajo no es el del archivo predeterminado (workflow.xml), debe especificarlo.

2.  Guarde el archivo como **C:\Tutorials\UseOozie\coordinator.xml** con la codificación ANSI(ASCII). Use el Bloc de notas si el editor de texto no proporciona la opción.

## <a id="deploy" ></a>Implementación del proyecto de Oozie y preparación del tutorial

Ejecutará el script de Azure PowerShell para realizar las siguientes acciones:

* Copiar el script de HiveQL (useoozie.hql) en el almacenamiento de blobs de Azure, wasb:///tutorials/useoozie/useoozie.hql.
* Copiar workflow.xml en wasb:///tutorials/useoozie/workflow.xml.
* Copiar coordinator.xml en wasb:///tutorials/useoozie/coordinator.xml.
* Copiar el archivo de datos (/example/data/sample.log) en wasb:///tutorials/useoozie/data/sample.log.
* Crear una tabla de Base de datos SQL para el almacenamiento de datos de exportación de Sqoop. El nombre de la tabla es *log4jLogCount*.

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

* El comando CREATE TABLE crea una tabla interna, también conocida como tabla administrada. El archivo de datos debe ubicarse en el contenedor predeterminado.
* El comando CREATE TABLE mueve el archivo de datos a la carpeta /hive/warehouse/&#60;tablename&#62; en el contenedor predeterminado.
* El comando CREATE EXTERNAL TABLE crea una tabla externa. El archivo de datos puede estar ubicado fuera del contenedor predeterminado.
* El comando CREATE EXTERNAL TABLE no mueve el archivo de datos.
* El comando CREATE EXTERNAL TABLE no permite a las subcarpetas en la carpeta especificada en la cláusula LOCATION. Este es el motivo por el que el tutorial hace una copia del archivo sample.log.

Para obtener más información, consulte [HDInsight: Hive Internal and External Tables Intro][5].

**Para preparar el tutorial**

1.  Abra Windows PowerShell ISE (en la pantalla Inicio de Windows 8, escriba **PowerShell_ISE** y, a continuación, haga clic en **Windows PowerShell ISE**. Consulte [Start Windows PowerShell on Windows 8 and Windows][6]).
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
        $hiveQLScript = "C:\Tutorials\UseOozie\useooziewf.hql"
        $workflowDefinition = "C:\Tutorials\UseOozie\workflow.xml"
        $coordDefinition =  "C:\Tutorials\UseOozie\coordinator.xml"

		# Carpeta de WASB para el almacenamiento de los archivos del tutorial de Oozie.
        $destFolder = "tutorials/useoozie"  # NO usar la ruta de acceso larga aquí
	    
    Para ver más descripciones de las variables, consulte la sección [Requisitos previos](#prerequisites) de este tutorial.

4.  Agregue lo siguiente al script en el panel de scripts:

		# Crear un objeto de contexto de almacenamiento
         $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey
        	
         function uploadOozieFiles()
         {		
             Write-Host "Copy HiveQL script, workflow definition and coordinator definition ..." -ForegroundColor Green
             Set-AzureStorageBlobContent -File $hiveQLScript -Container $containerName -Blob "$destFolder/useooziewf.hql" -Context $destContext
             Set-AzureStorageBlobContent -File $workflowDefinition -Container $containerName -Blob "$destFolder/workflow.xml" -Context $destContext
             Set-AzureStorageBlobContent -File $coordDefinition -Container $containerName -Blob "$destFolder/coordinator.xml" -Context $destContext
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

5.  Haga clic en **Run Script** o presione **F5** para ejecutar el
    script. La salida debe ser similar a:
    
    ![Resultado de preparación del
    tutorial](./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.Preparation.Output1.png)

## <a id="run" ></a>Ejecución de proyecto de Oozie

Azure PowerShell no proporciona actualmente cmdlets para la definición de trabajos de Oozie. Puede usar el cmdlet de PowerShell Invoke-RestMethod para invocar los servicios web de Oozie. La API de servicios web de Oozie es una API HTTP REST JSON. Para obtener más información acerca de la API de servicios web de Oozie, consulte la [documentación de Oozie 4.0 de Apache][3] (en inglés) para la versión del clúster de HDInsight 3.0 o la [documentación de Oozie 3.3.2 de Apache][4] (en inglés) para la versión del clúster de HDInsight 2.1.

**Para enviar un trabajo de Oozie**

1.  Abra Windows PowerShell ISE (en la pantalla Inicio de Windows 8, escriba **PowerShell_ISE** y, a continuación, haga clic en **Windows PowerShell ISE**. Consulte [Start Windows PowerShell on Windows 8 and Windows][6]).

2.  Copie el siguiente script en el panel de scripts y, a continuación,
    configure las catorce primeras variables (omita la sexta,
    $storageUri).

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

		 #Variables de WF/coordinador.
         $coordStart = "2014-03-21T13:45Z"
         $coordEnd = "2014-03-21T13:45Z"
         $coordFrequency = "1440"	# en minutos, 24h x 60m = 1440m
         $coordTimezone = "UTC"	#UTC/GMT
        
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
    
    $coordstart y $coordend son las horas de inicio y fin del flujo de trabajo. Para ver la hora UTC/GMT, busque "utc time" en bing.com. $coordFrequency es la frecuencia en minutos con la que desea ejecutar el flujo de trabajo.

3.  Agregue lo siguiente al script. Esta parte define la carga de Oozie:

		 #Carga de Oozie usada para el envío del servicio web de Oozie
         $OoziePayload =  @"
         <?xml version="1.0" encoding="UTF-8"?>
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
                <name>oozie.coord.application.path</name>
                <value>$oozieWFPath</value>
            </property>
        
            <property>
                <name>wfPath</name>
                <value>$oozieWFPath</value>
            </property>
        
            <property>
                <name>coordStart</name>
                <value>$coordStart</value>
            </property>
        
            <property>
                <name>coordEnd</name>
                <value>$coordEnd</value>
            </property>
        
            <property>
                <name>coordFrequency</name>
                <value>$coordFrequency</value>
            </property>
        
            <property>
                <name>coordTimezone</name>
                <value>$coordTimezone</value>
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
                <value>"$sqlDatabaseConnectionString"</value>
            </property>
        	
            <property>
                <name>sqlDatabaseTableName</name>
                <value>$SQLDatabaseTableName</value>
            </property>
        	
            <property>
                <name>user.name</name>
                <value>admin</value>
            </property>
        	
         </configuration>
         "@


   
    > [WACOM.NOTE] La principal diferencia que se aprecia al comparar con el archivo de carga de envío de flujos de trabajo es la variable **oozie.coord.application.path**. Al enviar una tarea de flujo de trabajo, se usa **oozie.wf.application.path**.

4.  Agregue lo siguiente al script. En esta parte se comprueba el estado del servicio web de Oozie:
    
         function checkOozieServerStatus()
         {
             Write-Host "Checking Oozie server status..." -ForegroundColor Green
             $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/admin/status"
             $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds -OutVariable $OozieServerStatus 
        	    
             $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
             $oozieServerSatus = $jsonResponse[0].("systemMode")
             Write-Host "Oozie server status is $oozieServerSatus..."
        	
             if($oozieServerSatus -notmatch "NORMAL")
             {
                 Write-Host "Oozie server status is $oozieServerSatus...cannot submit Oozie jobs. Check the server status and re-run the job."
                 exit 1
             }
         }

5.  Agregue lo siguiente al script. En esta parte se crea un trabajo de Oozie:
    
         function createOozieJob()
         {

			 # Crear trabajo de Oozie
             Write-Host "Sending the following Payload to the cluster:" -ForegroundColor Green
             Write-Host "`n--------`n$OoziePayload`n--------"
             $clusterUriCreateJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
             $response = Invoke-RestMethod -Method Post -Uri $clusterUriCreateJob -Credential $creds -Body $OoziePayload -ContentType "application/xml" -OutVariable $OozieJobName -debug -Verbose
        	
             $jsonResponse = ConvertFrom-Json (ConvertTo-Json -InputObject $response)
             $oozieJobId = $jsonResponse[0].("id")
             Write-Host "Oozie job id is $oozieJobId..."
        	
             return $oozieJobId
         }


   
    > [WACOM.NOTE] Al enviar una tarea del flujo de trabajo, debe realizar otra llamada de servicio web para iniciar la tarea una vez que esta se haya creado. En este caso, el trabajo de coordinador se desencadena por tiempo. El trabajo se iniciará automáticamente.

6.  Agregue lo siguiente al script. En esta parte se comprueba el estado del trabajo de Oozie:
    
         function checkOozieJobStatus($oozieJobId)
         {

			 # Obtener el estado del trabajo
             Write-Host "Sleeping for $waitTimeBetweenOozieJobStatusCheck seconds until the job metadata is populated in the Oozie metastore..." -ForegroundColor Green
             Start-Sleep -Seconds $waitTimeBetweenOozieJobStatusCheck
        	
             Write-Host "Getting job status and waiting for the job to complete..." -ForegroundColor Green
             $clusterUriGetJobStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?show=info"
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
        	
             Write-Host "$(Get-Date -format 'G'): $oozieJobId is in $JobStatus state!"
             if($JobStatus -notmatch "SUCCEEDED")
             {
                 Write-Host "Check logs at http://headnode0:9014/cluster for detais."
                 exit -1
             }
         }

7.  (Opcional) Agregue lo siguiente al script.
    
         function listOozieJobs()
         {
             Write-Host "Listing Oozie jobs..." -ForegroundColor Green
             $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/jobs"
             $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds 
        	    
             write-host "Job ID                                   App Name        Status      Started                         Ended"
             write-host "----------------------------------------------------------------------------------------------------------------------------------"
             foreach($job in $response.workflows)
             {
                 Write-Host $job.id "`t" $job.appName "`t" $job.status "`t" $job.startTime "`t" $job.endTime
             }
         }
        
         function ShowOozieJobLog($oozieJobId)
         {
             Write-Host "Showing Oozie job info..." -ForegroundColor Green
             $clusterUriStatus = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/$oozieJobId" + "?show=log"
             $response = Invoke-RestMethod -Method Get -Uri $clusterUriStatus -Credential $creds 
             write-host $response
         }
        
         function killOozieJob($oozieJobId)
         {
             Write-Host "Killing the Oozie job $oozieJobId..." -ForegroundColor Green
             $clusterUriStartJob = "https://$clusterName.azurehdinsight.net:443/oozie/v2/job/" + $oozieJobId + "?action=kill" #Valid values for the 'action' parameter are 'start', 'suspend', 'resume', 'kill', 'dryrun', 'rerun', and 'change'.
             $response = Invoke-RestMethod -Method Put -Uri $clusterUriStartJob -Credential $creds | Format-Table -HideTableHeaders -debug
         }

8.  Agregue lo siguiente al script:
    
         checkOozieServerStatus

		 # listOozieJobs
         $oozieJobId = createOozieJob($oozieJobId)
         checkOozieJobStatus($oozieJobId)
		 # ShowOozieJobLog($oozieJobId)
		 # killOozieJob($oozieJobId)
    
    Elimine el signo # si desea ejecutar las funciones adicionales.

9.  Si el clúster de HDinsight es la versión 2.1, reemplace "https://$clusterName.azurehdinsight.net:443/oozie/v2/" por "https://$clusterName.azurehdinsight.net:443/oozie/v1/". La versión del clúster de HDInsight 2.1 no es compatible con la versión 2 de los servicios web.

10. Haga clic en **Run Script** o presione **F5** para ejecutar el script. La salida debe ser similar a:
    
    ![Resultado del flujo de trabajo de ejecución del tutorial](./media/hdinsight-use-oozie-coordinator-time/HDI.UseOozie.RunCoord.Output.png)

11. Conéctese a la Base de datos SQL para ver los datos exportados.

**Para comprobar el registro de errores del trabajo**

Para solucionar los problemas de un flujo de trabajo, puede encontrar el archivo de registro de Oozie en C:\apps\dist\oozie-3.3.2.1.3.2.0-05\oozie-win-distro\logs\Oozie.log en el headnode del clúster. Para obtener información acerca de RDP, consulte [Administración de los clústeres de HDInsight mediante el Portal de administración](/es-es/documentation/articles/hdinsight-administer-use-management-portal/).

**Para volver a ejecutar el tutorial**

Para volver a ejecutar el flujo de trabajo, debe realizar lo siguiente:

* eliminar el archivo de salida del script de Hive
* eliminar los datos en la tabla log4jLogsCount

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

## <a id="nextsteps" ></a>Pasos siguientes

En este tutorial ha aprendido a definir un flujo de trabajo de Oozie y a ejecutar un trabajo de Oozie mediante Azure PowerShell. Para obtener más información, consulte los artículos siguientes:

* [Introducción a
  HDInsight](/es-es/documentation/articles/hdinsight-get-started/)
* [Introducción al emulador de
  HDInsight](/es-es/documentation/articles/hdinsight-get-started-emulator/)
* [Uso del almacenamiento de blobs de Azure con
  HDInsight](/es-es/documentation/articles/hdinsight-use-blob-storage/)
* [Administración de HDInsight con
  PowerShell](/es-es/documentation/articles/hdinsight-administer-use-powershell/)
* [Carga de datos en
  HDInsight](/es-es/documentation/articles/hdinsight-upload-data/)
* [Uso de Sqoop con HDInsight](../hdinsight-use-sqoop/)
* [Uso de Hive con
  HDInsight](/es-es/documentation/articles/hdinsight-use-hive/)
* [Uso de Pig con
  HDInsight](/es-es/documentation/articles/hdinsight-use-pig/)
* [Desarrollo de trabajos de streaming de Hadoop C# para
  HDInsight](/es-es/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
* [Desarrollo de programas MapReduce de Java para
  HDInsight](/es-es/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)



[1]: http://technet.microsoft.com/en-us/library/ee176949.aspx
[2]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx
[3]: http://oozie.apache.org/docs/4.0.0/
[4]: http://oozie.apache.org/docs/3.3.2/
[5]: http://blogs.msdn.com/b/cindygross/archive/2013/02/06/hdinsight-hive-internal-and-external-tables-intro.aspx
[6]: http://technet.microsoft.com/en-us/library/hh847889.aspx
