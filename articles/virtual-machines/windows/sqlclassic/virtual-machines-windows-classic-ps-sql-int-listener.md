---
title: "Configuración de un agente de escucha ILB para grupos de disponibilidad AlwaysOn | Microsoft Docs"
description: "En este tutorial se usan los recursos creados con el modelo de implementación clásica y se crea un agente de escucha de grupo de disponibilidad AlwaysOn en Azure con un equilibrador de carga interno (ILB)."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 291288a0-740b-4cfa-af62-053218beba77
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/01/2017
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 0c23ee550d8ac88994e8c7c54a33d348ffc24372
ms.openlocfilehash: 8e59988f24748a82d4e143295bab9bdaa65cf8e4
ms.lasthandoff: 01/11/2017


---
# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Configuración de un agente de escucha con ILB para grupos de disponibilidad AlwaysOn en Azure
> [!div class="op_single_selector"]
> * [Agente de escucha interno](virtual-machines-windows-classic-ps-sql-int-listener.md)
> * [Agente de escucha externo](virtual-machines-windows-classic-ps-sql-ext-listener.md)
> 
> 

## <a name="overview"></a>Información general
En este tema se muestra cómo configurar un agente de escucha para un grupo de disponibilidad AlwaysOn mediante un **Equilibrador de carga interno (ILB)**.

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.

Para configurar un agente de escucha ILB para un grupo de disponibilidad AlwaysOn en el modelo de Resource Manager, consulte [Configuración de un equilibrador de carga interno para un grupo de disponibilidad AlwaysOn de Azure](../sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md).

El grupo de disponibilidad puede contener réplicas que son solo locales, solo de Azure o abarcan ambas, locales y de Azure, para configuraciones híbridas. Las réplicas de Azure pueden residir en la misma región o en varias regiones mediante varias redes virtuales (VNet). En los pasos siguientes se supone que ya tiene [configurado un grupo de disponibilidad](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) pero no un agente de escucha.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Instrucciones y limitaciones de los agentes de escucha internos
Tenga en cuenta las siguientes instrucciones acerca del agente de escucha del grupo de disponibilidad en Azure mediante ILB:

* El agente de escucha del grupo de disponibilidad es compatible con Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2.
* Solo se admite un agente de escucha de grupo de disponibilidad interno para cada servicio en la nube, ya que el agente de escucha se configura según el ILB y solo hay un ILB por cada servicio en la nube. Sin embargo, es posible crear varios agentes de escucha externos. Para más información, consulte [Configuración de un agente de escucha externo para grupos de disponibilidad AlwaysOn en Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

## <a name="determine-the-accessibility-of-the-listener"></a>Determinar la accesibilidad del agente de escucha
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artículo se centra en la creación de un agente de escucha que usa un **equilibrador de carga interno (ILB)**. Si necesita un agente de escucha público o externo, consulte la versión de este artículo que indica los pasos necesarios para configurar un [agente de escucha externo](virtual-machines-windows-classic-ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Creación de extremos de máquina virtual de carga equilibrada con Direct Server Return
En ILB, debe crear primero el equilibrador de carga interno. Esto se hace en el script siguiente.

Tienes que crear un extremo de carga equilibrada para cada máquina virtual que hospeda una réplica de Azure. Si tienes réplicas en varias regiones, cada réplica de esa región tiene que estar en el mismo servicio en la nube de la misma red virtual. La creación de réplicas de grupo de disponibilidad que abarcan varias regiones de Azure, requiere configurar varias redes virtuales. Para más información sobre cómo configurar la conectividad entre redes virtuales, consulte [Configuración de una conexión de red virtual a red virtual para el modelo de implementación clásico](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md).

1. En el portal de Azure, navega a todas las máquinas virtuales que hospedas una réplica y consulta los detalles.
2. Haz clic en la pestaña **Extremos** para cada una de las máquinas virtuales.
3. Compruebe que el **nombre** y el **puerto público** del punto de conexión del agente de escucha que desea utilizar no están ya en uso. En el ejemplo siguiente, el nombre es "MyEndpoint" y el puerto es "1433".
4. En el cliente local, descarga e instala el [último módulo de PowerShell](https://azure.microsoft.com/downloads/).
5. Inicie **Azure PowerShell**. Se abrirá una nueva sesión de PowerShell con los módulos de administración de Azure cargados.
6. Ejecuta **Get-AzurePublishSettingsFile**. Este cmdlet te dirige a un explorador para descargar un archivo de configuración de publicación en un directorio local. Puede que tengas que escribir las credenciales de inicio de sesión de tu suscripción a Azure.
7. Ejecuta el comando **Import-AzurePublishSettingsFile** con la ruta de acceso del archivo de configuración de publicación que descargaste:
   
        Import-AzurePublishSettingsFile -PublishSettingsFile <PublishSettingsFilePath>
   
    Una vez que el archivo de configuración de publicación se haya importado, puedes administrar tu suscripción a Azure en la sesión de PowerShell.
    
1. En el **ILB**, debe asignar una dirección IP estática. En primer lugar, examine la configuración actual de la red virtual ejecutando el comando siguiente:
   
        (Get-AzureVNetConfig).XMLConfiguration
2. Tome nota del nombre de la **Subred** que contenga las máquinas virtuales que hospeden las réplicas. Este se usará en el parámetro **$SubnetName** en el script.
3. A continuación, en la subred que contiene las máquinas virtuales que hospedan las réplicas, tome nota del nombre del elemento **VirtualNetworkSite** y del elemento **AddressPrefix** inicial. Busque una dirección IP disponible pasando ambos valores al comando **Test-AzureStaticVNetIP** y examinando el parámetro** AvailableAddresses**. Por ejemplo, si el nombre de la red virtual fuera*MyVNet* y tuviera un intervalo de direcciones de subred que empezase por *172.16.0.128*, el siguiente comando mostraría las direcciones disponibles:
   
        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses
4. Elija una de las direcciones disponibles y úsela en el parámetro **$ILBStaticIP** del script siguiente.
5. Copie el siguiente script de PowerShell en un editor de texto y establezca los valores de variable que se ajusten a su entorno (observe que se han proporcionado los valores predeterminados para algunos parámetros). Tenga en cuenta que las implementaciones existentes que usan grupos de afinidad no pueden agregar ILB. Para obtener más información sobre requisitos de ILB, vea [Equilibrador de carga interno](../../../load-balancer/load-balancer-internal-overview.md). Además, si el grupo de disponibilidad abarca regiones de Azure, debe ejecutar el script una vez en cada centro de datos del servicio en la nube y los nodos que residen en ese centro de datos.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value
   
        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP
   
        # Configure a load balanced endpoint for each node in $AGNodes using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }
6. Una vez configuradas las variables, copie el script del editor de texto en la sesión de Azure PowerShell para ejecutarlo. Si el mensaje todavía muestra >>, escriba ENTER de nuevo para asegurarse de que el script comienza a ejecutarse. Nota

> [!NOTE]
> El Portal de Azure clásico no es compatible en este momento con el equilibrador de carga interno, por lo que no verá el ILB o los puntos de conexión en el Portal de Azure clásico. Sin embargo, el parámetro **Get-AzureEndpoint** devuelve una dirección IP interna si el equilibrador de carga se está ejecutando en ella. De lo contrario, devuelve null.
> 
> 

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Comprobación de que KB2854082 está instalado si es necesario.
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Apertura de los puertos de firewall en los nodos de grupo de disponibilidad
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Creación del agente de escucha de grupo de disponibilidad

Cree el agente de escucha de grupo de disponibilidad en dos pasos. En primer lugar, cree el recurso de clúster del punto de acceso cliente y configure las dependencias. En segundo lugar, configure los recursos de clúster con PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Creación del punto de acceso cliente y configuración de las dependencias de clúster
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Configuración de los recursos de clúster en PowerShell
1. En ILB, debe usar la dirección IP del equilibrador de carga interno (ILB) creado anteriormente. Use el script siguiente para obtener esta dirección IP en PowerShell.
   
        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress
2. En una de las máquinas virtuales, copie el script de PowerShell correspondiente a su sistema operativo en un editor de texto y establezca las variables en los valores que anotó anteriormente.
   
    Para Windows Server 2012 o superior, use el siguiente script:
   
        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)
   
        Import-Module FailoverClusters
   
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   
    Para Windows Server 2008 R2 o superior, use el siguiente script:
   
        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)
   
        Import-Module FailoverClusters
   
        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255
3. Una vez establecidas las variables, abra una ventana de Windows PowerShell con privilegios elevados, copie el script del editor de texto y péguelo en la sesión de Azure PowerShell para ejecutarlo. Si el mensaje todavía muestra >>, escriba ENTER de nuevo para asegurarse de que el script comienza a ejecutarse.
4. Repita este paso en cada máquina virtual. Este script configura el recurso de dirección IP con la dirección IP del servicio en la nube y establece otros parámetros como, por ejemplo, el puerto de sondeo. El recurso de dirección IP, una vez conectado, puede responder al sondeo en el puerto de sondeo del extremo con equilibrio de carga que se creó anteriormente en este tutorial.

## <a name="bring-the-listener-online"></a>Conexión del agente de escucha
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Elementos de seguimiento
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Prueba del agente de escucha del grupo de disponibilidad (dentro de la misma red virtual)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]


