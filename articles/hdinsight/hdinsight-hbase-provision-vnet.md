---
title: "Creación de clústeres de HBase en una red virtual | Microsoft Docs"
description: "Introducción al uso de HBase en HDInsight de Azure Aprenda a crear clústeres de HBase de HDInsight en Red virtual de Azure."
keywords: 
services: hdinsight,virtual-network
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 8de8e446-f818-4e61-8fad-e9d38421e80d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/18/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 62b443f8e2252d913b44ab5582450234e9b76844
ms.openlocfilehash: 81f29f1dc6efa24e7eb23490ac5a0d043b6ca1ad


---
# <a name="create-hbase-clusters-in-azure-virtual-network"></a>Creación de clústeres de HBase en Virtual Network de Azure
Aprenda a crear clústeres de HBase de HDInsight de Azure en una [Azure Virtual Network][1].

Con la integración de red virtual, los clústeres de HBase se pueden implementar en la misma red que sus aplicaciones para que estas puedan comunicarse directamente con HBase. Entre las ventajas se incluye lo siguiente:

* Conectividad directa entre la aplicación web y los nodos del clúster de HBase, lo cual permite la comunicación mediante las API de llamada a procedimiento remoto (RPC) de Java de HBase.
* Rendimiento mejorado gracias a que el tráfico ya no tiene que examinar varias puertas de enlace y equilibradores de carga.
* La posibilidad de procesar información confidencial de una manera segura sin exponer un extremo público.

### <a name="prerequisites"></a>Requisitos previos
Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Una estación de trabajo con Azure PowerShell**. Consulte [Install and use Azure PowerShell (Instalación y uso de Azure PowerShell)](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## <a name="create-hbase-cluster-into-virtual-network"></a>Creación de un clúster de HBase en red virtual
En esta sección, crea un clúster de HBase basado en Linux con la cuenta de Azure Storage dependiente en una red virtual de Azure mediante el uso de una [plantilla de Azure Resource Manager](../resource-group-template-deploy.md). Para conocer otros métodos de creación de clústeres y la descripción de la configuración, consulte [Creación de clústeres de Hadoop basados en Windows en HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Para más información acerca de cómo utilizar la plantilla para crear clústeres de Hadoop en HDInsight, consulte [Creación de clústeres de Hadoop basados en Windows en HDInsight mediante plantillas de Azure Resource Manager](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

> [!NOTE]
> Algunas propiedades se han codificado de forma rígida en la plantilla. Por ejemplo:
>
> * **Ubicación**: este de EE. UU. 2
> * Versión del clúster: 3.4
> * **Número de nodos de trabajo en el clúster**: 4
> * **Nombre de la cuenta de almacenamiento predeterminada**: una cadena única
> * **Nombre de la red virtual**: &lt;Nombre del clúster>-vnet
> * **Espacio de direcciones de red virtual**: 10.0.0.0/16
> * **Nombre de subred**: subnet1
> * **Intervalo de direcciones de subred**: 10.0.0.0/24
>
> &lt;Nombre del clúster> se reemplaza con el nombre de clúster que proporcione al utilizar la plantilla.
>
>

1. Haga clic en la imagen siguiente para abrir la plantilla en el Portal de Azure. La plantilla se encuentra en [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-linux-vnet/).

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-hbase-linux-vnet%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hbase-provision-vnet/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. En la hoja **Implementación personalizada**, escriba lo siguiente:

   * **Suscripción**: seleccione una suscripción de Azure usada para crear el clúster de HDInsight, la cuenta de almacenamiento dependiente y la red virtual de Azure.
   * **Grupo de recursos**: seleccione **Crear nuevo** y especifique un nuevo nombre al grupo de recursos.
   * **Ubicación**: seleccione una ubicación para el grupo de recursos.
   * **ClusterName**: escriba un nombre para el clúster de Hadoop que se va a crear.
   * **Nombre de inicio de sesión y contraseña de clúster**: el nombre de inicio de sesión predeterminado es **admin**.
   * **Nombre de usuario y contraseña de SSH**: el nombre de usuario predeterminado es **sshuser**.  Puede cambiarlo.
   * **Acepto los términos y condiciones indicados anteriormente**: (Seleccionar)
3. Haga clic en **Comprar**. Tarda aproximadamente 20 minutos en crear un clúster. Una vez creado el clúster, puede hacer clic en la hoja del clúster en el portal para abrirlo.

Después de completar el tutorial, quizá desee eliminar el clúster. Con HDInsight, los datos se almacenan en Almacenamiento de Azure, por lo que puede eliminar un clúster de forma segura cuando no está en uso. También se le cargará por un clúster de HDInsight aunque no esté en uso. Como en muchas ocasiones los cargos por el clúster son más que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no estén en uso. Para ver instrucciones sobre cómo eliminar un clúster, consulte [Administración de clústeres de Hadoop en HDInsight mediante Azure Portal](hdinsight-administer-use-management-portal.md#delete-clusters).

Para comenzar a trabajar con el nuevo clúster de HBase, utilice los procedimientos que encontrará en [Introducción al uso de HBase con Hadoop en HDInsight](hdinsight-hbase-tutorial-get-started.md).

## <a name="connect-to-the-hbase-cluster-using-hbase-java-rpc-apis"></a>Conexión al clúster de HBase mediante las API de RPC de Java de HBase
1. Cree una máquina virtual de infraestructura como servicio (IaaS) en la misma red virtual de Azure y la misma subred. Para obtener instrucciones sobre cómo crear una nueva máquina virtual de IaaS consulte el tutorial sobre la [creación de una máquina virtual que ejecuta Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md). Al seguir los pasos descritos en este documento, tiene que utilizar lo siguiente para la configuración de red:

   * **Red virtual**: &lt;Nombre del clúster>-vnet
   * **Subred**: subnet1

   > [!IMPORTANT]
   > Reemplace &lt;Nombre del clúster > con el nombre que utilizó al crear el clúster de HDInsight en los pasos anteriores.
   >
   >

   Con estos valores, se colocará la máquina virtual en la misma red virtual y subred que el clúster de HDInsight. Esta configuración les permitirá comunicarse directamente entre sí. No hay una manera de crear un clúster de HDInsight con un nodo perimetral vacío. El nodo perimetral puede usarse para administrar el clúster.  Para obtener más información, consulte [Uso de nodos perimetrales vacíos en HDInsight](hdinsight-apps-use-edge-node.md).

2. Cuando use una aplicación Java para conectarse a HBase en modo remoto, debe usar el nombre de dominio completo (FQDN). Para determinarlo, debe obtener el sufijo DNS específico de la conexión del clúster de HBase. Para ello, use uno de los siguientes métodos:

   * Utilice un explorador web para realizar una llamada Ambari:

     Vaya a https://&lt;Nombre del clúster>.azurehdinsight.net/api/v1/clusters/&lt;Nombre del clúster>/hosts?minimal_response=true. Convierte un archivo JSON con los sufijos DNS.
   * Utilice el sitio web de Ambari:

     1. Vaya a https://&lt;Nombre del clúster>.azurehdinsight.net.
     2. Haga clic en **Hosts** en el menú superior.
   * Use Curl para realizar llamadas REST:

         curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

     En los datos de notación de objetos JavaScript (JSON) devueltos, busque la entrada "host_name". Esto contiene el nombre de dominio completo (FQDN) de los nodos del clúster. Por ejemplo:

         ...
         "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
         ...

     La parte del nombre de dominio que comienza con el nombre del clúster es el sufijo DNS. Por ejemplo, mycluster.b1.cloudapp.net.
   * Uso de Azure PowerShell

     Use el siguiente script de Azure PowerShell para registrar la función **Get-ClusterDetail** , que se puede usar para devolver el sufijo DNS:

         function Get-ClusterDetail(
             [String]
             [Parameter( Position=0, Mandatory=$true )]
             $ClusterDnsName,
             [String]
             [Parameter( Position=1, Mandatory=$true )]
             $Username,
             [String]
             [Parameter( Position=2, Mandatory=$true )]
             $Password,
             [String]
             [Parameter( Position=3, Mandatory=$true )]
             $PropertyName
             )
         {
         <#
             .SYNOPSIS
              Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
             .Description
              This command shows the following 4 properties of an HDInsight cluster:
              1. ZookeeperQuorum (supports only HBase type cluster)
                 Shows the value of HBase property "hbase.zookeeper.quorum".
              2. ZookeeperClientPort (supports only HBase type cluster)
                 Shows the value of HBase property "hbase.zookeeper.property.clientPort".
              3. HBaseRestServers (supports only HBase type cluster)
                 Shows a list of host FQDNs that run the HBase REST server.
              4. FQDNSuffix (supports all cluster types)
                 Shows the FQDN suffix of hosts in the cluster.
             .EXAMPLE
              Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
              This command shows the value of HBase property "hbase.zookeeper.quorum".
             .EXAMPLE
              Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
              This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
             .EXAMPLE
              Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
              This command shows a list of host FQDNs that run the HBase REST server.
             .EXAMPLE
              Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
              This command shows the FQDN suffix of hosts in the cluster.
         #>

             $DnsSuffix = ".azurehdinsight.net"

             $ClusterFQDN = $ClusterDnsName + $DnsSuffix
             $webclient = new-object System.Net.WebClient
             $webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

             if($PropertyName -eq "ZookeeperQuorum")
             {
                 $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
                 $Response = $webclient.DownloadString($Url)
                 $JsonObject = $Response | ConvertFrom-Json
                 Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
             }
             if($PropertyName -eq "ZookeeperClientPort")
             {
                 $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
                 $Response = $webclient.DownloadString($Url)
                 $JsonObject = $Response | ConvertFrom-Json
                 Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
             }
             if($PropertyName -eq "HBaseRestServers")
             {
                 $Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
                 $Response1 = $webclient.DownloadString($Url1)
                 $JsonObject1 = $Response1 | ConvertFrom-Json
                 $PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'

                 $Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
                 $Response2 = $webclient.DownloadString($Url2)
                 $JsonObject2 = $Response2 | ConvertFrom-Json
                 foreach ($host_component in $JsonObject2.host_components)
                 {
                     $ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
                     Write-host $ConnectionString
                 }
             }
             if($PropertyName -eq "FQDNSuffix")
             {
                 $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/YARN/components/RESOURCEMANAGER"
                 $Response = $webclient.DownloadString($Url)
                 $JsonObject = $Response | ConvertFrom-Json
                 $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                 $pos = $FQDN.IndexOf(".")
                 $Suffix = $FQDN.Substring($pos + 1)
                 Write-host $Suffix
             }
         }

     Después de ejecutar el script de Azure PowerShell, use el siguiente comando para devolver el sufijo DNS usando la función **Get-ClusterDetail** . Especifique el nombre del clúster de HBase de HDInsight, el nombre y la contraseña de administrador cuando use este comando.

         Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

     Este código devolverá el sufijo DNS. Por ejemplo, **yourclustername.b4.internal.cloudapp.net**.
   * Uso de RDP

     Puede usar también Escritorio remoto para conectarse al clúster de HBase (se conectará al nodo principal) y ejecutar **ipconfig** desde el símbolo del sistema para obtener el sufijo DNS. Para obtener instrucciones sobre cómo habilitar el Protocolo de escritorio remoto (RDP) y conectarse al clúster mediante RDP, consulte [Administración de clústeres de Hadoop en HDInsight mediante Azure Portal][hdinsight-admin-portal].

     ![hdinsight.hbase.dns.surffix][img-dns-surffix]

<!--
3.    Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

    To make the configuration change:

    1. RDP into the virtual machine.
    2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
    3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
    - Set **Primary DNS Suffix** to the value obtained in step 2:

        ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
    4. Click **OK**.
    5. Reboot the virtual machine.
-->

Para comprobar que la máquina virtual puede comunicarse con el clúster de HBase, use el comando `ping headnode0.<dns suffix>` desde la máquina virtual. Por ejemplo, haga ping a headnode0.mycluster.b1.cloudapp.net

Para usar esta información en una aplicación Java, puede seguir los pasos que se indican en [Uso de Maven para crear aplicaciones Java que utilicen HBase con HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) para crear una aplicación. Para que la aplicación se conecte a un servidor HBase remoto, modifique el archivo **hbase-site.xml** de este ejemplo para que use el FQDN de ZooKeeper. Por ejemplo:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [!NOTE]
> Para obtener más información sobre la resolución de nombres en redes virtuales de Azure, incluido el uso de su propio servidor DNS, consulte [Resolución de nombres (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).
>
>

## <a name="next-steps"></a>Pasos siguientes
En este tutorial, ha aprendido a crear un clúster de HBase. Para obtener más información, consulte:

* [Introducción a HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Uso de nodos perimetrales vacíos en HDInsight](hdinsight-apps-use-edge-node.md)
* [Configuración de la replicación geográfica de HBase en HDInsight](hdinsight-hbase-replication.md)
* [Consulte Creación de clústeres de Hadoop en HDInsight.](hdinsight-provision-clusters.md)
* [Introducción al uso de HBase con Hadoop en HDInsight](hdinsight-hbase-tutorial-get-started.md)
* [Análisis de opiniones de Twitter con HBase en HDInsight](hdinsight-hbase-analyze-twitter-sentiment.md)
* [Información general sobre redes virtuales][vnet-overview]

[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines/virtual-machines-windows-hero-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: /powershell/azureps-cmdlets-docs


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Detalles de aprovisionamiento para el nuevo clúster de HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Uso de la acción de script para personalizar un clúster de HBase"

[azure-preview-portal]: https://portal.azure.com



<!--HONumber=Dec16_HO3-->


