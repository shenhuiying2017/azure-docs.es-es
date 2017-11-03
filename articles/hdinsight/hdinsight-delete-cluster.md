---
title: "Eliminación de un clúster de HDInsight - Azure | Microsoft Docs"
description: "Información sobre las distintas formas de eliminar un clúster de HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 55f7838b-9786-47ff-96db-1b64437bd0bb
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/23/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 6bacd40627b815c949491b70f8290e40b79e488c
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2017
---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Eliminación de un clúster de HDInsight con el explorador, PowerShell o la CLI de Azure

La facturación del clúster de HDInsight se inicia una vez creado el clúster y solo se detiene cuando se elimina. Se facturan por minuto realizando una prorrata, por lo que siempre debe eliminar aquellos que ya no se estén utilizando. En este documento, aprenderá a eliminar un clúster mediante el portal de Azure, Azure PowerShell y la CLI de Azure.

> [!IMPORTANT]
> Al eliminar un clúster de HDInsight, no se eliminan las cuentas de Azure Storage o Data Lake Store asociadas a este. Puede volver a usar los datos almacenados en esos servicios en el futuro.

## <a name="azure-portal"></a>Portal de Azure

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y seleccione su clúster de HDInsight. Si este no está anclado al panel, puede buscarlo por su nombre utilizando el campo de búsqueda.
   
    ![búsqueda del portal](./media/hdinsight-delete-cluster/navbar.png)

2. En la configuración del clúster, seleccione el icono **Eliminar**. Cuando se le pida, seleccione **Sí** para eliminar el clúster.
   
    ![eliminar icono](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Desde un símbolo del sistema de PowerShell, utilice el siguiente comando para eliminar el clúster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight.

## <a name="azure-cli-10"></a>CLI de Azure 1.0

Desde un símbolo del sistema, utilice el siguiente comando para eliminar el clúster:

    azure hdinsight cluster delete CLUSTERNAME

Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight.

> [!NOTE]
> CLI de Azure 2.0 no admite la eliminación de clústeres de HDInsight en la actualidad (23 de octubre de 2017).