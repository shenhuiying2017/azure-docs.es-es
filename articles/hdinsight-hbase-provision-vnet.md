<properties urlDisplayName="Provision HBase clusters on Azure Virtual Network" pageTitle="Aprovisionamiento de cl&uacute;steres de HBase en Red virtual de Azure | Azure" metaKeywords="" description="Obtenga informaci&oacute;n sobre c&oacute;mo crear cl&uacute;steres de HDInsight en Red virtual de Azure." metaCanonical="" services="hdinsight" documentationCenter="" title="Aprovisionamiento de cl&uacute;steres de HBase en Red virtual de Azure" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao" />

# Aprovisionamiento de clústeres de HBase en Red virtual de Azure

Obtenga información sobre cómo crear clústeres de HDInsight en Red virtual de Azure.

Con la integración de red virtual, los clústeres de HBase se pueden implementar en la misma red virtual como sus aplicaciones para que estas puedan comunicarse directamente con HBase. Entre las ventajas se incluye lo siguiente:

-   Conectividad directa entre la aplicación web y los nodos del clúster de HBase, que permite la comunicación mediante las API RPC de Java de HBase.
-   Mejora del rendimiento, ya que el tráfico no pasa por varias puertas de enlace y equilibradores de carga.
-   Procesamiento más seguro de la información confidencial sin exponer un extremo público.

## En este artículo

-   [Requisitos previos][Requisitos previos]
-   [Aprovisionamiento de clústeres de HBase en una red virtual][Aprovisionamiento de clústeres de HBase en una red virtual]
-   [Conexión al clúster de HBase aprovisionado en la red virtual mediante las API RPC de Java de HBase][Conexión al clúster de HBase aprovisionado en la red virtual mediante las API RPC de Java de HBase]
-   [Aprovisionamiento de un clúster de HBase usando PowerShell][Aprovisionamiento de un clúster de HBase usando PowerShell]
-   [Pasos siguientes][Pasos siguientes]

## <span id="prerequisites"></span></a>Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

-   **Una suscripción de Azure**. Azure es una plataforma basada en suscripción. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra][Opciones de compra], [Ofertas para miembros][Ofertas para miembros] o [Evaluación gratuita][Evaluación gratuita].

-   **Una estación de trabajo con Azure PowerShell instalado y configurado**. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][Instalación y configuración de Azure PowerShell]. Para ejecutar scripts de PowerShell, debe ejecutar Azure PowerShell como administrador y establecer la directiva de ejecución en *RemoteSigned*. Consulte [Running Windows PowerShell scripts][Running Windows PowerShell scripts].

    Antes de ejecutar scripts de PowerShell, asegúrese de estar conectado a su suscripción de Azure mediante el siguiente cmdlet:

        Add-AzureAccount

    Si tiene varias suscripciones a Azure, utilice el siguiente cmdlet para definir la suscripción actual:

        Select-AzureSubscription <AzureSubscriptionName>

## <span id="hbaseprovision"></span></a>Aprovisionamiento de clústeres de HBase en una red virtual

**Para crear una red virtual usando el portal de administración:**

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].
2.  En la parte inferior de la página, haga clic en **NUEVO**, **SERVICIOS DE RED**, **RED VIRTUAL** y, a continuación, **CREACIÓN RÁPIDA**.
3.  Escriba o seleccione los valores siguientes:

    -   **Name**: El nombre de la red virtual.
    -   **Espacio de direcciones**: elija un espacio de direcciones para la red virtual lo bastante grande para proporcionar direcciones para todos los nodos del clúster. De lo contrario, se producirá un error en el aprovisionamiento.
    -   **Número máximo de VM**: elija uno de los recuentos de VM máximos.
    -   **Ubicación**: la ubicación debe ser la misma que el clúster de HBase que creará.
    -   **Servidor DNS**: en este artículo se usa un servidor DNS interno proporcionado por Azure, de modo que puede elegir **Ninguno**. Asimismo, se admite una configuración de red más avanzada con servidores DNS personalizados. Para obtener instrucciones detalladas, consulte <http://msdn.microsoft.com/library/azure/jj156088.aspx>.

4.  Haga clic en **CREAR UNA RED VIRTUAL**. El nombre de la nueva red virtual aparecerá en la lista. Espere hasta que aparezca **Creado** en la columna de estado.
5.  En el panel principal, haga clic en la red virtual que acaba de crear.
6.  En la parte superior de la página, haga clic en **PANEL**.
7.  En **Vista rápida**, anote el **ID. DE RED VIRTUAL**. Lo necesitará al aprovisionar el clúster de HBase.
8.  En la parte superior de la página, haga clic en **CONFIGURAR**.
9.  En la parte inferior de la página, el nombre de subred predeterminado es **Subred-1**. Opcionalmente, puede cambiar el nombre de la subred o agregar una nueva subred para el clúster de HBase. Anote el nombre de subred, lo necesitará al aprovisionar el clúster.
10. Compruebe **CIDR(RECUENTO DE DIRECCIONES)** para la subred que se usará para el clúster. El recuento de direcciones debe ser mayor que el número de nodos de trabajo más siete (puerta de enlace: 2, nodo principal: 2, Zookeeper: 3). Por ejemplo, si necesita un clúster de HBase de 10 nodos, el recuento de direcciones para la subred debe ser mayor que 17 (10+7). De lo contrario, se producirá un error en la implementación.

    > [WACOM.NOTE] Se recomienda encarecidamente designar una sola subred para un clúster.

11. Haga clic en **Guardar** en la parte inferior de la página, si ha actualizado los valores de subred.

> [WACOM.NOTE] Los clústeres de HDInsight usan el almacenamiento de blobs de Azure para almacenar datos. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con Hadoop en HDInsight][Uso del almacenamiento de blobs de Azure con Hadoop en HDInsight]. Necesitará una cuenta de almacenamiento y un contenedor de almacenamiento de blobs. La ubicación de la cuenta de almacenamiento debe coincidir con la ubicación de la red virtual y del clúster.

**Para crear una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs:**

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].
2.  Haga clic en **NEW** en la esquina inferior izquierda, seleccione **SERVICIOS DE DATOS**, **ALMACENAMIENTO** y, a continuación, haga clic en **CREACIÓN RÁPIDA**.

3.  Escriba o seleccione los valores siguientes:

    -   **URL**: el nombre de la cuenta de almacenamiento.
    -   **UBICACIÓN**: la ubicación de la cuenta de almacenamiento. Asegúrese de que coincida con la ubicación de la red virtual. No se admiten grupos de afinidad.
    -   **REPLICACIÓN**: con fines de evaluación, use **Localmente redundante** para reducir el coste.

4.  Haga clic en **CREATE STORAGE ACCOUNT**. La nueva cuenta de almacenamiento aparecerá en la lista de almacenamiento.
5.  Espere hasta que la característica de **estado** de la nueva cuenta de almacenamiento cambie a **En línea**.
6.  Haga clic en la nueva cuenta de almacenamiento en la lista para seleccionarla.
7.  Haga clic en **MANAGE ACCESS KEYS** en la parte inferior de la página.
8.  Tome nota de los valores de los campos **NOMBRE DE CUENTA DE ALMACENAMIENTO** y **CLAVE DE ACCESO PRIMARIA** (o **CLAVE DE ACCESO SECUNDARIA**. Cualquiera de las claves funciona). Los necesitará más adelante en el tutorial.
9.  Desde la parte superior de la página, haga clic en **CONTENEDOR**.
10. Desde la parte inferior de la página, haga clic en **AGREGAR**.
11. Escriba el nombre del contenedor. Este contenedor se usará como contenedor predeterminado del clúster de HBase. De forma predeterminada, el nombre del contenedor predeterminado coincide con el nombre del clúster. Conserve el campo **ACCESO** como **Privado**.
12. Haga clic en el icono de verificación para crear el contenedor.

**Para aprovisionar un clúster de HBase usando el portal de Azure:**

> [WACOM.NOTE] Para obtener más información acerca del aprovisionamiento de un clúster de HBase nuevo usando PowerShell, consulte[Aprovisionamiento de un clúster de HBase usando PowerShell][Aprovisionamiento de un clúster de HBase usando PowerShell].

1.  Inicie sesión en el [Portal de administración de Azure][Portal de administración de Azure].
2.  Haga clic en **NUEVO** en la esquina inferior izquierda, elija **SERVICIOS DE DATOS**, **HDINSIGHT** y haga clic en **CREACIÓN PERSONALIZADA**.
3.  Escriba un **NOMBRE DE CLÚSTER** y elija la **VERSIÓN DE HDINSIGHT** que usará para el clúster.

    ![Campos de nombre del clúster y versión][Campos de nombre del clúster y versión]

4.  Seleccione el número de **NODOS DE DATOS** que deben crearse para el clúster y la región o red virtual de Azure (**REGIÓN/RED VIRTUAL**) que se usará para este clúster.

    ![Campos de número de nodos y región][Campos de número de nodos y región]

5.  Escriba el **NOMBRE DE USUARIO** y la **CONTRASEÑA** de administrador que se usarán con este clúster.

    ![Campos de nombre y contraseña de administrador][Campos de nombre y contraseña de administrador]

6.  Seleccione si se usará una cuenta de almacenamiento nueva o una existente. Si se usará una nueva, escriba el **NOMBRE DE CUENTA** y el **CONTENEDOR PREDETERMINADO** para crearla. Finalmente, seleccione la marca de verificación para crear el clúster.

    ![Selección de una cuenta de almacenamiento][Selección de una cuenta de almacenamiento]

Para comenzar a trabajar con su nuevo clúster de HBase, utilice los procedimientos que encontrará en [Introducción al uso de HBase con Hadoop en HDInsight][Introducción al uso de HBase con Hadoop en HDInsight].

## <span id="connect"></span></a>Conexión al clúster de HBase aprovisionado en la red virtual mediante las API RPC de Java de HBase

1.  Aprovisiona una máquina virtual IaaS en la misma red virtual de Azure y la misma subred. De este modo, tanto la máquina virtual como el clúster de HBase usan el mismo servidor DNS interno para resolver nombres de host. Para ello, debe elegir la opción de desde la galería y seleccionar la red virtual en lugar de un centro de datos. Para obtener las instrucciones, consulte [Creación de una máquina virtual que ejecuta Windows Server][Creación de una máquina virtual que ejecuta Windows Server]. Una imagen de Windows Server 2012 estándar con una VM pequeña es suficiente.

2.  Cuando use una aplicación Java para conectarse a HBase en modo remoto, debe usar el nombre de dominio completo (FQDN). Para determinarlo, debe obtener el sufijo DNS específico de la conexión del clúster de HBase. Para ello, use Curl para consultar Ambari o Escritorio remoto para conectarse al clúster.

    -   **Curl** - use el comando siguiente:

            curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

        En los datos de JSON devueltos, busque la entrada "host\_name". Esta entrada contendrá el nombre de dominio completo (FQDN) de los nodos del clúster. Por ejemplo:

            ...
            "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
            ...

        La parte del nombre de dominio que comienza con el nombre del clúster es el sufijo DNS. Por ejemplo, mycluster.b1.cloudapp.net.

    -   **PowerShell** - use el siguiente script de PowerShell para registrar la función **Get-ClusterDetail**, que se puede usar para devolver el sufijo DNS.

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
                 Displays information to facilitate HDInsight cluster to cluster scinario within same virtual network.
                .Description
                 This command shows following 4 properties of an HDInsight cluster.
                 1. ZookeeperQuorum (only support HBase type cluster)
                    Shows the value of hbase property "hbase.zookeeper.quorum".
                 2. ZookeeperClientPort (only support HBase type cluster)
                    Shows the value of hbase property "hbase.zookeeper.property.clientPort".
                 3. HBaseRestServers (only support HBase type cluster)
                    Shows a list of host FQDNs which run HBase rest server.
                 4. FQDNSuffix (support all type cluster)
                    Shows FQDN suffix of hosts in the cluster.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
                 This command shows the value of hbase property "hbase.zookeeper.quorum".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
                 This command shows the value of hbase property "hbase.zookeeper.property.clientPort".
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
                 This command shows a list of host FQDNs which run HBase rest server.
                .EXAMPLE
                 Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
                 This command shows FQDN suffix of hosts in the cluster.
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
                    $Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
                    $Response = $webclient.DownloadString($Url)
                    $JsonObject = $Response | ConvertFrom-Json
                    $FQDN = $JsonObject.host_components[0].HostRoles.host_name
                    $pos = $FQDN.IndexOf(".")
                    $Suffix = $FQDN.Substring($pos + 1)
                    Write-host $Suffix
                }
            }

        Después de ejecutar el script de PowerShell, use el siguiente comando para devolver el sufijo DNS usando la función Get-ClusterDetail. Especifique el nombre del clúster de HBase de HDInsight, el nombre y la contraseña de administrador cuando use este comando.

            Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

        Este código devolverá el sufijo DNS. Por ejemplo, **yourclustername.b4.internal.cloudapp.net**.

    > [WACOM.NOTE] Puede usar también Escritorio remoto para conectarse al clúster de HBase (se conectará al nodo principal) y ejecutar **ipconfig** desde la línea de comandos para obtener el sufijo DNS. Para obtener instrucciones sobre cómo habilitar RDP y conectarse al clúster usando RDP, consulte [Administración de clústeres de Hadoop en HDInsight mediante el Portal de administración de Azure][Administración de clústeres de Hadoop en HDInsight mediante el Portal de administración de Azure].
    >
    > ![hdinsight.hbase.dns.surffix][hdinsight.hbase.dns.surffix]

<!--  3.  Change the Primary DNS Suffix configuration of the virtual machine. This enables virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to the workernode0 of the HBase cluster.       To make the configuration change:      1. RDP into the virtual machine.      2. Open **Local Group Policy Editor**. The executable is gpedit.msc.     3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.      - Set **Primary DNS Suffix** to the value obtained in the step 2:           ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]     4. Click **OK**.      5. Reboot the virtual machine. -->

Para comprobar que la máquina virtual puede comunicarse con el clúster de HBase, use el comando `ping headnode0.<dns suffix>` desde la máquina virtual. Por ejemplo, ping headnode0.mycluster.b1.cloudapp.net

Para usar esta información en una aplicación Java, puede seguir los pasos que se indican en [Uso de Maven para crear aplicaciones Java que utilicen HBase con HDInsight (Hadoop)][Uso de Maven para crear aplicaciones Java que utilicen HBase con HDInsight (Hadoop)] para crear una aplicación. Para que la aplicación se conecte a un servidor HBase remoto, modifique el archivo **hbase-site.xml** de este ejemplo para que use el FQDN de ZooKeeper. Por ejemplo:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [WACOM.NOTE] Para obtener más información sobre la resolución de nombres en redes virtuales de Azure, incluido el uso de su propio servidor DNS, consulte [Resolución de nombres (DNS)][Resolución de nombres (DNS)].

## <span id="powershell"></span></a>Aprovisionamiento de un clúster de HBase usando Azure PowerShell:\*\*

1.  Abra PowerShell ISE.
2.  Copie y pegue la siguiente copia en el panel de scripts.

        $hbaseClusterName = "<HBaseClusterName>"
        $hadoopUserName = "<HBaseClusterUsername>"
        $hadoopUserPassword = "<HBaseClusterUserPassword>"
        $location = "<HBaseClusterLocation>"  #i.e. "West US"
        $clusterSize = <HBaseClusterSize>  
        $vnetID = "<AzureVirtualNetworkID>"
        $subNetName = "<AzureVirtualNetworkSubNetName>"
        $storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
        $storageAccountKey = "<AzureStorageAccountKey>"
        $storageContainerName = "<AzureBlobStorageContainer>"

        $password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
        $creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password) 

        New-AzureHDInsightCluster -Name $hbaseClusterName `
                                  -ClusterType HBase `
                                  -Version 3.1 `
                                  -Location $location `
                                  -ClusterSizeInNodes $clusterSize `
                                  -Credential $creds `
                                  -VirtualNetworkId $vnetID `
                                  -SubnetName $subNetName `
                                  -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
                                  -DefaultStorageAccountKey $storageAccountKey `
                                  -DefaultStorageContainerName $storageContainerName

3.  Haga clic en **Ejecutar script** o presione **F5**.
4.  Para validar el clúster, puede comprobar el clúster del portal de administración o ejecutar el siguiente cmdlet de PowerShell en el panel inferior:

    Get-AzureHDInsightCluster

## <span id="nextsteps"></span></a>Pasos siguientes

En este tutorial, hemos aprendido a aprovisionar un clúster de HBase. Para obtener más información, consulte:

-   [Introducción a HDInsight][Introducción a HDInsight]
-   [Aprovisionamiento de clústeres de Hadoop en HDInsight][Aprovisionamiento de clústeres de Hadoop en HDInsight]
-   [Introducción al uso de HBase con Hadoop en HDInsight][Introducción al uso de HBase con Hadoop en HDInsight]
-   [Análisis del espíritu de Twitter con HBase en HDInsight][Análisis del espíritu de Twitter con HBase en HDInsight]
-   [Información general sobre redes virtuales][Información general sobre redes virtuales].

  [Requisitos previos]: #prerequisites
  [Aprovisionamiento de clústeres de HBase en una red virtual]: #hbaseprovision
  [Conexión al clúster de HBase aprovisionado en la red virtual mediante las API RPC de Java de HBase]: #connect
  [Aprovisionamiento de un clúster de HBase usando PowerShell]: #powershell
  [Pasos siguientes]: #nextsteps
  [Opciones de compra]: http://azure.microsoft.com/es-es/pricing/purchase-options/
  [Ofertas para miembros]: http://azure.microsoft.com/es-es/pricing/member-offers/
  [Evaluación gratuita]: http://azure.microsoft.com/es-es/pricing/free-trial/
  [Instalación y configuración de Azure PowerShell]: ../install-configure-powershell
  [Running Windows PowerShell scripts]: http://technet.microsoft.com/es-es/library/ee176949.aspx
  [Portal de administración de Azure]: https://manage.windowsazure.com
  [Uso del almacenamiento de blobs de Azure con Hadoop en HDInsight]: ../hdinsight-use-blob-storage/
  [Campos de nombre del clúster y versión]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png
  [Campos de número de nodos y región]: ./media/hdinsight-hbase-provision-vnet/hbasewizard2.png
  [Campos de nombre y contraseña de administrador]: ./media/hdinsight-hbase-provision-vnet/hbasewizard3.png
  [Selección de una cuenta de almacenamiento]: ./media/hdinsight-hbase-provision-vnet/hbasewizard4.png
  [Introducción al uso de HBase con Hadoop en HDInsight]: ../hdinsight-hbase-get-started/
  [Creación de una máquina virtual que ejecuta Windows Server]: ../virtual-machines-windows-tutorial/
  [Administración de clústeres de Hadoop en HDInsight mediante el Portal de administración de Azure]: ../hdinsight-administer-use-management-portal/#rdp
  [hdinsight.hbase.dns.surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
  [Uso de Maven para crear aplicaciones Java que utilicen HBase con HDInsight (Hadoop)]: azure.microsoft.com/es-es/documentation/articles/hdinsight-hbase-build-java-maven/
  [Resolución de nombres (DNS)]: http://msdn.microsoft.com/es-es/library/azure/jj156088.aspx
  [Introducción a HDInsight]: ../hdinsight-get-started/
  [Aprovisionamiento de clústeres de Hadoop en HDInsight]: ../hdinsight-provision-clusters/
  [Análisis del espíritu de Twitter con HBase en HDInsight]: ../hdinsight-hbase-twitter-sentiment/
  [Información general sobre redes virtuales]: http://msdn.microsoft.com/library/azure/jj156007.aspx
