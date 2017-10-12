---
title: "Creación de clústeres de Hadoop con PowerShell - Azure HDInsight | Microsoft Docs"
description: "Aprenda a crear clústeres de Hadoop, HBase, Storm o Spark en Linux para HDInsight con Azure PowerShell."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4208deca-d64a-45e1-8948-2673d5d7678c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/28/2017
ms.author: nitinme
ms.openlocfilehash: 079a3d1c7f91477d641dbc65fe0f04e86a0dcd30
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Crear clústeres basados en Linux en HDInsight con Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell es un eficaz entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Microsoft Azure. En este documento se ofrece información sobre cómo crear un clúster de HDInsight basado en Linux mediante Azure PowerShell. También se incluye un script de ejemplo.

> [!NOTE]
> Azure PowerShell solo está disponible en clientes de Windows. Si está usando un cliente de Linux, Unix o Mac OS X, consulte [Crear clústeres basados en Linux en HDInsight con la CLI de Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) para más información sobre el uso de la CLI de Azure para crear un clúster.

## <a name="prerequisites"></a>Requisitos previos
Antes de iniciar este procedimiento, debe tener lo siguiente:

* Una suscripción de Azure. Vea [Obtener evaluación gratuita de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-azurerm-ps)

    > [!IMPORTANT]
    > La compatibilidad con Azure PowerShell para administrar recursos de HDInsight mediante Azure Service Manager está **en desuso** y dejó de estar disponible por completo el 1 de enero de 2017. En los pasos descritos en este documento, se usan los nuevos cmdlets de HDInsight que funcionan con Azure Resource Manager.
    >
    > Para instalar la última versión de Azure PowerShell, siga los pasos descritos en [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). Si tiene scripts que se deben modificar para usar los nuevos cmdlets que funcionan con Azure Resource Manager, consulte [Migrating to Azure Resource Manager-based development tools for HDInsight clusters](hdinsight-hadoop-development-using-azure-resource-manager.md) (Migración a herramientas de desarrollo basadas en Azure Resource Manager para clústeres de HDInsight) para más información.

## <a name="create-cluster"></a>Crear clúster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para crear un clúster de HDInsight con Azure PowerShell, debe realizar los procedimientos siguientes:

* Creación de un grupo de recursos de Azure
* Creación de una cuenta de almacenamiento de Azure
* Creación de un contenedor de blobs de Azure
* Creación de un clúster de HDInsight

El script siguiente muestra cómo crear un nuevo clúster:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

Los valores que se especifican para el inicio de sesión de clúster se usan para crear la cuenta de usuario de Hadoop del clúster. Use esta cuenta para conectarse a servicios hospedados en el clúster como interfaces de usuario web o API de REST.

Los valores que se especifican para el usuario SSH se usan para crear el usuario SSH para el clúster. Use esta cuenta para iniciar una sesión remota de SSH en el clúster y ejecutar trabajos. Para más información, vea el documento [Uso de SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

> [!IMPORTANT]
> Si tiene pensado usar más de 32 nodos de trabajo (en el momento de crear el clúster o cambiando el tamaño del clúster después de su creación), también debe especificar un tamaño de nodo principal con al menos 8 núcleos y 14 GB de RAM.
>
> Para obtener más información acerca de los tamaños de nodo y los costos asociados, consulte [Precios de HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Un clúster puede tardar hasta 20 minutos en crearse.

## <a name="create-cluster-configuration-object"></a>Creación de un clúster: objeto de configuración

También puede crear un objeto de configuración de HDInsight mediante el cmdlet `New-AzureRmHDInsightClusterConfig`. A continuación, puede modificar este objeto de configuración con el fin de habilitar otras opciones de configuración para el clúster. Por último, utilice el parámetro `-Config` del cmdlet `New-AzureRmHDInsightCluster` para emplear la configuración.

El script siguiente crea un objeto de configuración para configurar un R Server en el tipo de clúster de HDInsight. La configuración habilita un nodo perimetral, RStudio y una cuenta de almacenamiento adicional.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-98)]

> [!WARNING]
> No se admite el uso de una cuenta de almacenamiento en una ubicación diferente a la del clúster de HDInsight. Cuando utilice este ejemplo, cree la cuenta de almacenamiento adicional en la misma ubicación que el servidor.

## <a name="customize-clusters"></a>Personalización de los clústeres

* Consulte [Personalización de los clústeres de HDInsight con Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Solución de problemas

Si experimenta problemas con la creación de clústeres de HDInsight, consulte los [requisitos de control de acceso](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya ha creado con éxito un clúster de HDInsight, use los siguientes recursos para aprender a trabajar con él.

### <a name="hadoop-clusters"></a>Clústeres Hadoop

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>Clústeres HBase

* [Introducción a HBase en HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desarrollo de aplicaciones de Java para HBase en HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clústeres Storm

* [Desarrollo de topologías de Java para Storm en HDInsight](hdinsight-storm-develop-java-topology.md)
* [Uso de componentes de Python en Storm en HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implementación y supervisión de topologías con Storm en HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Clústeres de Spark

* [Crear una aplicación independiente con Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Ejecutar trabajos de forma remota en un clúster de Spark mediante Livy](hdinsight-apache-spark-livy-rest-interface.md)
* [Spark with BI: Realizar el análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark con aprendizaje automático: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming con Spark: uso de Spark en HDInsight para compilar aplicaciones de streaming en tiempo real](hdinsight-apache-spark-eventhub-streaming.md)

