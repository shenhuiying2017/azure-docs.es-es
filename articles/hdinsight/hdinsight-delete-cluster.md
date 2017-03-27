---
title: "Eliminación de un clúster de HDInsight | Microsoft Docs"
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
ms.date: 02/08/2017
ms.author: larryfr
ms.custom: H1Hack27Feb2017,hdinsightactive
translationtype: Human Translation
ms.sourcegitcommit: d391c5c6289aa63e969f63f189eb5db680883f0a
ms.openlocfilehash: fbb561f4bfda27c74ffdc08c1b07f4adb83286ab
ms.lasthandoff: 03/01/2017

---
# <a name="delete-an-hdinsight-cluster-using-your-browser-powershell-or-the-azure-cli"></a>Eliminación de un clúster de HDInsight con el explorador, PowerShell o la CLI de Azure

La facturación del clúster de HDInsight se inicia una vez creado el clúster y solo se detiene cuando se elimina. Se facturan por minuto realizando una prorrata, por lo que siempre debe eliminar aquellos que ya no se estén utilizando. En este documento, obtendrá información sobre cómo eliminar un clúster mediante Azure Portal, Azure PowerShell y la CLI de Azure.

> [!IMPORTANT]
> Al eliminar un clúster de HDInsight, no se eliminan las cuentas de Azure Storage asociadas a este. Puesto que no se elimina la cuenta de almacenamiento, los datos se conservan y se pueden reutilizar en el futuro.

## <a name="azure-portal"></a>Portal de Azure

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y seleccione su clúster de HDInsight. Si este no está anclado al panel, puede buscarlo por su nombre utilizando el campo de búsqueda.
   
    ![búsqueda del portal](./media/hdinsight-delete-cluster/navbar.png)

2. Una vez que se abra la hoja para el clúster, seleccione el icono **Eliminar**. Cuando se le pida, seleccione **Sí** para eliminar el clúster.
   
    ![eliminar icono](./media/hdinsight-delete-cluster/deletecluster.png)

## <a name="azure-powershell"></a>Azure PowerShell

Desde un símbolo del sistema de PowerShell, utilice el siguiente comando para eliminar el clúster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight.

## <a name="azure-cli"></a>Azure CLI

Desde un símbolo del sistema, utilice el siguiente comando para eliminar el clúster:

    azure hdinsight cluster delete CLUSTERNAME

Reemplace **CLUSTERNAME** por el nombre del clúster de HDInsight.


