<properties linkid="manage-services-hdinsight-administer-hdinsight-using-powershell" urlDisplayName="HDInsight Administration" pageTitle="Administer HDInsight using PowerShell | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to perform administrative tasks for the HDInsight clusters using PowerShell." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Administer HDInsight using PowerShell" authors="bradsev" />

Administración de HDInsight con PowerShell
==========================================

Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. En este artículo, aprenderá a administrar clústeres de HDInsight con una consola local de Azure PowerShell mediante el uso de Windows PowerShell. Para obtener más información acerca de los cmdlets de HDInsight PowerShell, consulte [Documentación de referencia de los cmdlets de HDInsight](http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx).

**Requisitos previos:**

Antes de empezar este artículo, debe tener lo siguiente:

-   Una suscripción de Azure. Azure es una plataforma basada en suscripción. Los cmdlets de HDInsight PowerShell realizan las tareas con su suscripción. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra](https://www.windowsazure.com/en-us/pricing/purchase-options/), [Ofertas para miembros](https://www.windowsazure.com/en-us/pricing/member-offers/) o [Evaluación gratuita](https://www.windowsazure.com/en-us/pricing/free-trial/).

-   Una estación de trabajo con Azure PowerShell. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

En este artículo
----------------

-   [Aprovisionamiento de un clúster](#provision)
-   [Enumeración y visualización de clústeres](#listshow)
-   [Eliminación de un clúster](#delete)
-   [Concesión/Revocación del acceso a los servicios de HTTP](#httpservices)
-   [Envío de trabajos de MapReduce](#mapreduce)
-   [Envío de trabajos de Hive](#hive)
-   [Carga de datos en el almacenamiento de blobs](#upload)
-   [Descarga de datos de salida de MapReduce del almacenamiento de blobs](#download)

Aprovisionamiento de un clúster de HDInsight
--------------------------------------------

HDInsight utiliza contenedores de almacenamiento de blobs de Azure como sistemas de archivos predeterminados. Es preciso tener una cuenta de almacenamiento de Azure y un contenedor de almacenamiento antes de crear un clúster de HDInsight.

**Para crear una cuenta de Almacenamiento de Azure**

Después de importar el archivo publishsettings, puede usar el siguiente comando para crear una cuenta de almacenamiento:

    # Cree una cuenta de almacenamiento de Azure.
    $storageAccountName = "<StorageAcccountName>"
    $location = "<Microsoft data center>"           # Por ejemplo, "West US"

    New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

> [WACOM.NOTE] La cuenta de almacenamiento debe ubicarse en el mismo centro de datos que el clúster de HDInsight. En la actualidad, solo pueden aprovisionarse clústeres de HDInsight en los siguientes centros de datos:

><ul>
<li>Sudeste asiático</li>
<li>Europa del Norte</li>
<li>Europa occidental</li>
<li>Este de EE. UU.</li>
<li>Oeste de EE. UU.</li>
</ul>

Para obtener información acerca de la creación de una cuenta de almacenamiento de Azure a través del portal de administración, consulte [Creación de una cuenta de almacenamiento](/en-us/manage/services/storage/how-to-create-a-storage-account/).

Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los comandos siguientes para recuperar dicha información:

    # Incluya las cuentas de almacenamiento de la suscripción actual.
    Get-AzureStorageAccount
    # Enumere las claves de una cuenta de almacenamiento.
    Get-AzureStorageKey <StorageAccountName>

Para obtener información acerca de cómo conseguir la información usando el portal de administración, consulte la sección *Visualización, copia y generación de claves de acceso al almacenamiento* de [Administración de cuentas de almacenamiento](/en-us/manage/services/storage/how-to-manage-a-storage-account/).

**Para crear un contendor de almacenamiento de Azure**

PowerShell no puede crear un contenedor de blobs durante el proceso de aprovisionamiento de HDInsight. Puede crear uno con el siguiente script:

    $storageAccountName = "<StorageAccountName>"
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $containerName="<ContainerName>"

    # Cree un objeto de contexto de almacenamiento.
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                           -StorageAccountKey $storageAccountKey  

    # Cree un contenedor de almacenamiento de blobs.
    New-AzureStorageContainer -Name $containerName -Context $destContext

**Para aprovisionar un clúster**

Una vez que tenga preparados la cuenta de almacenamiento y el contenedor de blobs, podrá proceder con la creación de un clúster.

    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $clusterName = "<HDInsightClusterName>"
    $location = "<MicrosoftDataCenter>"
    $clusterNodes = <ClusterSizeInNodes>

    # Obtener la clave de la cuenta de almacenamiento
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

    # Cree un nuevo clúster de HDInsight.
    New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

En la siguiente captura de pantalla se muestra la ejecución del script:

![HDI.PS.Provision](./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png)

Enumeración y visualización de clústeres
----------------------------------------

Use los comandos siguientes para enumerar y mostrar los detalles del clúster:

**Para enumerar todos los clústeres en la suscripción actual**

    Get-AzureHDInsightCluster 

**Para mostrar los detalles del clúster específico en la suscripción actual**

    Get-AzureHDInsightCluster -Name <ClusterName> 

Eliminación de un clúster
-------------------------

Use el comando siguiente para eliminar un clúster:

    Remove-AzureHDInsightCluster -Name <ClusterName> 

Concesión/Revocación del acceso a los servicios de HTTP
-------------------------------------------------------

Los clústeres de HDInsight tienen los siguientes servicios web HTTP (todos estos servicios tienen extremos RESTful):

-   ODBC
-   JDBC
-   Ambari
-   Oozie
-   Templeton

De manera predeterminada, estos servicios se conceden para el acceso. Puede revocar/conceder el acceso. A continuación se ofrece una muestra:

    Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

En la muestra *hdiv2* es el nombre de un clúster de HDInsight.

> [WACOM.NOTE] Al conceder/revocar el acceso, restablecerá el nombre de usuario y la contraseña del usuario del clúster.

Esto también se puede hacer a través del Portal de administración de Azure. Consulte [Administración de HDInsight con el portal de administración](/en-us/documentation/articles/hdinsight-administer-use-management-portal/).

Envío de trabajos de MapReduce
------------------------------

La distribución de clústeres de HDInsight se incluye con algunas muestras de MapReduce. Una de las muestras ilustra el recuento de la frecuencia de las palabras en los archivos de código fuente.

**Para enviar un trabajo de MapReduce**

El siguiente script de PowerShell envía el trabajo de muestra de recuento de palabras:

    $clusterName = "<HDInsightClusterName>"            

    # Defina el trabajo de MapReduce
    $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    # Ejecute el trabajo y muestre el error estándar 
    $wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}

> [WACOM.NOTE] *hadoop-examples.jar* viene con la versión 2.1 de clústeres de HDInsight. Se cambió el nombre del archivo a *hadoop-mapreduce.jar* en la versión 3.0 de clústeres de HDInsight.

Para obtener información acerca del prefijo WASB, consulte [Uso del almacenamiento de blobs de Azure para HDInsight][hdinsight-storage].

**Para descargar la salida del trabajo de MapReduce**

La siguiente script de PowerShell recupera la salida del trabajo de MapReduce desde el último procedimiento:

    $storageAccountName = "<StorageAccountName>"   
    $containerName = "<ContainerName>"             
        
    # Crear el objeto de contexto de la cuenta de almacenamiento
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    # Descargar la salida en el equipo local
    Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    # Mostrar la salida
    cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Para obtener más información acerca del desarrollo y la ejecución de trabajos de MapReduce, consulte [Uso de MapReduce con HDInsight](/en-us/documentation/articles/hdinsight-use-mapreduce/).

Envío de trabajos de Hive
-------------------------

La distribución de clústeres de HDInsight se incluye con una tabla de muestra de Hive llamada *hivesampletable*. Puede usar una consulta "show tables;" de HiveQL para enumerar las tablas de Hive en un clúster.

**Para enviar un trabajo de Hive**

El siguiente script envía un trabajo de Hive para enumerar las tablas de Hive:

    $clusterName = "<HDInsightClusterName>"               

    # Consulta de HiveQL
    $querystring = @"
        SHOW TABLES;
        SELECT * FROM hivesampletable 
            WHERE Country='United Kingdom'
            LIMIT 10;
    "@

    Use-AzureHDInsightCluster -Name $clusterName
    Invoke-Hive $querystring

El trabajo de Hive mostrará primero las tablas de Hive creadas en el clúster y los datos devueltos por hivesampletable.

Para obtener más información acerca del uso de Hive, consulte [Uso de Hive con HDInsight](/en-us/documentation/articles/hdinsight-use-hive/).

Carga de datos en el almacenamiento de blobs
--------------------------------------------

Consulte [Carga de datos en HDInsight](/en-us/documentation/articles/hdinsight-upload-data/).

Descarga de datos de salida de MapReduce del almacenamiento de blobs
--------------------------------------------------------------------

Consulte la sección [Envío de trabajos MapReduce](#mapreduce) en este artículo.

Consulte también
----------------

-   [Documentación de referencia de los cmdlets de HDInsight](http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx)
-   [Administración de HDInsight con el portal de administración](/en-us/documentation/articles/hdinsight-administer-use-management-portal/)
-   [Administración de HDInsight con la interfaz de línea de comandos](/en-us/documentation/articles/hdinsight-administer-use-command-line/)
-   [Aprovisionamiento de clústeres de HDInsight](/en-us/documentation/articles/hdinsight-provision-clusters/)
-   [Carga de datos en HDInsight](/en-us/documentation/articles/hdinsight-upload-data/)
-   [Envío de trabajos de Hadoop mediante programación](/en-us/documentation/articles/hdinsight-submit-hadoop-jobs-programmatically/)
-   [Introducción a HDInsight de Azure](/en-us/documentation/articles/hdinsight-get-started/)

