---
title: "Creación de Azure HDInsight (Hadoop) basado en Windows | Microsoft Docs"
description: "Aprenda a crear clústeres basados en Windows en Azure HDInsight mediante plantillas de Azure Resource Manager."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: a4975787-699a-4cdc-b764-d8c9301e13ef
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/14/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: bb700c7de96712666bc4be1f8e430a2e94761f69
ms.openlocfilehash: ad511174f8df2fca752447ed942880d02c141308
ms.lasthandoff: 01/24/2017


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-resource-manager-templates"></a>Creación de clústeres de Hadoop basados en Windows en HDInsight mediante plantillas de Azure Resource Manager

[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Aprenda a crear clústeres de HDInsight basados en Windows con plantillas de Azure Resource Manager. Para obtener más información, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](../azure-resource-manager/resource-group-template-deploy.md). 

La información de este artículo solo se aplica a los clústeres de HDInsight para Windows. Para más información, consulte [Creación de clústeres de Hadoop en HDInsight con plantillas de Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).


## <a name="prerequisites"></a>Requisitos previos:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de empezar las instrucciones de este artículo, debe tener lo siguiente:

* [Suscripción de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell o CLI de Azure 

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

### <a name="access-control-requirements"></a>Requisitos de control de acceso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="resource-manager-templates"></a>Plantillas de Administrador de recursos
La plantilla de Resource Manager facilita la creación de clústeres de HDInsight, sus recursos dependientes (por ejemplo, la cuenta de almacenamiento predeterminada) y otros recursos (como Azure SQL Database para utilizar Apache Sqoop) para la aplicación en una operación única y coordinada. En la plantilla, se definen los recursos necesarios para la aplicación y se especifican los parámetros de implementación para especificar valores para diferentes entornos. La plantilla consta de JSON y expresiones que puede usar para generar valores para su implementación.

En el [Apéndice A](#appx-a-arm-template)puede encontrar una plantilla de Resource Manager para crear un clúster de HDInsight y la cuenta de Azure Storage dependiente. Utilice un editor de texto para guardar la plantilla en un archivo en la estación de trabajo. Obtendrá información sobre cómo llamar a la plantilla mediante varias herramientas.

Para más información sobre la plantilla de Resource Manager, consulte

* [Creación de plantillas del Administrador de recursos de Azure](../azure-resource-manager/resource-group-authoring-templates.md)
* [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](../azure-resource-manager/resource-group-template-deploy.md)

## <a name="deploy-with-powershell"></a>Implementación con PowerShell
El procedimiento siguiente crea un clúster de HDInsight.

**Implementación de un clúster mediante una plantilla de Resource Manager**

1. Guarde el archivo json en el [Apéndice A](#appx-a-arm-template) de la estación de trabajo.
2. Configure los parámetros si es necesario.
3. Ejecute la plantilla con siguiente script de PowerShell:

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>"
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and varialbes
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$hdinsightClusterName";clusterStorageAccountName="$defaultStorageAccountName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

    El script de PowerShell solo configura el nombre del clúster y el nombre de la cuenta de almacenamiento.  Puede establecer otros valores en la plantilla de Resource Manager.

Para obtener más información, consulte [Implementación con PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy).

## <a name="deploy-with-azure-cli"></a>Implementación con la CLI de Azure
En el ejemplo siguiente se crea un clúster y su contenedor y cuenta de almacenamiento dependientes mediante una llamada a una plantilla de Resource Manager:

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US 2"
    azure group deployment create "hdi1229rg" "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json" -p "{\"clusterName\":{\"value\":\"hdi1229win\"},\"clusterStorageAccountName\":{\"value\":\"hdi1229store\"},\"location\":{\"value\":\"East US 2\"},\"clusterLoginPassword\":{\"value\":\"Pass@word1\"}}"





## <a name="deploy-with-rest-api"></a>Implementación con la API de REST
Consulte [Implementación con la API de REST](../azure-resource-manager/resource-group-template-deploy-rest.md).

## <a name="deploy-with-visual-studio"></a>Implementación con Visual Studio
Con Visual Studio, puede crear un proyecto del grupo de recursos e implementarlo en Azure a través de la interfaz de usuario. Seleccione el tipo de recursos que incluirá en su proyecto y esos recursos se agregarán automáticamente a la plantilla del Administrador de recursos. El proyecto también ofrece un script de PowerShell para implementar la plantilla.

Para ver una introducción sobre el uso de Visual Studio con grupos de recursos, consulte [Creación e implementación de grupos de recursos de Azure mediante Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido varias maneras de crear un clúster de HDInsight. Para obtener más información, consulte los artículos siguientes:

* Para ver un ejemplo de cómo implementar los recursos mediante la biblioteca cliente de .NET, consulte [Deploy Azure resources using .NET libraries and a template](../virtual-machines/virtual-machines-windows-csharp-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)(Implementación de recursos de Azure mediante bibliotecas de .NET y una plantilla).
* Para ver un ejemplo en profundidad de la implementación de una aplicación, consulte [Aprovisionamiento e implementación predecibles de microservicios en Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
* Para obtener instrucciones sobre cómo implementar la solución en diferentes entornos, vea [Entornos de desarrollo y pruebas en Microsoft Azure](../solution-dev-test-environments.md).
* Para información sobre las secciones de la plantilla de Azure Resource Manager, consulte [Creación de plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Para ver una lista de las funciones que puede usar en una plantilla de Azure Resource Manager, consulte [Funciones de la plantilla de Azure Resource Manager](../azure-resource-manager/resource-group-template-functions.md).

## <a name="appx-a-resource-manager-template"></a>Anexo A: Plantilla de Resource Manager
La siguiente plantilla de Azure Resource Manager crea un clúster de Hadoop basado en Windows con la cuenta de almacenamiento de Azure dependiente.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
        "type": "string",
        "defaultValue": "East US 2",
        "allowedValues": [
            "Central US",
            "North Europe",
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Brizil South",
            "Australia East",
            "Australia Southeast",
            "Central India"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password for the cluster login."
        }
        },
        "clusterStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "The name of the storage account to be created and be used as the cluster's storage."
        }
        },
        "clusterStorageType": {
        "type": "string",
        "defaultValue": "Standard_LRS",
        "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS"
        ]
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 4,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
        "variables": {},
        "resources": [
            {
            "name": "[parameters('clusterStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2015-05-01-preview",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "accountType": "[parameters('clusterStorageType')]"
            }
            },
            {
            "name": "[parameters('clusterName')]",
            "type": "Microsoft.HDInsight/clusters",
            "location": "[parameters('location')]",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
            ],
            "tags": {},
            "properties": {
                "clusterVersion": "3.2",
                "osType": "Windows",
                "clusterDefinition": {
                "kind": "hadoop",
                "configurations": {
                    "gateway": {
                    "restAuthCredential.isEnabled": true,
                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                    }
                }
                },
                "storageProfile": {
                "storageaccounts": [
                    {
                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                    "isDefault": true,
                    "container": "[parameters('clusterName')]",
                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                    }
                ]
                },
                "computeProfile": {
                "roles": [
                    {
                    "name": "headnode",
                    "targetInstanceCount": "1",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    },
                    {
                    "name": "workernode",
                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    }
                ]
                }
            }
            }
        ],
        "outputs": {
            "cluster": {
            "type": "object",
            "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
            }
        }
    }

