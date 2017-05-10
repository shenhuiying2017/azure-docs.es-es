---
title: "Creación de Azure HDInsight (Hadoop) con PowerShell | Microsoft Docs"
description: "Aprenda a crear clústeres de Hadoop, HBase, Storm o Spark en Linux para HDInsight con Azure PowerShell."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4208deca-d64a-45e1-8948-2673d5d7678c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: f22fc9e9306d34b66e89966a0f4485461539899d
ms.contentlocale: es-es
ms.lasthandoff: 05/03/2017


---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Crear clústeres basados en Linux en HDInsight con Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell es un eficaz entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Microsoft Azure. En este documento se ofrece información sobre cómo crear un clúster de HDInsight basado en Linux mediante Azure PowerShell. También se incluye un script de ejemplo.

> [!NOTE]
> Azure PowerShell solo está disponible en clientes de Windows. Si está usando un cliente de Linux, Unix o Mac OS X, consulte [Crear clústeres basados en Linux en HDInsight con la CLI de Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) para más información sobre el uso de la CLI de Azure para crear un clúster.

## <a name="prerequisites"></a>Requisitos previos
Antes de iniciar este procedimiento, debe tener lo siguiente:

* Una suscripción de Azure. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-azurerm-ps)

    > [!IMPORTANT]
    > La compatibilidad con Azure PowerShell para administrar recursos de HDInsight mediante Azure Service Manager está **en desuso** y dejó de estar disponible por completo el 1 de enero de 2017. En los pasos descritos en este documento, se usan los nuevos cmdlets de HDInsight que funcionan con Azure Resource Manager.
    >
    > Para instalar la última versión de Azure PowerShell, siga los pasos descritos en [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Si tiene scripts que se deben modificar para usar los nuevos cmdlets que funcionan con Azure Resource Manager, consulte [Migrating to Azure Resource Manager-based development tools for HDInsight clusters](hdinsight-hadoop-development-using-azure-resource-manager.md) (Migración a herramientas de desarrollo basadas en Azure Resource Manager para clústeres de HDInsight) para más información.

## <a name="create-cluster"></a>Crear clúster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para crear un clúster de HDInsight con Azure PowerShell, debe realizar los procedimientos siguientes:

* Creación de un grupo de recursos de Azure
* Creación de una cuenta de almacenamiento de Azure
* Creación de un contenedor de blobs de Azure
* Creación de un clúster de HDInsight

Los dos parámetros más importantes que debe establecer para crear clústeres de Linux son los que especifica el tipo de sistema operativo y los detalles del usuario de SSH:

* Asegúrese de especificar el parámetro **-OSType** como **Linux**.
* Para usar SSH para las sesiones remotas en los clústeres, puede especificar la contraseña del usuario de SSH o la clave pública SSH. Si se especifica tanto la contraseña del usuario de SSH como la clave pública SSH, se omitirá la clave. Si desea usar la clave SSH en las sesiones remotas, debe especificar una contraseña SSH en blanco cuando se le solicite. Para obtener más información, consulte [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

El script siguiente muestra cómo crear un nuevo clúster:

    $token ="<SpecifyAnUniqueString>"
    $subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name

    $resourceGroupName = $token + "rg"      # Provide a Resource Group name
    $clusterName = $token
    $defaultStorageAccountName = $token + "store"   # Provide a Storage account name
    $defaultStorageContainerName = $token + "container"
    $location = "East US 2"     # Change the location if needed
    $clusterNodes = 1           # The number of nodes in the HDInsight cluster

    # Sign in to Azure
    Login-AzureRmAccount

    # Select the subscription to use if you have multiple subscriptions
    Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account and container used as the default storage
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $defaultStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultStorageContainerName -Context $destContext

    # Create an HDInsight cluster
    $credentials = Get-Credential -Message "Enter Cluster user credentials" -UserName "admin"
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"

    # The location of the HDInsight cluster must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $defaultStorageAccountName | %{$_.Location}

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials

Los valores que se especifican para **$clusterCredentials** se usan para crear la cuenta de usuario de Hadoop para el clúster. Use esta cuenta para conectar con el clúster.

Los valores que se especifican para **$sshCredentials** se usan para crear el usuario de SSH para el clúster. Use esta cuenta para iniciar una sesión remota de SSH en el clúster y ejecutar trabajos.

> [!IMPORTANT]
> En este script, debe especificar el número de nodos de trabajo que se incluirán en el clúster. Si tiene pensado usar más de 32 nodos de trabajo (en el momento de crear el clúster o cambiando el tamaño del clúster después de su creación), también debe especificar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
>
> Para obtener más información acerca de los tamaños de nodo y los costos asociados, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Un clúster puede tardar hasta 20 minutos en crearse.

## <a name="create-cluster-configuration-object"></a>Creación de un clúster: objeto de configuración

También puede crear un objeto de configuración de HDInsight mediante el cmdlet `New-AzureRmHDInsightClusterConfig`. A continuación, puede modificar este objeto de configuración con el fin de habilitar otras opciones de configuración para el clúster. Por último, utilice el parámetro `-Config` del cmdlet `New-AzureRmHDInsightCluster` para emplear la configuración.

El script siguiente crea un objeto de configuración para configurar un R Server en el tipo de clúster de HDInsight. La configuración habilita un nodo perimetral, RStudio y una cuenta de almacenamiento adicional.

    # Create another storage account used as additional storage account
    $additionalStorageAccountName = $token + "store2"
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName `
        -StorageAccountName $additionalStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $additionalStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $additionalStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value

    # Create a new configuration for RServer cluster type
    # Use -EdgeNodeSize to set the size of the edge node for RServer clusters
    # if you want a specific size. Otherwise, the default size is used.
    $config = New-AzureRmHDInsightClusterConfig `
        -ClusterType "RServer" `
        -EdgeNodeSize "Standard_D12_v2"

    # Add RStudio to the configuration
    $rserverConfig = @{"RStudio"="true"}
    $config = $config | Add-AzureRmHDInsightConfigValues `
        -RServer $rserverConfig

    # Add an additional storage account
    Add-AzureRmHDInsightStorage -Config $config -StorageAccountName "$additionalStorageAccountName.blob.core.windows.net" -StorageAccountKey $additionalStorageAccountKey

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -OSType Linux `
        -Version "3.5" `
        -SshCredential $sshCredentials `
        -Config $config

> [!WARNING]
> No se admite el uso de una cuenta de almacenamiento en una ubicación diferente a la del clúster de HDInsight. Cuando utilice este ejemplo, cree la cuenta de almacenamiento adicional en la misma ubicación que el servidor.

## <a name="customize-clusters"></a>Personalización de los clústeres

* Consulte [Personalización de los clústeres de HDInsight con Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Solución de problemas

Si experimenta problemas con la creación de clústeres de HDInsight, consulte los [requisitos de control de acceso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya ha creado con éxito un clúster de HDInsight, use los siguientes recursos para aprender a trabajar con él.

### <a name="hadoop-clusters"></a>Clústeres Hadoop

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clústeres HBase

* [Introducción a HBase en HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desarrollo de aplicaciones de Java para HBase en HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clústeres Storm

* [Desarrollo de topologías de Java para Storm en HDInsight](hdinsight-storm-develop-java-topology.md)
* [Uso de componentes de Python en Storm en HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implementación y supervisión de topologías con Storm en HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Clústeres de Spark

* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)


