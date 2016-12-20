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
ms.date: 10/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 67442bf4b04f6f3799d30f7ce26547c8145d9168


---
# <a name="how-to-delete-an-hdinsight-cluster"></a>Eliminación de un clúster de HDInsight
La facturación de los clústeres de HDInsight comienza una vez que se crea el clúster y se detiene cuando el clúster se elimina y se prorratea por minuto, por lo que siempre debe eliminar el clúster cuando ya no esté en uso. En este documento, obtendrá información sobre cómo eliminar un clúster mediante el Portal de Azure, Azure PowerShell y la CLI de Azure.

> [!IMPORTANT]
> Al eliminar un clúster de HDInsight, no se eliminan las cuentas de Almacenamiento de Azure asociadas a este. Por tanto, podrá conservar y reutilizar los datos almacenados por el clúster.
> 
> 

## <a name="azure-portal"></a>Portal de Azure
1. Inicie sesión en el [Portal de Azure](https://portal.azure.com) y seleccione su clúster de HDInsight. Si este no está anclado al panel, puede buscarlo por su nombre utilizando el campo de búsqueda (icono de lupa) situado en la parte derecha de la barra de navegación.
   
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




<!--HONumber=Nov16_HO3-->


