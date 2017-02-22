---
title: "Creación de clústeres de Azure HDInsight (Hadoop) basados en Windows mediante PowerShell | Microsoft Docs"
description: "Aprenda a crear clústeres de Hadoop basados en Windows en HDInsight con Azure PowerShell."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: a365e67d-55bc-476e-a126-68f0962ec5aa
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/17/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: 9870b61eb00cee169973ae7c09b0c4886cd2db53


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-powershell"></a>Creación de clústeres de Hadoop basados en Windows en HDInsight con Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Aprenda a crear clústeres de Hadoop basados en Windows en HDInsight con Azure PowerShell. 

La información de este artículo solo se aplica a los clústeres de HDInsight para Windows. Para más información sobre la creación de clústeres basados en Linux, consulte [Creación de clústeres de Hadoop en HDInsight con Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Requisitos previos:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de empezar las instrucciones de este artículo, debe tener lo siguiente:

* Suscripción de Azure. Consulte [How to get Azure Free trial for testing Hadoop in HDInsight (Obtención de una versión de prueba gratuita de Azure para probar Hadoop en HDInsight)](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell.

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>Requisitos de control de acceso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Creación de clústeres
Azure PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Esta sección proporciona instrucciones sobre cómo crear un clúster de HDInsight con Azure PowerShell. Para obtener información sobre la configuración de una estación de trabajo para que ejecute cmdlets de HDInsight Windows PowerShell, consulte [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs). Para obtener más información sobre el uso de Azure PowerShell con HDInsight, consulte [Administración de HDInsight con PowerShell](hdinsight-administer-use-powershell.md). Para obtener una lista de los cmdlets de HDInsight Windows PowerShell, consulte [Documentación de referencia de los cmdlets de HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).

Los siguientes procedimientos son necesarios para crear un clúster de HDInsight con Azure PowerShell:

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<Enter an Alias>"
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"
    #endregion

    ###########################################
    # Service names and varialbes
    ###########################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    $clusterSizeInNodes = 1
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ###########################################
    # Connect to Azure
    ###########################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ###########################################
    # Create the resource group
    ###########################################
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    ###########################################
    # Preapre default storage account and container
    ###########################################
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Type Standard_GRS `
        -Location $location

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $hdinsightClusterName -Context $defaultStorageContext

    ###########################################
    # Create the cluster
    ###########################################

    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $hdinsightClusterName

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

## <a name="create-clusters-using-resource-manager-template"></a>Creación de clústeres mediante una plantilla de Resource Manager
Puede usar Azure PowerShell para implementar una plantilla de Azure Resource Manager que cree un clúster de HDInsight.  Consulte [Llamada a plantillas mediante Azure PowerShell](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-powershell).

## <a name="customize-clusters"></a>Personalización de los clústeres
* Consulte [Personalización de los clústeres de HDInsight con Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts (Windows)](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido varias maneras de crear un clúster de HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md) : aprenda a empezar a trabajar con su clúster de HDInsight
* [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md) : aprenda a enviar trabajos a HDInsight mediante programación
* [Administración de clústeres de Hadoop en HDInsight con PowerShell de Azure](hdinsight-administer-use-powershell.md) : aprenda a trabajar con HDInsight mediante Azure PowerShell
* [Documentación del SDK de HDInsight de Azure][hdinsight-sdk-documentation]: descubra el SDK de HDInsight

[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx



<!--HONumber=Jan17_HO4-->


