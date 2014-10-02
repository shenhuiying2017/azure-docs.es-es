<properties linkid="manage-services-hdinsight-hbase-provision-on-vnet" urlDisplayName="Provision HBase clusters on Azure Virtual Network" pageTitle="Provision HBase clusters on Azure Virtual Network | Azure" metaKeywords="" description="Learn how to create HDInsight clusters on Azure Virtual Network." metaCanonical="" services="hdinsight" documentationCenter="" title="Provision HBase clusters on Azure Virtual Network" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao"></tags>

# Aprovisionamiento de clústeres de HBase en Red virtual de Azure

Obtenga información sobre cómo crear clústeres de HDInsight en Red virtual de Azure.

Con la integración de red virtual, los clústeres de HBase se pueden implementar en la misma red virtual como sus aplicaciones para que estas puedan comunicarse directamente con HBase. Entre las ventajas se incluye lo siguiente:

-   Conectividad directa entre la aplicación web y los nodos del clúster de HBase, que permite la comunicación mediante las API RPC de Java de HBase.
-   Mejora del rendimiento, ya que el tráfico no pasa por varias puertas de enlace y equilibradores de carga.
-   Procesamiento más seguro de la información confidencial sin exponer un extremo público.

> [WACOM.NOTE]En la actualidad, solo los clústeres de HBase pueden aprovisionarse en redes virtuales de Azure. No se admiten los clústeres de Hadoop.

## En este artículo

-   [Requisitos previos][]
-   [Aprovisionamiento de clústeres de HBase en una red virtual][]
-   [Conexión al clúster de HBase aprovisionado en la red virtual mediante las API RPC de Java de HBase][]
-   [Pasos siguientes][]

## <span id="prerequisites"></span></a>Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

-   **Una suscripción de Azure**. Azure es una plataforma basada en suscripción. Para obtener más información acerca de cómo obtener una suscripción, consulte [Opciones de compra][], [Ofertas para miembros][] o [Evaluación gratuita][].

-   **Una estación de trabajo con Azure PowerShell instalado y configurado**. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell][]. Para ejecutar scripts de PowerShell, debe ejecutar Azure PowerShell como administrador y establecer la directiva de ejecución en *RemoteSigned*. Consulte [Running Windows PowerShell scripts][].

    Antes de ejecutar scripts de PowerShell, asegúrese de estar conectado a su suscripción de Azure mediante el siguiente cmdlet:

        Add-AzureAccount

    Si tiene varias suscripciones a Azure, utilice el siguiente cmdlet para definir la suscripción actual:

        Select-AzureSubscription <AzureSubscriptionName>

## <span id="hbaseprovision"></span></a>Aprovisionamiento de clústeres de HBase en una red virtual

**Para crear una red virtual usando el portal de administración:**

1.  Inicie sesión en el [Portal de administración de Azure][].
2.  En la parte inferior de la página, haga clic en **NUEVO**, **SERVICIOS DE RED**, **RED VIRTUAL** y, a continuación, **CREACIÓN RÁPIDA**.
3.  Escriba o seleccione los valores siguientes:

    -   **Name**: El nombre de la red virtual.
    -   **Espacio de direcciones**: elija un espacio de direcciones para la red virtual lo bastante grande para proporcionar direcciones para todos los nodos del clúster. De lo contrario, se producirá un error en el aprovisionamiento.
    -   **Número máximo de VM**: elija uno de los recuentos de VM máximos.
    -   **Ubicación**: la ubicación debe ser la misma que el clúster de HBase que creará.
    -   **Servidor DNS**: en este artículo se usa un servidor DNS interno proporcionado por Azure, de modo que puede elegir **Ninguno**. Asimismo, se admite una configuración de red más avanzada con servidores DNS personalizados. Para obtener instrucciones detalladas, consulte [][]<http://msdn.microsoft.com/library/azure/jj156088.aspx></a>.

4.  Haga clic en **CREAR UNA RED VIRTUAL**. El nombre de la nueva red virtual aparecerá en la lista. Espere hasta que aparezca **Creado** en la columna de estado.
5.  En el panel principal, haga clic en la red virtual que acaba de crear.
6.  En la parte superior de la página, haga clic en **PANEL**.
7.  En **Vista rápida**, anote el **ID. DE RED VIRTUAL**. Lo necesitará al aprovisionar el clúster de HBase.
8.  En la parte superior de la página, haga clic en **CONFIGURAR**.
9.  En la parte inferior de la página, el nombre de subred predeterminado es **Subred-1**. Opcionalmente, puede cambiar el nombre de la subred o agregar una nueva subred para el clúster de HBase. Anote el nombre de subred, lo necesitará al aprovisionar el clúster.
10. Compruebe **CIDR(RECUENTO DE DIRECCIONES)** para la subred que se usará para el clúster. El recuento de direcciones debe ser mayor que el número de nodos de trabajo más siete (puerta de enlace: 2, nodo principal: 2, Zookeeper: 3). Por ejemplo, si necesita un clúster de HBase de 10 nodos, el recuento de direcciones para la subred debe ser mayor que 17 (10+7). De lo contrario, se producirá un error en la implementación.

    > [WACOM.NOTE] Se recomienda encarecidamente designar una sola subred para un clúster.

11. Haga clic en **Guardar** en la parte inferior de la página, si ha actualizado los valores de subred.

> [WACOM.NOTE] Los clústeres de HDInsight usan el almacenamiento de blobs de Azure para almacenar datos. Para obtener más información, consulte [Uso del almacenamiento de blobs de Azure con Hadoop en HDInsight][]. Necesitará una cuenta de almacenamiento y un contenedor de almacenamiento de blobs. La ubicación de la cuenta de almacenamiento debe coincidir con la ubicación de la red virtual y del clúster.

**Para crear una cuenta de almacenamiento de Azure y un contenedor de almacenamiento de blobs:**

1.  Inicie sesión en el [Portal de administración de Azure][].
2.  Haga clic en **NEW** en la esquina inferior izquierda, seleccione **DATA SERVICES**, **STORAGE** y, a continuación, haga clic en **QUICK CREATE**.

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

**Para aprovisionar un clúster de HBase usando Azure PowerShell:**

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

Para probar el nuevo clúster de HBase, puede usar los procedimientos encontrados en [Introducción al uso de HBase con Hadoop en HDInsight][].

## <span id="connect"></span></a>Conexión al clúster de HBase aprovisionado en la red virtual mediante las API RPC de Java de HBase

1.  Aprovisiona una máquina virtual IaaS en la misma red virtual de Azure y la misma subred. De este modo, tanto la máquina virtual como el clúster de HBase usan el mismo servidor DNS interno para resolver nombres de host. Para ello, debe elegir la opción de desde la galería y seleccionar la red virtual en lugar de un centro de datos. Para obtener las instrucciones, consulte [Creación de una máquina virtual que ejecuta Windows Server][]. Una imagen de Windows Server 2012 estándar con una VM pequeña es suficiente.

2.  Obtenga el sufijo DNS específico de la conexión del clúster de HBase. Para hacer RDP en el clúster de HBase (se conectará al nodo principal) y ejecutar **ipconfig** desde un símbolo del sistema. Para obtener instrucciones sobre cómo habilitar RDP y conectarse al clúster usando RDP, consulte [Administración de clústeres de Hadoop en HDInsight mediante el Portal de administración de Azure][].

    ![hdinsight.hbase.dns.surffix][]

3.  Cambie la configuración del sufijo DNS principal de la máquina virtual. Esto permite que la máquina virtual resuelva automáticamente el nombre de host del clúster de HBase sin la especificación explícita del sufijo. Por ejemplo, el nombre de host *workernode0* se resolverá correctamente en workernode0 del clúster de HBase.
    Para cambiar la configuración:

    1.  RDP en la máquina virtual.
    2.  Abra **Editor de directivas de grupo local**. gpedit.msc se puede ejecutar.
    3.  Expanda **Configuración del equipo**, **Plantillas administrativas**, **Red** y, a continuación, haga clic en **Cliente DNS**.
    4.  Establezca **Sufijo DNS primario** en el valor obtenido en el paso 2:

        ![hdinsight.hbase.primary.dns.suffix][]

    1.  Haga clic en **OK**.
    2.  Reinicio de la máquina virtual.

    Ahora, la máquina virtual está lista para comunicarse con el clúster de HBase. Para probar la conexión, ejecute "ping headnode0" en la máquina virtual.

## <span id="nextsteps"></span></a>Pasos siguientes

En este tutorial, hemos aprendido a aprovisionar un clúster de HBase. Para obtener más información, consulte:

-   [Introducción a HDInsight][]
-   [Aprovisionamiento de clústeres de Hadoop en HDInsight][]
-   [Introducción al uso de HBase con Hadoop en HDInsight][]
-   [Análisis del espíritu de Twitter con HBase en HDInsight][]
-   [Información general sobre redes virtuales][].

  [Requisitos previos]: #prerequisites
  [Aprovisionamiento de clústeres de HBase en una red virtual]: #hbaseprovision
  [Conexión al clúster de HBase aprovisionado en la red virtual mediante las API RPC de Java de HBase]: #connect
  [Pasos siguientes]: #nextsteps
  [Opciones de compra]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [Ofertas para miembros]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [Evaluación gratuita]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Instalación y configuración de Azure PowerShell]: ../install-configure-powershell
  [Running Windows PowerShell scripts]: http://technet.microsoft.com/en-us/library/ee176949.aspx
  [Portal de administración de Azure]: https://manage.windowsazure.com
  []: http://msdn.microsoft.com/library/azure/jj156088.aspx
  [Uso del almacenamiento de blobs de Azure con Hadoop en HDInsight]: ../hdinsight-use-blob-storage/
  [Introducción al uso de HBase con Hadoop en HDInsight]: ../hdinsight-hbase-get-started/
  [Creación de una máquina virtual que ejecuta Windows Server]: ../virtual-machines-windows-tutorial/
  [Administración de clústeres de Hadoop en HDInsight mediante el Portal de administración de Azure]: ../hdinsight-administer-use-management-portal/#rdp
  [hdinsight.hbase.dns.surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
  [hdinsight.hbase.primary.dns.suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
  [Introducción a HDInsight]: ../hdinsight-get-started/
  [Aprovisionamiento de clústeres de Hadoop en HDInsight]: ../hdinsight-provision-clusters/
  [Análisis del espíritu de Twitter con HBase en HDInsight]: ../hdinsight-hbase-twitter-sentiment/
  [Información general sobre redes virtuales]: http://msdn.microsoft.com/library/azure/jj156007.aspx
