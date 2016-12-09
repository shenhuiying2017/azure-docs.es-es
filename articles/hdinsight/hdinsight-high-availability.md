---
title: "Disponibilidad de clústeres de Hadoop en HDInsight | Microsoft Docs"
description: "HDInsight implementa clústeres confiables y de alta disponibilidad con un nodo principal adicional."
services: hdinsight
tags: azure-portal
editor: cgronlun
manager: jhubbard
author: mumian
documentationcenter: 
ms.assetid: ccab792d-60d6-4287-96c2-479e5d0cf358
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/21/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: cc59d7785975e3f9acd574b516d20cd782c22dac
ms.openlocfilehash: 1f40d6119d5fefc48c1e12d510423f996239fe14


---
# <a name="availability-and-reliability-of-windows-based-hadoop-clusters-in-hdinsight"></a>Disponibilidad y confiabilidad de clústeres de Hadoop basados en Windows en HDInsight
> [!NOTE]
> Los pasos usados en este documento son específicos de los clústeres de HDInsight basados en Windows. Si va a usar un clúster basado en Linux, consulte [Disponibilidad y fiabilidad de clústeres de Hadoop en HDInsight](hdinsight-high-availability-linux.md) para tener información específica de Linux.
>
>

HDInsight permite a los clientes implementar varios tipos de clúster para diferentes cargas de trabajo de análisis de datos. Los tipos de clúster que se ofrecen hoy en día son los clústeres de Hadoop para cargas de trabajo  de consulta y análisis, los clústeres de HBase para cargas de trabajo de NoSQL y los clústeres de Storm para cargas de trabajo de procesamiento de eventos en tiempo real. Dentro de un tipo de clúster determinado hay diferentes roles para los distintos nodos. Por ejemplo:

* Los clústeres de Hadoop para HDInsight se implementan con dos roles:

  * Nodo principal (2 nodos)
  * Nodo de datos (al menos 1 nodo)
* Los clústeres de HBase para HDInsight se implementan con tres roles:

  * Servidores principales (2 nodos)
  * Servidores de región (al menos 1 nodo)
  * Nodos maestro o Zookeeper (3 nodos)
* Los clústeres de Storm para HDInsight se implementan con tres roles:

  * Nodos Nimbus (2 nodos)
  * Servidores de supervisor (al menos 1 nodo)
  * Nodos Zookeeper (3 nodos)

Las implementaciones estándar de clústeres de Hadoop normalmente tienen un solo nodo principal. HDInsight quita este único punto de error con la incorporación de un nodo principal secundario/servidor principal/nodo Nimbus para aumentar la disponibilidad y confiabilidad del servicio necesario para administrar las cargas de trabajo. Estos nodos principales/servidores principales/nodos Nimbus están diseñados para administrar los errores de los nodos de trabajo sin problemas, pero cualquier interrupción de los servicios maestros que se ejecutan en el nodo principal haría que el clúster deje de funcionar.

Se han agregado los nodos [ZooKeeper](http://zookeeper.apache.org/) (ZK), que se usan para la elección de líder de nodos principales y para asegurar que los nodos de trabajo y las puertas de enlace (GW) saben cuándo conmutar por error al nodo principal secundario (Nodo principal1) cuando el nodo principal activo (Nodo principal0) pasa a estar inactivo.

![Diagrama de los nodos principales de gran confiabilidad en la implementación de Hadoop en HDInsight.](./media/hdinsight-high-availability/hadoop.high.availability.architecture.diagram.png)

## <a name="check-active-head-node-service-status"></a>Comprobación del estado de los servicios del nodo principal activo
Para determinar qué nodo principal está activo y comprobar el estado de los servicios que se ejecutan en ese nodo principal, debe conectarse al clúster de Hadoop mediante el Protocolo de escritorio remoto (RDP). Para ver instrucciones de RDP, consulte [Administración de clústeres de Hadoop en HDInsight mediante Azure Portal](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp). Una vez conectado remotamente al clúster, haga doble clic en el icono **Estado de disponibilidad de los servicios de Hadoop**, situado en el escritorio, para obtener el estado sobre qué servicios de nodo principal se están ejecutando (Namenode, Jobtracker, Templeton, Oozieservice, Metastore y Hiveserver2), o para HDI 3.0 los servicios Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore y Hiveserver2.

![](./media/hdinsight-high-availability/Hadoop.Service.Availability.Status.png)

En la captura de pantalla, el nodo principal activo es *headnode0*.

## <a name="access-log-files-on-the-secondary-head-node"></a>Acceso a los archivos de registro en el nodo principal secundario
Para acceder a registros de trabajo en el nodo principal secundario en el caso de que haya pasado a ser el nodo principal, la exploración de la interfaz de usuario de la herramienta de seguimiento de trabajos todavía funciona como lo hace para el nodo activo primario. Para acceder a la herramienta de seguimiento de trabajos, debe conectarse al clúster de Hadoop mediante el Protocolo de escritorio remoto (RDP) tal y como se describió en la sección anterior. Una vez que se haya conectado de forma remota al clúster, haga doble clic en el icono **Estado de nodo de nombres de Hadoop** del escritorio y, a continuación, haga clic en **Registros del nodo de nombres** para obtener el directorio de registros del nodo principal secundario.

![](./media/hdinsight-high-availability/Hadoop.Head.Node.Log.Files.png)

## <a name="configure-head-node-size"></a>Configuración del tamaño del nodo principal
Los nodos principales se asignan como máquinas virtuales (VM) grandes de forma predeterminada. El tamaño es adecuado para la administración de la mayoría de trabajos de Hadoop que se ejecutan en el clúster. Pero hay escenarios que pueden requerir máquinas virtuales extragrandes para los nodos principales. Un ejemplo es cuando el clúster tiene que administrar un gran número de pequeños trabajos de Oozie.

Las máquinas virtuales extragrandes se pueden configurar usando cmdlets de Azure PowerShell o el SDK de HDInsight.

La creación y aprovisionamiento de un clúster mediante Azure PowerShell se documenta en [Administración de HDInsight con PowerShell](hdinsight-administer-use-powershell.md). La configuración de un nodo principal extragrande requiere la adición del parámetro `-HeadNodeVMSize ExtraLarge` al cmdlet de `New-AzureRmHDInsightcluster` usado en este código.

    # Create a new HDInsight cluster in Azure PowerShell
    # Configured with an ExtraLarge head-node VM
    New-AzureRmHDInsightCluster `
                -ResourceGroupName $resourceGroupName `
                -ClusterName $clusterName `
                -Location $location `
                -HeadNodeVMSize ExtraLarge `
                -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $storageAccountKey `
                -DefaultStorageContainerName $containerName  `
                -ClusterSizeInNodes $clusterNodes

Para el SDK, la historia es similar. La creación y aprovisionamiento de un clúster mediante el SDK se documenta en [Uso del SDK .NET de HDInsight](hdinsight-provision-clusters.md). La configuración de un nodo principal extragrande requiere la adición del parámetro `HeadNodeSize = NodeVMSize.ExtraLarge` al método de `ClusterCreateParameters()` usado en este código.

    # Create a new HDInsight cluster with the HDInsight SDK
    # Configured with an ExtraLarge head-node VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
        Name = clustername,
        Location = location,
        HeadNodeSize = NodeVMSize.ExtraLarge,
        DefaultStorageAccountName = storageaccountname,
        DefaultStorageAccountKey = storageaccountkey,
        DefaultStorageContainer = containername,
        UserName = username,
        Password = password,
        ClusterSizeInNodes = clustersize
    };


## <a name="next-steps"></a>Pasos siguientes
* [Apache ZooKeeper](http://zookeeper.apache.org/)
* [Conexión a los clústeres de HDInsight con RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp)
* [Uso del SDK .NET de HDInsight](hdinsight-provision-clusters.md)



<!--HONumber=Nov16_HO3-->


