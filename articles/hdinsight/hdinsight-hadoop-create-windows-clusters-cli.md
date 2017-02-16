---
title: "Creación de clústeres de Hadoop basados en Windows en HDInsight con la CLI de Azure"
description: "Aprenda a crear clústeres de Hadoop basados en Linux para Azure HDInsight mediante la CLI de Azure."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c766544c-c16f-4bfa-89d0-592325d24250
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 84d52dccef4e2d9a1ae253831b5d8f86b6fb50a6
ms.openlocfilehash: 489067e44525532f2913f44156a5eeb19214d250


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-cli"></a>Creación de clústeres de Hadoop basados en Windows en HDInsight con la CLI de Azure

[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Aprenda a crear clústeres de Hadoop basados en Windows en HDInsight mediante la CLI de Azure. 

La información de este artículo solo se aplica a los clústeres de HDInsight para Windows. Para más información sobre la creación de clústeres basados en Linux, consulte [Creación de clústeres de Hadoop en HDInsight con la CLI de Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Requisitos previos:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de empezar las instrucciones de este artículo, debe tener lo siguiente:

* **Suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Azure CLI**.
  
[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)] 

### <a name="access-control-requirements"></a>Requisitos de control de acceso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="connect-to-azure"></a>Conexión a Azure
Use el comando siguiente para conectarse a Azure:

    azure login

Para más información acerca de cómo autenticarse con una cuenta profesional o educativa, consulte [Conexión a una suscripción de Azure desde la CLI de Azure](../xplat-cli-connect.md).

Use el siguiente comando para cambiar al modo de Azure Resource Manager:

    azure config mode arm

Para obtener ayuda, use el modificador **-h** .  Por ejemplo:

    azure hdinsight cluster create -h

## <a name="create-clusters"></a>Creación de clústeres
Para poder crear un clúster de HDInsight, debe tener un grupo de administración de recursos y una cuenta de Azure Blob Storage. Para crear un clúster de HDInsight, debe especificar lo siguiente:

* **Grupo de recursos de Azure**: se debe crear una cuenta de Data Lake Analytics dentro de un grupo de recursos de Azure. Administrador de recursos de Azure permite trabajar con los recursos de la aplicación como un grupo. Puede implementar, actualizar o eliminar todos los recursos de la aplicación en una operación única y coordinada.
  
    Para mostrar los grupos de recursos de su suscripción:
  
        azure group list
  
    Para crear un nuevo grupo de recursos:
  
        azure group create -n "<Resource Group Name>" -l "<Azure Location>"
* **Nombre del clúster de HDInsight**
* **Ubicación**: uno de los centros de datos de Azure que admite clústeres de HDInsight. Para obtener una lista de ubicaciones admitidas, consulte los [precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).
* **Cuenta de almacenamiento predeterminada**: HDInsight usa un contenedor de almacenamiento de blobs de Azure como sistema de archivos predeterminado. Es necesario tener una cuenta de Almacenamiento de Azure antes de crear un clúster de HDInsight.
  
    Para crear una cuenta de almacenamiento de Azure:
  
        azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS
  
  > [!NOTE]
  > La cuenta de Almacenamiento debe colocarse con HDInsight en el mismo centro de datos.
  > El tipo de cuenta de almacenamiento no puede ser ZRS, porque ZRS no admite tablas.
  > 
  > 
  
    Para más información sobre la creación de una cuenta de Azure Storage mediante el portal de Azure, consulte [Creación, administración o eliminación de una cuenta de Storage][azure-create-storageaccount].
  
    Si ya tiene una cuenta de almacenamiento pero no sabe su nombre ni su clave, puede usar los comandos siguientes para recuperar dicha información:
  
        -- Lists Storage accounts
        azure storage account list
        -- Shows a Storage account
        azure storage account show "<Storage Account Name>"
        -- Lists the keys for a Storage account
        azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"
  
    Para más información sobre cómo obtener la información mediante el portal de Azure, consulte la sección "Administración de la cuenta de almacenamiento" del artículo [Acerca de las cuentas de almacenamiento de Azure](../storage/storage-create-storage-account.md#manage-your-storage-account).
* **(Opcional) Contenedor de blobs predeterminado**: el comando **azure hdinsight cluster create** crea el contenedor si aún no existe. Si opta por crear el contenedor antes, puede usar el comando siguiente:
  
    azure storage container create --account-name "<Storage Account Name>" --account-key <Storage Account Key> [ContainerName]

Cuando tenga preparada la cuenta de almacenamiento, podrá crear un clúster.

    azure hdinsight cluster create -g <Resource Group Name> -c <HDInsight Cluster Name> -l <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --workerNodeCount 2 --headNodeSize Large --workerNodeSize Large --defaultStorageAccountName <Azure Storage Account Name>.blob.core.windows.net --defaultStorageAccountKey "<Default Storage Account Key>" --defaultStorageContainer <Default Blob Storage Container> --userName admin --password "<HTTP User Password>" --rdpUserName <RDP Username> --rdpPassword "<RDP User Password" --rdpAccessExpiry "03/01/2016"


## <a name="create-clusters-using-configuration-files"></a>Creación de clústeres mediante archivos de configuración
Normalmente, se crea un clúster de HDInsight, se ejecutan trabajos en él y luego se elimina el clúster para reducir los costos. La interfaz de línea de comandos le da la opción de guardar las configuraciones en un archivo para que pueda volver a usarlas cada vez que cree un clúster.  

    azure hdinsight config create [options ] <Config File Path> <overwirte>
    azure hdinsight config add-config-values [options] <Config File Path>
    azure hdinsight config add-script-action [options] <Config File Path>

Ejemplo: Crear un archivo de configuración que contenga una acción de sript para ejecutarlo al crear un clúster.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"
    azure hdinsight cluster create --configurationPath "C:\myFiles\configFile.config"

## <a name="create-clusters-with-script-action"></a>Creación de clústeres con acción de scripts
Cree un archivo de configuración que contenga una acción de scripts para ejecutarlo al crear un clúster.

    azure hdinsight config create "C:\myFiles\configFile.config"
    azure hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <scriptActionURI> --name myScriptAction --parameters "-param value"

Creación de un clúster con una acción de scripts

    azure hdinsight cluster create -g myarmgroup01 -l westus -y Windows --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01


Para información sobre la acción de script, vea [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster.md).

## <a name="create-clusters-using-resource-manager-templates"></a>Creación de clústeres con plantillas de Resource Manager
Puede utilizar la CLI para crear clústeres llamando a plantillas de ARM. Consulte [Implementación con la CLI de Azure](hdinsight-hadoop-create-windows-clusters-arm-templates.md#deploy-with-azure-cli).

## <a name="see-also"></a>Otras referencias
* [Introducción a HDInsight de Azure](hdinsight-hadoop-linux-tutorial-get-started.md) : aprenda a empezar a trabajar con su clúster de HDInsight
* [Envío de trabajos de Hadoop mediante programación](hdinsight-submit-hadoop-jobs-programmatically.md) : aprenda a enviar trabajos a HDInsight mediante programación
* [Administración de clústeres de Hadoop en HDInsight mediante la CLI de Azure](hdinsight-administer-use-command-line.md)
* [Uso de la interfaz de la línea de comandos entre plataformas de Azure con el Administrador de servicios de Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)




<!--HONumber=Jan17_HO5-->


