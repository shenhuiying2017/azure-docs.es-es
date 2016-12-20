---
title: "Administración de clústeres de Hadoop mediante la CLI de Azure | Microsoft Docs"
description: "Cómo usar la CLI de Azure para administrar clústeres de Hadoop en HDInsight"
services: hdinsight
editor: cgronlun
manager: jhubbard
author: mumian
tags: azure-portal
documentationcenter: 
ms.assetid: 4f26c79f-8540-44bd-a470-84722a9e4eca
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a8115205c674221ee3dc46b0c3149c64081135b8


---
# <a name="manage-hadoop-clusters-in-hdinsight-using-the-azure-cli"></a>Administración de clústeres de Hadoop en HDInsight mediante la CLI de Azure
[!INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Aprenda a usar la [interfaz de la línea de comandos de Azure](../xplat-cli-install.md) para administrar clústeres de Hadoop en Azure HDInsight. La CLI de Azure se implementa en Node.js y se puede usar en cualquier plataforma compatible con Node.js, entre las que se incluyen Windows, Mac y Linux.

Este artículo solo trata del uso de la CLI de Azure con HDInsight. Para obtener información general sobre cómo usar la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure][azure-command-line-tools].

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este artículo, debe tener lo siguiente:

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **CLI de Azure** - Vea [Instalar y configurar la CLI de Azure](../xplat-cli-install.md) para obtener información de instalación y configuración.
* **Conectarse a Azure**mediante el comando siguiente:
  
        azure login
  
    Para más información acerca de cómo autenticarse con una cuenta profesional o educativa, consulte [Conexión a una suscripción de Azure desde la CLI de Azure](../xplat-cli-connect.md).
* **Cambiar al modo de Administrador de recursos de Azure**con el siguiente comando:
  
        azure config mode arm

Para obtener ayuda, use el modificador **-h** .  Por ejemplo:

    azure hdinsight cluster create -h

## <a name="create-clusters"></a>Creación de clústeres
Consulte [Creación de clústeres basados en Linux en HDInsight con la CLI de Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md).

## <a name="list-and-show-cluster-details"></a>Enumeración y visualización de los detalles del clúster
Use los comandos siguientes para enumerar y mostrar los detalles del clúster:

    azure hdinsight cluster list
    azure hdinsight cluster show <Cluster Name>

![HDI.CLIListCluster][image-cli-clusterlisting]

## <a name="delete-clusters"></a>Eliminación de clústeres
Use el comando siguiente para eliminar un clúster:

    azure hdinsight cluster delete <Cluster Name>

También puede eliminar un clúster eliminando el grupo de recursos que lo contiene. Tenga en cuenta que esto eliminará todos los recursos del grupo, incluida la cuenta de almacenamiento predeterminada.

    azure group delete <Resource Group Name>

## <a name="scale-clusters"></a>Escalado de clústeres
Para cambiar el tamaño del clúster de Hadoop:

    azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## <a name="enabledisable-http-access-for-a-cluster"></a>Habilitar/deshabilitar el acceso HTTP para un clúster
    azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
    azure hdinsight cluster disable-http-access [options] <Cluster Name>

## <a name="enabledisable-rdp-access-for-a-cluster"></a>Habilitar/deshabilitar el acceso RDP para un clúster
      azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
      azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido a realizar diferentes tareas administrativas en clústeres de HDInsight. Para obtener más información, consulte los artículos siguientes:

* [Administración de HDInsight mediante Azure Portal][hdinsight-admin-portal]
* [Administración de HDInsight mediante Azure PowerShell][hdinsight-admin-powershell]
* [Introducción a HDInsight de Azure][hdinsight-get-started]
* [Uso de la CLI de Azure][azure-command-line-tools]

[azure-command-line-tools]: ../xplat-cli-install.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Enumerar y mostrar los clústeres"



<!--HONumber=Nov16_HO3-->


