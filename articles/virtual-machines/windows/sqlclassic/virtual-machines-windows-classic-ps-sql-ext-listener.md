---
title: "Configuración de un agente de escucha externo para grupos de disponibilidad AlwaysOn | Microsoft Docs"
description: "Este tutorial le guiará a través de los pasos necesarios para crear un agente de escucha de grupo de disponibilidad AlwaysOn en Azure que sea accesible desde el exterior usando la dirección IP virtual pública del servicio en la nube asociado."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/31/2017
ms.author: mikeray
ms.openlocfilehash: 8e506be42aea4fb3c48c29b771a78dcf694f4518
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Configuración de un agente de escucha externo para grupos de disponibilidad AlwaysOn en Azure
> [!div class="op_single_selector"]
> * [Agente de escucha interno](../classic/ps-sql-int-listener.md)
> * [Agente de escucha externo](../classic/ps-sql-ext-listener.md)
> 
> 

En este tema se muestra cómo configurar un agente de escucha para un grupo de disponibilidad AlwaysOn al que se tiene acceso externo en Internet. Esto se realiza asociando la dirección **IP Virtual pública (VIP)** del servicio en la nube al agente de escucha.

> [!IMPORTANT] 
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../../../azure-resource-manager/resource-manager-deployment-model.md). En este artículo se trata el modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos.

El grupo de disponibilidad puede contener réplicas que son solo locales, solo de Azure o abarcan ambas, locales y de Azure, para configuraciones híbridas. Las réplicas de Azure pueden residir en la misma región o en varias regiones mediante varias redes virtuales (VNet). En los pasos siguientes se supone que ya tiene [configurado un grupo de disponibilidad](../classic/portal-sql-alwayson-availability-groups.md) pero no un agente de escucha.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Instrucciones y limitaciones de los agentes de escucha externos
Tenga en cuenta las siguientes instrucciones acerca del agente de escucha del grupo de disponibilidad en Azure cuando se implementa con la dirección VIP pública del servicio en la nube:

* El agente de escucha del grupo de disponibilidad es compatible con Windows Server 2008 R2, Windows Server 2012 y Windows Server 2012 R2.
* La aplicación cliente debe residir en un servicio en la nube diferente al que contiene el grupo de disponibilidad de las máquinas virtuales. Azure no es compatible con Direct Server Return cuando el cliente y el servidor se encuentran en el mismo servicio en la nube.
* Los pasos descritos en este artículo le mostrarán de forma predeterminada cómo configurar un agente de escucha para usar la dirección IP Virtual (VIP) del servicio en la nube. De todos modos, es posible reservar y crear varias direcciones VIP para el servicio en la nube. Esto le permitirá usar los pasos de este artículo para crear varios agentes de escucha asociados a una VIP diferente. Para obtener información sobre cómo crear varias direcciones VIP, consulte [Varias direcciones VIP por cada servicio en la nube](../../../load-balancer/load-balancer-multivip.md).
* Si crea un agente de escucha para un entorno híbrido, la red local debe tener conectividad a Internet pública así como a la VPN de sitio a sitio con la red virtual de Azure. Cuando se encuentra en la subred de Azure, el agente de escucha del grupo de disponibilidad solo está disponible con la dirección IP pública del servicio en la nube respectivo.
* No se puede crear un agente de escucha externo en el mismo servicio en la nube donde también haya creado un agente de escucha interno mediante el Equilibrador de carga interno (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Determinar la accesibilidad del agente de escucha
[!INCLUDE [ag-listener-accessibility](../../../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artículo se centra en la creación de un agente de escucha que usa **equilibrio de carga externo**. Si quiere un agente de escucha que sea privado para su red virtual, vea la versión de este artículo que indica los pasos necesarios para configurar un [agente de escucha con ILB](../classic/ps-sql-int-listener.md).

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Creación de extremos de máquina virtual de carga equilibrada con Direct Server Return
El equilibrio de carga externo usa la dirección IP virtual pública del servicio en la nube que hospeda las máquinas virtuales. Por lo que en este caso no tiene que crear ni configurar el equilibrador de carga.

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
    
1. Copie el siguiente script de PowerShell en un editor de texto y establezca los valores de variable que se ajusten a su entorno (se han proporcionado los valores predeterminados para algunos parámetros). Tenga en cuenta que si el grupo de disponibilidad abarca regiones de Azure, debe ejecutar el script una vez en cada centro de datos del servicio en la nube y los nodos que residen en ese centro de datos.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

2. Una vez configuradas las variables, copie el script del editor de texto en la sesión de Azure PowerShell para ejecutarlo. Si el mensaje todavía muestra >>, escriba ENTER de nuevo para asegurarse de que el script comienza a ejecutarse.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Comprobación de que KB2854082 está instalado si es necesario.
[!INCLUDE [kb2854082](../../../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Apertura de los puertos de firewall en los nodos de grupo de disponibilidad
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Creación del agente de escucha de grupo de disponibilidad

Cree el agente de escucha de grupo de disponibilidad en dos pasos. En primer lugar, cree el recurso de clúster del punto de acceso cliente y configure las dependencias. En segundo lugar, configure los recursos de clúster con PowerShell.

### <a name="create-the-client-access-point-and-configure-the-cluster-dependencies"></a>Creación del punto de acceso cliente y configuración de las dependencias de clúster
[!INCLUDE [firewall](../../../../includes/virtual-machines-ag-listener-create-listener.md)]

### <a name="configure-the-cluster-resources-in-powershell"></a>Configuración de los recursos de clúster en PowerShell
1. Para el equilibrio de carga externo, debe obtener la dirección IP virtual pública del servicio en la nube que contiene las réplicas. Inicie sesión en Azure Portal. Navegue hasta el servicio en la nube que contiene la máquina virtual del grupo de disponibilidad. Abra la vista **Panel** .
2. Tome nota de la dirección que aparece en **Dirección IP virtual (VIP) pública**. Si la solución abarca redes virtuales, repita este paso para cada servicio en la nube que contenga una máquina virtual que hospeda una réplica.
3. En una de las máquinas virtuales, copie el siguiente script de PowerShell en un editor de texto y establezca las variables en los valores que anotó anteriormente.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Una vez establecidas las variables, abra una ventana de Windows PowerShell con privilegios elevados, copie el script del editor de texto y péguelo en la sesión de Azure PowerShell para ejecutarlo. Si el mensaje todavía muestra >>, escriba ENTER de nuevo para asegurarse de que el script comienza a ejecutarse.
5. Repita este paso en cada máquina virtual. Este script configura el recurso de dirección IP con la dirección IP del servicio en la nube y establece otros parámetros como, por ejemplo, el puerto de sondeo. El recurso de dirección IP, una vez conectado, puede responder al sondeo en el puerto de sondeo del extremo con equilibrio de carga que se creó anteriormente en este tutorial.

## <a name="bring-the-listener-online"></a>Conexión del agente de escucha
[!INCLUDE [Bring-Listener-Online](../../../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Elementos de seguimiento
[!INCLUDE [Follow-up](../../../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Prueba del agente de escucha del grupo de disponibilidad (dentro de la misma red virtual)
[!INCLUDE [Test-Listener-Within-VNET](../../../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Prueba del agente de escucha del grupo de disponibilidad (por Internet)
Para obtener acceso al agente de escucha desde el exterior de la red virtual, debe usar equilibrio de carga público o externo (descrito en este tema) en lugar de ILB, que es accesible únicamente dentro de la misma red virtual. En la cadena de conexión, especifique el nombre del servicio en la nube. Por ejemplo, si tiene un servicio en la nube denominado *mycloudservice*, la instrucción sqlcmd sería tal como sigue:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

A diferencia del ejemplo anterior, se debe usar autenticación de SQL, ya que el autor de la llamada no puede usar la autenticación de Windows por Internet. Para más información, consulte [Always On Availability Group in Azure VM: Client Connectivity Scenarios](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx)(Grupo de disponibilidad AlwaysOn en la máquina virtual de Azure: escenarios de conectividad de cliente). Al usar la autenticación de SQL, asegúrese de que crea el mismo inicio de sesión en ambas réplicas. Para obtener más información sobre cómo solucionar problemas de inicio de sesión con grupos de disponibilidad, consulte [Asignar inicios de sesión o usar un usuario de base de datos SQL contenido para conectar con otras réplicas y asignarlas a las bases de datos de disponibilidad](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Si las réplicas AlwaysOn están en subredes diferentes, los clientes tendrán que especificar **MultisubnetFailover=True** en la cadena de conexión. Esto se traduce en intentos de conexión en paralelo con las réplicas de las diferentes subredes. Tenga en cuenta que este escenario incluye una implementación de grupo de disponibilidad AlwaysOn entre regiones.

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [Listener-Next-Steps](../../../../includes/virtual-machines-ag-listener-next-steps.md)]

