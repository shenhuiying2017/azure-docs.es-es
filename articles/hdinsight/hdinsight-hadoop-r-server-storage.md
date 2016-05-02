<properties
   pageTitle="Opciones de Almacenamiento de Azure para R Server en HDInsight (versión preliminar) | Azure"
   description="Obtenga información sobre las distintas opciones de almacenamiento disponibles para los usuarios con R Server en HDInsight (versión preliminar)"
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="03/28/2016"
   ms.author="jeffstok"
/>

# Opciones de almacenamiento de Azure del servidor de R en HDInsight (versión preliminar)

R Server en HDInsight (versión preliminar) tiene acceso a los blobs de Azure y al almacenamiento de Azure Data Lake como forma de conservar los datos, el código, los objetos de resultado del análisis, etc.

Cuando crea un clúster de Hadoop en HDInsight, especifica una cuenta de Almacenamiento de Azure. Se designa un contenedor de almacenamiento de blobs desde esa cuenta para conservar el sistema de archivos para el clúster creado, es decir, el Sistema de archivos distribuido de Hadoop (HDFS). Con vistas al rendimiento, el clúster de HDInsight se crea en el mismo centro de datos que la cuenta de almacenamiento principal que especifica. Para obtener más información, consulte [Uso de almacenamiento de blobs de Azure con HDInsight](hdinsight-hadoop-use-blob-storage.md "Uso del almacenamiento de blobs de Azure con HDInsight").


## Uso de varias cuentas de almacenamiento de blobs de Azure

Si es necesario, es posible acceder a varias cuentas de almacenamiento o contenedores de Azure con el clúster de HDI. Para ello, debe especificar las cuentas de almacenamiento adicionales en la interfaz de usuario durante la creación del clúster y seguir estos pasos para poder usarlas en R.

1.	Supongamos que crea un clúster de HDInsight con el nombre de cuenta de almacenamiento "storage1" y con el contenedor predeterminado "container1". También especifica una cuenta de almacenamiento adicional denominada "storage2".  
2.	Ahora copia un archivo "mycsv.csv" al directorio "/share" y quiere analizar dicho archivo.  

    hadoop fs –mkdir /share hadoop fs –copyFromLocal myscsv.scv /share

3.	En código R, establezca el nodo de nombre en "default" e indique el directorio y el archivo que se van a procesar.

    myNameNode <- "default" myPort <- 0

  Ubicación de los datos

    bigDataDirRoot <- "/share"  

  Definición del contexto de proceso de Spark

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  Establecimiento del contexto de proceso

    rxSetComputeContext(mySparkCluster)

  Definición del sistema de archivos HDFS

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  Especificación del archivo de entrada en HDFS que se va a analizar

    inputFile <-file.path(bigDataDirRoot,"mycsv.csv")
 
Todas las referencias de archivos y directorios apuntan a la cuenta de almacenamiento wasb://container1@storage1.blob.core.windows.net, ya que es la **cuenta de almacenamiento predeterminada** asociada al clúster de HDInsight.

Ahora supongamos que quiere procesar un archivo denominado "mySpecial.csv" que se encuentra en el directorio "/private" del contenedor "container2" en el nombre de cuenta de almacenamiento "storage2".

En código R, cambie la referencia del nodo de nombre a la cuenta de almacenamiento "storage2".

    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

  Ubicación de los datos

    bigDataDirRoot <- "/private"

  Definición del contexto de proceso de Spark

    mySparkCluster <- RxSpark(consoleOutput=TRUE)

  Establecimiento del contexto de proceso

    rxSetComputeContext(mySparkCluster)

  Definición del sistema de archivos HDFS

    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

  Especificación del archivo de entrada en HDFS que se va a analizar

    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")
 
Todas las referencias de archivos y directorios apuntan ahora a la cuenta de almacenamiento wasb://container2@storage2.blob.core.windows.net, ya que es el **Nodo de nombre** que ha especificado.

Tenga en cuenta que tendrá que configurar el directorio /user/RevoShare/<SSH username> en la cuenta de almacenamiento "storage2":

    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>


## Uso de Archivos de Azure en el nodo perimetral 

Existe también una práctica opción de almacenamiento de datos para su uso en el nodo perimetral denominada [Archivos de Azure](../storage/storage-how-to-use-files-linux.md "Archivos de Azure") que permite montar un recurso compartido de archivos de Almacenamiento de Azure en el sistema de archivos de Linux. Esto puede resultar práctico para almacenar archivos de datos, scripts de R y objetos de resultado que podría necesitar más adelante cuando sea conveniente usar el sistema de archivos nativo en el nodo perimetral en lugar de HDFS. Una gran ventaja de usar Archivos de Azure es que cualquier sistema con un SO compatible (Windows, Linux) puede montar y usar los recursos compartidos de archivos, por ejemplo, otro clúster de HDInsight que tenga usted o alguien de su equipo, una máquina virtual de Azure o incluso un sistema local.


## Pasos siguientes

Ahora que sabe cómo crear un nuevo clúster de HDInsight que incluya un servidor de R y los aspectos básicos del uso de la consola de R desde una sesión de SSH, use las siguientes opciones para descubrir otras formas de trabajar con el servidor de R en HDInsight.

- [Información general sobre el servidor de R en Hadoop](hdinsight-hadoop-r-server-overview.md)
- [Introducción al servidor de R en Hadoop](hdinsight-hadoop-r-server-get-started.md)
- [Agregar un servidor de RStudio a HDInsight Premium](hdinsight-hadoop-r-server-install-r-studio.md)
- [Opciones de contexto de proceso del servidor de R en HDInsight Premium](hdinsight-hadoop-r-server-compute-contexts.md)

<!---HONumber=AcomDC_0420_2016-->