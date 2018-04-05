---
title: Soluciones de Azure Storage para R Server en HDInsight (Azure) | Microsoft Docs
description: Obtenga información sobre las distintas opciones de almacenamiento disponibles con R Server en HDInsight.
services: HDInsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 1cf30096-d3ca-45ea-b526-aa3954402f66
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/22/2018
ms.author: nitinme
ms.openlocfilehash: f0333252e404c52f123da916df01d198234ef5af
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="azure-storage-solutions-for-r-server-on-azure-hdinsight"></a>Soluciones de Azure Storage para R Server en Azure HDInsight

R Server en HDInsight dispone de diversas soluciones para almacenar datos, código u objetos que contienen resultados de análisis. Entre ellas se incluyen las siguientes opciones:

- [Azure Blob](https://azure.microsoft.com/services/storage/blobs/)
- [Almacén de Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/)
- [Azure File Storage](https://azure.microsoft.com/services/storage/files/)

También tiene la opción de acceder a varias cuentas o contenedores de Azure Storage con el clúster de HDInsight. Azure File Storage constituye una práctica opción de almacenamiento de datos para usar en el nodo perimetral que le permite montar un recurso compartido de archivos de Azure Storage en, por ejemplo, el sistema de archivos Linux. Pero los recursos compartidos de Azure File se pueden montar y utilizar en cualquier sistema que tenga un sistema operativo compatible, como Windows o Linux. 

Cuando se crea un clúster de Hadoop en HDInsight, se especifica una cuenta de **Azure Storage** o **Data Lake Store**. Un contenedor de almacenamiento específico de esa cuenta conserva el sistema de archivos para el clúster creado (por ejemplo, el Sistema de archivos distribuido de Hadoop). Para más información e instrucciones, consulte:

- [Uso de Azure Storage con HDInsight](../hdinsight-hadoop-use-blob-storage.md)
- [Uso de Data Lake Store con clústeres de Azure HDInsight](../hdinsight-hadoop-use-data-lake-store.md)

## <a name="use-azure-blob-storage-accounts-with-r-server-cluster"></a>Uso de cuentas de Azure Blob Storage con un clúster de R Server

Si especificó más de una cuenta de almacenamiento al crear el clúster de R Server, las instrucciones siguientes explican cómo usar una cuenta secundaria para las operaciones y el acceso a datos en el clúster de R Server. Supongamos las cuentas de almacenamiento y el contenedor siguientes: **storage1** y un contenedor predeterminado denominado **container1**, y  **storage2** con **container2**.

> [!WARNING]
> Con vistas al rendimiento, el clúster de HDInsight se crea en el mismo centro de datos que la cuenta de almacenamiento principal especificada. No se admite el uso de una cuenta de almacenamiento en una ubicación diferente a la del clúster de HDInsight.

### <a name="use-the-default-storage-with-r-server-on-hdinsight"></a>Uso del almacenamiento predeterminado con R Server en HDInsight

1. Use un cliente SSH para conectarse al nodo perimetral del clúster. Para obtener información sobre cómo utilizar SSH con clústeres de HDInsight, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
  
2. Copie un archivo de muestra, mysamplefile.csv, en el directorio /share. 

        hadoop fs –mkdir /share
        hadoop fs –copyFromLocal mycsv.scv /share  

3. Cambie a R Studio u otra consola de R y escriba código R para establecer el nodo de nombre en **default** y la ubicación del archivo al que quiere acceder.  

        myNameNode <- "default"
        myPort <- 0

        #Location of the data:  
        bigDataDirRoot <- "/share"  

        #Define Spark compute context:
        mySparkCluster <- RxSpark(nameNode=myNameNode, consoleOutput=TRUE)

        #Set compute context:
        rxSetComputeContext(mySparkCluster)

        #Define the Hadoop Distributed File System (HDFS) file system:
        hdfsFS <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

        #Specify the input file to analyze in HDFS:
        inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")

Todas las referencias de archivos y directorios apuntan a la cuenta de almacenamiento `wasb://container1@storage1.blob.core.windows.net`. Esta es la **cuenta de almacenamiento predeterminada** asociada al clúster de HDInsight.

### <a name="use-the-additional-storage-with-r-server-on-hdinsight"></a>Uso del almacenamiento adicional con R Server en HDInsight

Ahora supongamos que quiere procesar un archivo llamado mysamplefile1.csv que se encuentra en el directorio /private de **container2** en **storage2**.

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
    inputFile <-file.path(bigDataDirRoot,"mysamplefile1.csv")

Todas las referencias de archivos y directorios apuntan ahora a la cuenta de almacenamiento `wasb://container2@storage2.blob.core.windows.net`. Este es el **nodo de nombres** que ha especificado.

Debe configurar el directorio /user/RevoShare/<SSH username> en **storage2** de la forma siguiente:


    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare
    hadoop fs -mkdir wasb://container2@storage2.blob.core.windows.net/user/RevoShare/<RDP username>

## <a name="use-an-azure-data-lake-store-with-r-server-cluster"></a>Uso de una instancia de Azure Data Lake Store con el clúster de R Server 

Para utilizar Data Lake Store con el clúster de HDInsight, tiene que dar al clúster acceso a cada Azure Data Lake Store que quiera usar. Para obtener instrucciones sobre cómo usar Azure Portal para crear un clúster de HDInsight con una cuenta de Azure Data Lake Store como almacenamiento predeterminado o como almacén adicional, consulte [Creación de un clúster de HDInsight con Data Lake Store mediante Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

El uso del almacén en el script de R es muy similar al uso de una cuenta de almacenamiento secundaria de Azure (tal como se describe en el procedimiento anterior).

### <a name="add-cluster-access-to-your-azure-data-lake-stores"></a>Incorporación de acceso del clúster a los almacenes de Azure Data Lake
El acceso a un almacén de Azure Data Lake se establece mediante el uso de una entidad de servicio de Azure Active Directory (AAD) asociada a su clúster de HDInsight.

1. Al crear el clúster de HDInsight, seleccione **Identidad de AAD del clúster** en la pestaña **Origen de datos**.

2. En el cuadro de diálogo **Identidad de AAD del clúster**, en **Seleccionar entidad de servicio de AD**, elija **Crear nuevo**.

Después de asignar un nombre a la entidad de servicio y crear una contraseña para ella, haga clic en **Administrar acceso a ADLS** para asociar la entidad de servicio a Data Lake Store.

También es posible agregar acceso al clúster a una o varias cuentas de Data Lake Store después de la creación del clúster. Abra la entrada de Azure Portal para una instancia de Data Lake Store y vaya a **Explorador de datos > Acceso > Agregar**. 

### <a name="how-to-access-the-data-lake-store-from-r-server-on-hdinsight"></a>Acceso a la instancia de Data Lake Store desde R Server en HDInsight

Una vez que haya dado acceso a Data Lake Store, puede usar el almacén en el clúster de R Server en HDInsight de la manera en que lo haría con una cuenta de almacenamiento de Azure secundaria. La única diferencia es que el prefijo **wasb://** cambia a **adl://**, de la forma siguiente:


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
    inputFile <-file.path(bigDataDirRoot,"mysamplefile.csv")

Los siguientes comandos se usan para configurar la cuenta de Data Lake Store con el directorio RevoShare y agregar el archivo .csv de muestra del ejemplo anterior:


    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare
    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/user/RevoShare/<user>

    hadoop fs -mkdir adl://rkadl1.azuredatalakestore.net/share

    hadoop fs -copyFromLocal /usr/lib64/R Server-7.4.1/library/RevoScaleR/SampleData/mysamplefile.csv adl://rkadl1.azuredatalakestore.net/share

    hadoop fs –ls adl://rkadl1.azuredatalakestore.net/share


## <a name="use-azure-file-storage-with-r-server-on-hdinsight"></a>Uso de Azure File Storage con R Server en HDInsight

También hay una opción de almacenamiento de datos adecuada para su uso en el nodo perimetral llamada [Azure Files] (https://azure.microsoft.com/services/storage/files/). Con esta opción podrá montar un recurso compartido de archivos de Azure Storage en el sistema de archivos de Linux. Esta opción puede resultar práctica para almacenar archivos de datos, scripts de R y objetos de resultado que podría necesitar más adelante, en especial cuando sea conveniente usar el sistema de archivos nativo en el nodo perimetral en lugar de HDFS. 

Una ventaja importante de Archivos de Azure es que los recursos compartidos de archivos se pueden montar y utilizar en cualquier sistema que tenga un sistema operativo compatible, como Windows o Linux. Por ejemplo, puede utilizarse con otro clúster de HDInsight que sea suyo o de alguien de su equipo, con una máquina virtual de Azure, o incluso con un sistema local. Para más información, consulte:

- [Uso de Azure File Storage con Linux](../../storage/files/storage-how-to-use-files-linux.md)
- [Uso de Azure File Storage en Windows](../../storage/files/storage-dotnet-how-to-use-files.md)


## <a name="next-steps"></a>Pasos siguientes

* [Introducción a las funcionalidades de R de código abierto de R Server en HDInsight](r-server-overview.md)
* [Introducción al uso de R Server en HDInsight](r-server-get-started.md)
* [Opciones de contexto de proceso para R Server en HDInsight (versión preliminar)](r-server-compute-contexts.md)

