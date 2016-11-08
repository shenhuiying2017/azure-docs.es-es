---
title: Creación de clústeres de Hadoop basados en Linux en HDInsight con plantillas de Azure Resource Manager | Microsoft Docs
description: Obtenga información acerca de cómo crear clústeres para Azure HDInsight con plantillas de Azure Resource Manager.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: jgao

---
# Creación de clústeres de Hadoop basados en Linux en HDInsight con plantillas de Azure Resource Manager
[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Aprenda a crear clústeres de HDInsight con plantillas del Administrador de recursos de Azure. Para obtener más información, consulte [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](../resource-group-template-deploy.md). Para consultar otras herramientas y características de creación de clústeres, haga clic en la selección de pestaña de la parte superior de esta página o consulte los [métodos de creación de clústeres](hdinsight-provision-clusters.md#cluster-creation-methods).

## Requisitos previos:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de empezar las instrucciones de este artículo, debe tener lo siguiente:

* [Suscripción de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell o CLI de Azure
  
    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

## Plantillas de Administrador de recursos
La plantilla de Resource Manager facilita la creación de clústeres de HDInsight, sus recursos dependientes (por ejemplo, la cuenta de almacenamiento predeterminada) y otros recursos (como Azure SQL Database para utilizar Apache Sqoop) para la aplicación en una operación única y coordinada. En la plantilla, se definen los recursos necesarios para la aplicación y se especifican los parámetros de implementación para especificar valores para diferentes entornos. La plantilla consta de JSON y expresiones que puede usar para generar valores para su implementación.

En el [Apéndice A](#appx-a-arm-template) puede encontrar una plantilla de Resource Manager para crear un clúster de HDInsight y la cuenta de Azure Storage dependiente. Utilice [VSCode](https://code.visualstudio.com/#alt-downloads) multiplataforma con la [extensión de Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) o un editor de texto para guardar la plantilla en un archivo en su estación de trabajo. En este documento, obtendrá información sobre cómo llamar a la plantilla mediante distintos métodos.

Para más información sobre la plantilla de Resource Manager, consulte

* [Creación de plantillas del Administrador de recursos de Azure](../resource-group-authoring-templates.md)
* [Implementación de una aplicación con la plantilla del Administrador de recursos de Azure](../resource-group-template-deploy.md)

Para averiguar el esquema JSON para ciertos elementos, puede seguir el procedimiento siguiente:

1. Abra [Azure Portal](https://porta.azure.com) para crear un clúster de HDInsight. Consulte [Crear clústeres basados en Linux en HDInsight con Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).
2. Configure los elementos requeridos y los elementos que necesite en el esquema JSON.
3. Antes de hacer clic en **Crear**, haga clic en **Opciones de automatización** como se muestra en la siguiente captura de pantalla:
   
    ![Hadoop en HDInsight crear clúster plantilla de Resource Manager opciones de automatización de esquemas](./media/hdinsight-hadoop-create-linux-clusters-arm-templates/hdinsight-create-cluster-resource-manager-template-automation-option.png)
   
    El portal creará una plantilla de Resource Manager según la configuración que haya establecido.
   
   ## Implementación con PowerShell

El procedimiento siguiente crea el clúster de HDInsight basado en Linux.

**Implementación de un clúster mediante una plantilla de Resource Manager**

1. Guarde el archivo json en el [Apéndice A](#appx-a-arm-template) de la estación de trabajo. En el script de PowerShell, el nombre del archivo es *C:\\HDITutorials-ARM\\hdinsight-arm-template.json*.
2. Si fuera necesario, establezca los parámetros y las variables.
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
        $parameters = @{clusterName="$hdinsightClusterName"}
   
        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters
   
        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 
   
    El script de PowerShell solo configura el nombre del clúster. El nombre de la cuenta de almacenamiento está codificado en la plantilla. Se le pedirá que escriba la contraseña de usuario del clúster (el nombre de usuario predeterminado es *admin*) y la contraseña de usuario SSH (el nombre de usuario SSH predeterminado es *sshuser*).

Para más información, consulte [Implementación con PowerShell](../resource-group-template-deploy.md#deploy-with-powershell).

## Implementación con la CLI de Azure
En el ejemplo siguiente se crea un clúster y su contenedor y cuenta de almacenamiento dependientes mediante una llamada a una plantilla de Resource Manager:

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US"
    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json"

Se le pedirá que escriba el nombre del clúster, la contraseña de usuario del clúster (el nombre de usuario predeterminado es *admin*) y la contraseña de usuario SSH (el nombre de usuario SSH predeterminado es *sshuser*). Introduzca el siguiente comando para proporcionar parámetros en línea:

    azure group deployment create --resource-group "hdi1229rg" --name "hdi1229" --template-file "c:\Tutorials\HDInsightARM\create-linux-based-hadoop-cluster-in-hdinsight.json" --parameters '{"clusterName":{"value":"hdi1229"},"clusterLoginPassword":{"value":"Pass@word1"},"sshPassword":{"value":"Pass@word1"}}'

## Implementación con la API de REST
Consulte [Implementación con la API de REST](../resource-group-template-deploy.md#deploy-with-the-rest-api).

## Implementación con Visual Studio
Con Visual Studio, puede crear un proyecto del grupo de recursos e implementarlo en Azure a través de la interfaz de usuario. Seleccione el tipo de recursos que incluirá en su proyecto y esos recursos se agregarán automáticamente a la plantilla del Administrador de recursos. El proyecto también ofrece un script de PowerShell para implementar la plantilla.

Para ver una introducción sobre el uso de Visual Studio con grupos de recursos, consulte [Creación e implementación de grupos de recursos de Azure mediante Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Pasos siguientes
En este artículo, ha aprendido varias maneras de crear un clúster de HDInsight. Para obtener más información, consulte los artículos siguientes:

* Para ver un ejemplo de cómo implementar los recursos mediante la biblioteca cliente de .NET, consulte [Deploy Azure resources using .NET libraries and a template](../virtual-machines/virtual-machines-windows-csharp-template.md) (Implementación de recursos de Azure mediante bibliotecas de .NET y una plantilla).
* Para ver un ejemplo en profundidad de la implementación de una aplicación, consulte [Aprovisionamiento e implementación predecibles de microservicios en Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
* Para obtener instrucciones sobre cómo implementar la solución en diferentes entornos, vea [Entornos de desarrollo y pruebas en Microsoft Azure](../solution-dev-test-environments.md).
* Para información sobre las secciones de la plantilla de Azure Resource Manager, consulte [Creación de plantillas de Azure Resource Manager](../resource-group-authoring-templates.md).
* Para ver una lista de las funciones que puede usar en una plantilla de Azure Resource Manager, consulte [Funciones de la plantilla de Azure Resource Manager](../resource-group-template-functions.md).

## Anexo A: Plantilla de Resource Manager
La siguiente plantilla de Administrador de recursos de Azure crea un clúster de Hadoop basado en Linux con la cuenta de Almacenamiento de Azure dependiente.

> [!NOTE]
> El ejemplo incluye información de configuración de la tienda de Hive y Oozie Metastore. Quite la sección o configúrela antes de usar la plantilla.
> 
> 

    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
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
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "sshUserName": {
        "type": "string",
        "defaultValue": "sshuser",
        "metadata": {
            "description": "These credentials can be used to remotely access the cluster."
        }
        },
        "sshPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password must be at least 10 characters in length and must contain at least one digit, one non-alphanumeric character, and one upper or lower case letter."
        }
        },
        "location": {
        "type": "string",
        "defaultValue": "East US",
        "allowedValues": [
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
            "Australia East",
            "Australia Southeast"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterType": {
        "type": "string",
        "defaultValue": "hadoop",
        "allowedValues": [
            "hadoop",
            "hbase",
            "storm",
            "spark"
        ],
        "metadata": {
            "description": "The type of the HDInsight cluster to create."
        }
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 2,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
    "variables": {
        "defaultApiVersion": "2015-05-01-preview",
        "clusterApiVersion": "2015-03-01-preview",
        "clusterStorageAccountName": "[concat(parameters('clusterName'),'store')]"
    },
    "resources": [
        {
        "name": "[variables('clusterStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": { },
        "properties": {
            "accountType": "Standard_LRS"
        }
        },
        {
        "name": "[parameters('clusterName')]",
        "type": "Microsoft.HDInsight/clusters",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('clusterApiVersion')]",
        "dependsOn": [ "[concat('Microsoft.Storage/storageAccounts/',variables('clusterStorageAccountName'))]" ],
        "tags": {

        },
        "properties": {
            "clusterVersion": "3.4",
            "osType": "Linux",
            "tier": "standard",
            "clusterDefinition": {
            "kind": "[parameters('clusterType')]",
            "configurations": {
                "gateway": {
                "restAuthCredential.isEnabled": true,
                "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                },
                "hive-site": {
                    "javax.jdo.option.ConnectionDriverName": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "javax.jdo.option.ConnectionURL": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "javax.jdo.option.ConnectionUserName": "johndole",
                    "javax.jdo.option.ConnectionPassword": "myPassword$"
                },
                "hive-env": {
                    "hive_database": "Existing MSSQL Server database with SQL authentication",
                    "hive_database_name": "myhive20160901",
                    "hive_database_type": "mssql",
                    "hive_existing_mssql_server_database": "myhive20160901",
                    "hive_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "hive_hostname": "myadla0901dbserver.database.windows.net"
                },
                "oozie-site": {
                    "oozie.service.JPAService.jdbc.driver": "com.microsoft.sqlserver.jdbc.SQLServerDriver",
                    "oozie.service.JPAService.jdbc.url": "jdbc:sqlserver://myadla0901dbserver.database.windows.net;database=myhive20160901;encrypt=true;trustServerCertificate=true;create=false;loginTimeout=300",
                    "oozie.service.JPAService.jdbc.username": "johndole",
                    "oozie.service.JPAService.jdbc.password": "myPassword$",
                    "oozie.db.schema.name": "oozie"
                },
                "oozie-env": {
                    "oozie_database": "Existing MSSQL Server database with SQL authentication",
                    "oozie_database_name": "myhive20160901",
                    "oozie_database_type": "mssql",
                    "oozie_existing_mssql_server_database": "myhive20160901",
                    "oozie_existing_mssql_server_host": "myadla0901dbserver.database.windows.net",
                    "oozie_hostname": "myadla0901dbserver.database.windows.net"
                }            
            }
            },
            "storageProfile": {
            "storageaccounts": [
                {
                "name": "[concat(variables('clusterStorageAccountName'),'.blob.core.windows.net')]",
                "isDefault": true,
                "container": "[parameters('clusterName')]",
                "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('clusterStorageAccountName')), variables('defaultApiVersion')).key1]"
                }
            ]
            },
            "computeProfile": {
            "roles": [
                {
                "name": "headnode",
                "targetInstanceCount": "2",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
                }
                },
                {
                "name": "workernode",
                "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                "hardwareProfile": {
                    "vmSize": "Standard_D3"
                },
                "osProfile": {
                    "linuxOperatingSystemProfile": {
                    "username": "[parameters('sshUserName')]",
                    "password": "[parameters('sshPassword')]"
                    }
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

<!---HONumber=AcomDC_0914_2016-->