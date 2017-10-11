---
title: Soluciones de Azure Storage para R Server en HDInsight (Azure) | Microsoft Docs
description: "Obtenga información sobre las distintas opciones de almacenamiento disponibles para los usuarios con R Server en HDInsight."
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: aef9c15636ccaecce07d4fa218a40ed26ebad9df
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="azure-storage-solutions-for-r-server-on-hdinsight"></a>Soluciones de Azure Storage para R Server en HDInsight

Microsoft R Server en HDInsight dispone de diversas soluciones para almacenar datos, código u objetos que contienen resultados de análisis. Entre ellas se incluyen las siguientes opciones:

- [Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Almacén de Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/)
- [Azure File Storage](https://azure.microsoft.com/services/storage/files/)

También tiene la opción de acceder a varias cuentas o contenedores de Azure Storage con el clúster de HDI. Azure File Storage constituye una práctica opción de almacenamiento de datos para usar en el nodo perimetral que le permite montar un recurso compartido de archivos de Azure Storage en, por ejemplo, el sistema de archivos Linux. Pero los recursos compartidos de Azure File se pueden montar y utilizar en cualquier sistema que tenga un sistema operativo compatible, como Windows o Linux. 

Cuando se crea un clúster de Hadoop en HDInsight, se especifica una cuenta de **Azure Storage** o una de **Data Lake Store**. Un contenedor de almacenamiento específico de esa cuenta conserva el sistema de archivos para el clúster creado (por ejemplo, el Sistema de archivos distribuido de Hadoop). Para más información e instrucciones, consulte:

- [Uso de Azure Storage con HDInsight](hdinsight-hadoop-use-blob-storage.md)
- [Uso de Data Lake Store con clústeres de Azure HDInsight](hdinsight-hadoop-use-data-lake-store.md) 

Para más información sobre las soluciones de almacenamiento de Azure, consulte [Introducción a Microsoft Azure Storage](../storage/common/storage-introduction.md). 

Para obtener instrucciones sobre cómo seleccionar la opción de almacenamiento más adecuada para su escenario, consulte [Decisión sobre cuándo usar Azure Blobs, Azure Files o Azure Data Disks](../storage/common/storage-decide-blobs-files-disks.md). 


## <a name="use-azure-blob-storage-accounts-with-r-server"></a>Uso de cuentas de Azure Blob Storage con R Server

Si es necesario, se puede acceder a varias cuentas de almacenamiento o contenedores de Azure con el clúster de HDI. Para ello, tiene que especificar las cuentas de almacenamiento adicionales en la interfaz de usuario durante la creación del clúster y seguir estos pasos para poder usarlas en R Server.

> [!WARNING]
> Con vistas al rendimiento, el clúster de HDInsight se crea en el mismo centro de datos que la cuenta de almacenamiento principal especificada. No se admite el uso de una cuenta de almacenamiento en una ubicación diferente a la del clúster de HDInsight.

1. Cree un clúster de HDInsight con el nombre de cuenta de almacenamiento **storage1** y un contenedor predeterminado denominado **container1**.
2. Especifique una cuenta de almacenamiento adicional con el nombre **storage2**.  
3. Copie el archivo mycsv.csv en el directorio /share y realice un análisis en ese archivo.  

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal myscsv.scv /share  

4. En código R, establezca el nodo de nombre en **default** e indique el directorio y el archivo que se van a procesar.  

        myNameNode <- "default"
        myPort <- 0

        #Location of the data:  
        bigDataDirRoot <- "/share"  

        #Define Spark compute context:
        mySparkCluster <- RxSpark(consoleOutput=TRUE)

        #Set compute context:
        rxSetComputeContext(mySparkCluster)

        #Define the Hadoop Distributed File System (HDFS) file system:
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

        #Specify the input file to analyze in HDFS:
        inputFile <-file.path(bigDataDirRoot,"mycsv.csv")

Todas las referencias de archivos y directorios apuntan a la cuenta de almacenamiento wasb://container1@storage1.blob.core.windows.net. Esta es la **cuenta de almacenamiento predeterminada** asociada al clúster de HDInsight.

Ahora supongamos que quiere procesar un archivo llamado mySpecial.csv que se encuentra en el directorio /private de **container2** en **storage2**.

En el código R, apunte la referencia del nodo de nombres a la cuenta de almacenamiento **storage2** .


    myNameNode <- "wasb://container2@storage2.blob.core.windows.net"
    myPort <- 0

    #Location of the data:
    bigDataDirRoot <- "/private"

    #Define Spark compute context:
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    #Set compute context:
    rxSetComputeContext(mySparkCluster)

    #Define HDFS file system:
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    #Specify the input file to analyze in HDFS:
    inputFile <-file.path(bigDataDirRoot,"mySpecial.csv")

Todas las referencias de archivos y directorios apuntan ahora a la cuenta de almacenamiento wasb://container2@storage2.blob.core.windows.net. Este es el **nodo de nombres** que ha especificado.

Debe configurar el directorio /user/RevoShare/<SSH username> en **storage2** de la forma siguiente:


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>



## <a name="use-an-azure-data-lake-store-with-r-server"></a>Uso de una instancia de Azure Data Lake Store con R Server

Para utilizar los almacenes de Data Lake con su cuenta de HDInsight, tiene que dar al clúster acceso a cada almacén de Azure Data Lake que quiera usar. Para obtener instrucciones sobre cómo usar Azure Portal para crear un clúster de HDInsight con una cuenta de Azure Data Lake Store como almacenamiento predeterminado o como almacén adicional, consulte [Creación de un clúster de HDInsight con Data Lake Store mediante Azure Portal](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

El uso del almacén en el script de R es muy similar al uso de una cuenta de almacenamiento secundaria de Azure (tal como se describe en el procedimiento anterior).

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Incorporación de acceso del clúster a los almacenes de Azure Data Lake
El acceso a un almacén de Azure Data Lake se establece mediante el uso de una entidad de servicio de Azure Active Directory (AAD) asociada a su clúster de HDInsight.

Para agregar una entidad de servicio de Azure AD:

1. Al crear el clúster de HDInsight, seleccione **Identidad de AAD del clúster** en la pestaña **Origen de datos**.

2. En el cuadro de diálogo **Identidad de AAD del clúster**, en **Seleccionar entidad de servicio de AD**, elija **Crear nuevo**.

Después de asignar un nombre a la entidad de servicio y crear una contraseña para ella, haga clic en **Administrar acceso a ADLS** para asociar la entidad de servicio a los almacenes Data Lake Store.

También es posible agregar acceso al clúster a una o varias instancias de Data Lake Store después de la creación del clúster. Abra la entrada de Azure Portal para una instancia de Data Lake Store y vaya a **Explorador de datos > Acceso > Agregar**. 

### <a name="how-to-access-the-data-lake-store-from-r-server"></a>Acceso a la instancia de Data Lake Store desde R Server

Una vez que haya dado acceso a un almacén de Data Lake, puede usar el almacén en R Server en HDInsight de la manera en que lo haría con una cuenta de almacenamiento de Azure secundaria. La única diferencia es que el prefijo **wasb://** cambia a **adl://**, de la forma siguiente:


    # Point to the ADL store (e.g. ADLtest)
    myNameNode <- "adl://rkadl1.azuredatalakestore.net"
    myPort <- 0

    # Location of the data (assumes a /share directory on the ADL account)
    bigDataDirRoot <- "/share"  

    # Define Spark compute context
    mySparkCluster <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort)

    # Set compute context
    rxSetComputeContext(mySparkCluster)

    # Define HDFS file system
    hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

    # Specify the input file in HDFS to analyze
    inputFile <-file.path(bigDataDirRoot,"AirlineDemoSmall.csv")

    # Create factors for days of the week
    colInfo <- list(DayOfWeek = list(type = "factor",
               levels = c("Monday", "Tuesday", "Wednesday", "Thursday",
                          "Friday", "Saturday", "Sunday")))

    # Define the data source
    airDS <- RxTextData(file = inputFile, missingValueString = "M",
                    colInfo  = colInfo, fileSystem = hdfsFS)

    # Run a linear regression
    model <- rxLinMod(ArrDelay~CRSDepTime+DayOfWeek, data = airDS)


Los siguientes comandos se usan para configurar la cuenta de almacenamiento de Data Lake con el directorio RevoShare y agregar el archivo .csv de ejemplo del ejemplo anterior:


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/AirlineDemoSmall.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-r-server"></a>Uso de Azure File Storage con R Server

También hay una práctica opción de almacenamiento de datos para usar en el nodo perimetral llamada [Azure Files] ((https://azure.microsoft.com/services/storage/files/). Con esta opción podrá montar un recurso compartido de archivos de Almacenamiento de Azure en el sistema de archivos de Linux. Esta opción puede resultar práctica para almacenar archivos de datos, scripts de R y objetos de resultado que podría necesitar más adelante, en especial cuando sea conveniente usar el sistema de archivos nativo en el nodo perimetral en lugar de HDFS. 

Una ventaja importante de Archivos de Azure es que los recursos compartidos de archivos se pueden montar y utilizar en cualquier sistema que tenga un sistema operativo compatible, como Windows o Linux. Por ejemplo, puede utilizarse con otro clúster de HDInsight que sea suyo o de alguien de su equipo, con una máquina virtual de Azure, o incluso con un sistema local. Para más información, consulte:

- [Uso de Azure File Storage con Linux](../storage/files/storage-how-to-use-files-linux.md)
- [Uso de Azure File Storage en Windows](../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Pasos siguientes

Ahora que comprende las opciones de Azure Storage, use los vínculos siguientes para descubrir otras maneras de realizar tareas de ciencia de datos con R Server en HDInsight.

* [Información general de R Server en HDInsight (versión preliminar)](hdinsight-hadoop-r-server-overview.md)
* [Introducción al servidor de R en Hadoop](hdinsight-hadoop-r-server-get-started.md)
* [Instalación de RStudio con R Server en HDInsight (si no está instalado durante la creación del clúster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Opciones de contexto de proceso para R Server en HDInsight (versión preliminar)](hdinsight-hadoop-r-server-compute-contexts.md)

