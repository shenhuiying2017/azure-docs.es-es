<properties
pageTitle="Eliminación de un clúster de HDInsight | Azure"
description="Información sobre las distintas formas de eliminar un clúster de HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="05/20/2016"
ms.author="larryfr"/>

#Eliminación de un clúster de HDInsight

La facturación de los clústeres de HDInsight comienza una vez que se crea el clúster y se detiene cuando el clúster se elimina y se prorratea por minuto, por lo que siempre debe eliminar el clúster cuando ya no esté en uso. En este documento, obtendrá información sobre cómo eliminar un clúster mediante el Portal de Azure, Azure PowerShell y la CLI de Azure.

> [AZURE.IMPORTANT] Al eliminar un clúster de HDInsight, no se eliminan las cuentas de Almacenamiento de Azure asociadas a este. Por tanto, podrá conservar y reutilizar los datos almacenados por el clúster.

##Portal de Azure

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com) y seleccione su clúster de HDInsight. Si este no está anclado al panel, puede buscarlo por su nombre utilizando el campo de búsqueda (icono de lupa) situado en la parte derecha de la barra de navegación.

    ![búsqueda del portal](./media/hdinsight-delete-cluster/navbar.png)

2. Una vez que se abra la hoja para el clúster, seleccione el icono __Eliminar__. Cuando se le pida, seleccione __Sí__ para eliminar el clúster.

    ![eliminar icono](./media/hdinsight-delete-cluster/deletecluster.png)

##Azure PowerShell

Desde un símbolo del sistema de PowerShell, utilice el siguiente comando para eliminar el clúster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Reemplace __CLUSTERNAME__ por el nombre del clúster de HDInsight.

##Azure CLI

Desde un símbolo del sistema, utilice el siguiente comando para eliminar el clúster:

    azure hdinsight cluster delete CLUSTERNAME
    
Reemplace __CLUSTERNAME__ por el nombre del clúster de HDInsight.

<!---HONumber=AcomDC_0525_2016-->