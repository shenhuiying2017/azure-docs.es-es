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
ms.date="03/07/2016"
ms.author="larryfr"/>

#Eliminación de un clúster de HDInsight

Los clústeres de HDInsight se facturan por hora, por lo que siempre debe eliminar aquellos que ya no se estén utilizando. En este documento, obtendrá información sobre cómo eliminar un clúster mediante el Portal de Azure, Azure PowerShell y la CLI de Azure.

> [AZURE.IMPORTANT] Al eliminar un clúster de HDInsight, no se eliminan las cuentas de Almacenamiento de Azure asociadas a este. Por tanto, podrá conservar y reutilizar los datos almacenados por el clúster.

##Portal de Azure

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com) y seleccione su clúster de HDInsight. Si este no está anclado al panel, puede buscarlo por su nombre utilizando el campo de búsqueda (icono de lupa) situado en la parte derecha de la barra de navegación.

    ![búsqueda del portal](./media/hdinsight-delete-cluster/navbar.png)

2. Una vez que se abra la hoja para el clúster, seleccione el icono __Eliminar__. Cuando se le pida, seleccione __Sí__ para eliminar el clúster.

    ![eliminar icono](./media/hdinsight-delete-cluster/deletecluster.png)

##Azure PowerShell

> [AZURE.NOTE] Si no ha instalado ni configurado Azure PowerShell, siga los pasos que se describen en el documento [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

Desde un símbolo del sistema de PowerShell, utilice el siguiente comando para eliminar el clúster:

    Remove-AzureRmHDInsightCluster -ClusterName CLUSTERNAME

Reemplace __CLUSTERNAME__ por el nombre del clúster de HDInsight.

##Azure CLI

[AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

Desde un símbolo del sistema, utilice el siguiente comando para eliminar el clúster:

    azure hdinsight cluster delete CLUSTERNAME
    
Reemplace __CLUSTERNAME__ por el nombre del clúster de HDInsight.

<!---HONumber=AcomDC_0420_2016-->