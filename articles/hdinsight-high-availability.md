<properties linkid="manage-services-hdinsight-high-availability" urlDisplayName="HDInsight High Availability" pageTitle="Availability of Hadoop clusters in HDInsight | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight deploys highly available and reliable clusters." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Availability of Hadoop clusters in HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Disponibilidad y fiabilidad de clústeres de Hadoop en HDInsight

## Introducción

Se ha agregado un segundo nodo principal a los clústeres de Hadoop desarrollados por HDInsight para aumentar la disponibilidad y fiabilidad del servicio necesario para administrar cargas de trabajo. Las implementaciones estándar de clústeres de Hadoop normalmente tienen un solo nodo principal. Estos clústeres están diseñados para administrar los errores de los nodos de trabajo sin problemas, pero cualquier interrupción de los servicios maestros que se ejecutan en el nodo principal haría que el clúster dejara de funcionar.

![][]

HDInsight quita este único punto de error con la incorporación de un nodo principal secundario (Nodo principal1). Se han agregado nodos de [ZooKeeper][] (ZK) que se usan para la elección de líder de nodos principales y para asegurar que los nodos de trabajo y las puertas de enlace (GW) saben cuándo conmutar por error al nodo principal secundario (Nodo principal1) cuando el nodo principal activo (Nodo principal0) pasa a estar inactivo.

## Comprobación del estado de los servicios en el nodo principal activo

Para determinar qué nodo principal está activo y comprobar el estado de los servicios que se ejecutan en ese nodo principal, debe conectarse al clúster de Hadoop mediante el Protocolo de escritorio remoto (RDP). La capacidad de interactuar remotamente en el clúster está deshabilitada de forma predeterminada en Azure, por lo que primero se debe habilitar. Para obtener instrucciones sobre cómo hacer esto en el portal, consulte [Conexión a los clústeres de HDInsight con RDP][].
Una vez que se ha conectado remotamente al clúster, haga doble clic en el icono **Estado de disponibilidad de los servicios de Hadoop** situado del escritorio para obtener el estado sobre qué nodo principal se están ejecutando los servicios Namenode, Jobtracker, Templeton, Oozieservice, Metastore y Hiveserver2, o para HDI 3.0, Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore y Hiveserver2.

![][1]

## Acceso a los archivos de registro en el nodo principal secundario

Para acceder a registros de trabajo en el nodo principal secundario en el caso de que haya pasado a ser el nodo principal, la exploración de la interfaz de usuario de la herramienta de seguimiento de trabajos todavía funciona como lo hace para el nodo activo primario. Para acceder a la herramienta de seguimiento de trabajos, debe conectarse al clúster de Hadoop mediante el Protocolo de escritorio remoto (RDP) tal y como se describió en la sección anterior. Una vez que se ha conectado de forma remota de clúster, haga doble clic en el icono **Nodo de nombres de Hadoop** situado en el escritorio y después haga clic en **Registros del nodo de nombres** para obtener el directorio de registros del nodo principal secundario.

![][2]

## Configuración del tamaño del nodo principal

Los nodos principales se asignan como máquinas virtuales grandes de forma predeterminada. El tamaño es adecuado para la administración de la mayoría de trabajos de Hadoop que se ejecutan en el clúster. Pero hay escenarios que pueden requerir máquinas virtuales extragrandes para los nodos principales. Un ejemplo es cuando el clúster tiene que administrar un gran número de pequeños trabajos de Oozie.

Las máquinas virtuales extragrandes se pueden configurar usando cmdlets de Azure PowerShell o el SDK de HDInsight.

La creación y aprovisionamiento de un clúster usando PowerShell se documenta en [Administración de HDInsight con PowerShell][]. La configuración de un nodo principal extragrande requiere la adición del parámetro `-HeadNodeVMSize ExtraLarge` al cmdlet `New-AzureHDInsightcluster` usado en este código.

    # Create a new HDInsight cluster in Azure PowerShell
    # Configured with an ExtraLarge Headnode VM
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

Para la SDK, la historia es similar. La creación y aprovisionamiento de un clúster usando el SDK se documenta en [Uso del SDK .NET de HDInsight][]. La configuración de un nodo principal extragrande requiere la adición del parámetro `HeadNodeSize = NodeVMSize.ExtraLarge` al método `ClusterCreateParameters()` usado en este método.

    # Create a new HDInsight cluster with the HDInsight SDK
    # Configured with an ExtraLarge Headnode VM
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

**Referencias**

-   [ZooKeeper][]
-   [Conexión a los clústeres de HDInsight con RDP][]
-   [Uso del SDK .NET de HDInsight][]

  []: http://i.imgur.com/jrUmrH4.png
  [ZooKeeper]: http://zookeeper.apache.org/
  [Conexión a los clústeres de HDInsight con RDP]: ../hdinsight-administer-use-management-portal/#rdp
  [1]: http://i.imgur.com/MYTkCHW.png
  [2]: http://i.imgur.com/eL6jzgB.png
  [Administración de HDInsight con PowerShell]: ../hdinsight-administer-use-powershell/
  [Uso del SDK .NET de HDInsight]: ../hdinsight-provision-clusters/#sdk
