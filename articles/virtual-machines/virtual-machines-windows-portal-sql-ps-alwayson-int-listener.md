<properties
   pageTitle="Configuración de agentes de escucha de grupo de disponibilidad AlwaysOn – Microsoft Azure"
   description="Configure agentes de escucha de grupo de disponibilidad en el modelo de Azure Resource Manager mediante un equilibrador de carga interno con una o varias direcciones IP."
   services="virtual-machines"
   documentationCenter="na"
   authors="MikeRayMSFT"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows-sql-server"
   ms.workload="infrastructure-services"
   ms.date="09/22/2016"
   ms.author="MikeRayMSFT"/>

# Configuración de uno o varios agentes de escucha de grupo de disponibilidad AlwaysOn: Resource Manager 

En este tema se muestra cómo llevar a cabo dos tareas:

- Crear un equilibrador de carga interno para grupos de disponibilidad de SQL Server mediante cmdlets de PowerShell

- Agregar direcciones IP adicionales a un equilibrador de carga para admitir más de un grupo de disponibilidad de SQL Server

> **Importante** La capacidad para asignar varias direcciones IP a un equilibrador de carga interno es nueva en Azure y solo está disponible en el modelo de Resource Manager. La compatibilidad con varias direcciones IP en un equilibrador de carga interno es una característica en versión preliminar y está sujeta a los términos de la versión preliminar incluidos en el contrato de licencia (por ejemplo, el Contrato Enterprise, el Contrato de Microsoft Azure o el contrato Microsoft Online Subscription), así como a otros [Términos de uso complementarios de las versiones preliminares de Microsoft Azure](http://azure.microsoft.com/support/legal/preview-supplemental-terms/) aplicables.

En SQL Server, un agente de escucha de grupo de disponibilidad es un nombre de red virtual a la que los clientes se conectan para acceder a una base de datos en la réplica principal o secundaria. En las máquinas virtuales de Azure, un equilibrador de carga contiene la dirección IP del agente de escucha. El equilibrador de carga enruta el tráfico a la instancia de SQL Server que está escuchando en el puerto de sondeo. En la mayoría de los casos, un grupo de disponibilidad usa un equilibrador de carga interno. Un equilibrador de carga interno de Azure puede hospedar una o varias direcciones IP. Cada una usa un puerto de sondeo específico. En este documento, se muestra cómo usar PowerShell para crear un equilibrador de carga o para agregar direcciones IP a un equilibrador de carga existente para grupos de disponibilidad de SQL Server.

Para completar esta tarea, debe tener un grupo de disponibilidad de SQL Server implementado en las máquinas virtuales de Azure con el modelo de Resource Manager. Las dos máquinas virtuales de SQL Server deben pertenecer al mismo conjunto de disponibilidad. Puede usar la [plantilla de Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) para crear automáticamente el grupo de disponibilidad en Azure Resource Manager. Esta plantilla crea automáticamente el grupo de disponibilidad, incluido el equilibrador de carga interno. Si lo prefiere, puede [configurar manualmente un grupo de disponibilidad AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

En este tema, es necesario que los grupos de disponibilidad ya estén configurados.

Temas relacionados:

 - [Configuración de Grupos de disponibilidad AlwaysOn en la máquina virtual de Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)
 
 - [Configuración de una conexión entre dos redes virtuales mediante Azure Resource Manager y PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

[AZURE.INCLUDE [Inicio de una sesión de PowerShell](../../includes/sql-vm-powershell.md)]

## Configuración de Firewall de Windows

Configure Firewall de Windows para permitir el acceso de SQL Server. Debe configurar el firewall para que permita conexiones TCP a los puertos usados por la instancia de SQL Server, así como al puerto usado por el sondeo del agente de escucha. Para instrucciones detalladas, consulte [Configurar Firewall de Windows para el acceso al motor de base de datos](http://msdn.microsoft.com/library/ms175043.aspx#Anchor_1). Cree una regla de entrada para el puerto de SQL Server y para el puerto de sondeo.

## Script de ejemplo: Crear un equilibrador de carga interno con PowerShell

> [AZURE.NOTE] Si ha creado el grupo de disponibilidad con la [plantilla de Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md), no es necesario completar este paso.

El siguiente script de PowerShell crea un equilibrador de carga interno, configura las reglas de equilibrio de carga y establece una dirección IP para el equilibrador de carga. Para ejecutar el script, abra Windows PowerShell ISE y pegue el script en el panel Script. Use `Login-AzureRMAccount` para iniciar sesión en PowerShell. Si tiene varias suscripciones de Azure, puede usar `Select-AzureRmSubscription ` para establecer la suscripción.

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<Resource Group Name>" # Resource group name
$VNetName = "<Virtual Network Name>"         # Virtual network name
$SubnetName = "<Subnet Name>"                # Subnet name
$ILBName = "<Load Balancer Name>"            # ILB name
$Location = "<Azure Region>"                 # Azure location
$VMNames = "<VM1>","<VM2>"                   # Virtual machine names

$ILBIP = "<n.n.n.n>"                         # IP address
[int]$ListenerPort = "<nnnn>"                # AG listener port
[int]$ProbePort = "<nnnn>"                   # Probe port

$LBProbeName ="ILBPROBE_$ListenerPort"       # The Load balancer Probe Object Name              
$LBConfigRuleName = "ILBCR_$ListenerPort"    # The Load Balancer Rule Object Name

$FrontEndConfigurationName = "FE_SQLAGILB_1" # Object name for the Front End configuration 
$BackEndConfigurationName ="BE_SQLAGILB_1"   # Object name for the Back End configuration

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 

$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName 

$FEConfig = New-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.id

$BEConfig = New-AzureRMLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName 

$SQLHealthProbe = New-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -Protocol tcp -Port $ProbePort -IntervalInSeconds 15 -ProbeCount 2

$ILBRule = New-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP 

$ILB= New-AzureRmLoadBalancer -Location $Location -Name $ILBName -ResourceGroupName $ResourceGroupName -FrontendIpConfiguration $FEConfig -BackendAddressPool $BEConfig -LoadBalancingRule $ILBRule -Probe $SQLHealthProbe 

$bepool = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $BackEndConfigurationName -LoadBalancer $ILB 

foreach($VMName in $VMNames)
    {
        $VM = Get-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VMName 
        $NICName = ($VM.NetworkInterfaceIDs[0].Split('/') | select -last 1)
        $NIC = Get-AzureRmNetworkInterface -name $NICName -ResourceGroupName $ResourceGroupName
        $NIC.IpConfigurations[0].LoadBalancerBackendAddressPools = $BEPool
        Set-AzureRmNetworkInterface -NetworkInterface $NIC
        start-AzureRmVM -ResourceGroupName $ResourceGroupName -Name $VM.Name 
    }
```

## Script de ejemplo: Agregar una dirección IP a un equilibrador de carga existente con PowerShell

Para utilizar más de un grupo de disponibilidad, use PowerShell para agregar otra dirección IP a un equilibrador de carga existente. Cada dirección IP requiere su regla de equilibrio de carga, puerto de sondeo y puerto de front-end propios.

El puerto de front-end es el que las aplicaciones usan para conectarse a la instancia de SQL Server. Las direcciones IP para los diferentes grupos de disponibilidad pueden usar el mismo puerto de front-end.

>[AZURE.NOTE] Para los grupos de disponibilidad de SQL Server, cada dirección IP requiere un puerto de sondeo específico. Por ejemplo, si una dirección IP en un equilibrador de carga utiliza el puerto de sondeo 59999, no puede usarlo ninguna otra dirección IP de ese equilibrador de carga.

- Para información sobre los límites del equilibrador de carga, consulte **IP de front-end privada por equilibrador de carga** en [Límites de redes - Azure Resource Manager](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

- Para información acerca de los límites de los grupos de disponibilidad, consulte [ Restricciones (grupos de disponibilidad)](http://msdn.microsoft.com/library/ff878487.aspx#RestrictionsAG).

El script siguiente agrega una nueva dirección IP a un equilibrador de carga existente. Actualice las variables para su entorno. El equilibrador de carga interno usa el puerto del agente de escucha para el puerto de front-end de equilibrio de carga. Este puerto puede ser aquel en el que SQL Server escucha. Para las instancias predeterminadas de SQL Server, se trata del puerto 1433. La regla de equilibrio de carga para un grupo de disponibilidad requiere una dirección IP flotante (Direct Server Return), así que el puerto de back-end es el mismo que el puerto de front-end.

```powershell
# Login-AzureRmAccount
# Select-AzureRmSubscription -SubscriptionId <xxxxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx>

$ResourceGroupName = "<ResourceGroup>"          # Resource group name
$VNetName = "<VirtualNetwork>"                  # Virtual network name
$SubnetName = "<Subnet>"                        # Subnet name
$ILBName = "<ILBName>"                          # ILB name                      

$ILBIP = "<n.n.n.n>"                            # IP address
[int]$ListenerPort = "<nnnn>"                   # AG listener port
[int]$ProbePort = "<nnnnn>"                     # Probe port 

$ILB = Get-AzureRmLoadBalancer -Name $ILBName -ResourceGroupName $ResourceGroupName 

$count = $ILB.FrontendIpConfigurations.Count+1
$FrontEndConfigurationName ="FE_SQLAGILB_$count"  

$LBProbeName = "ILBPROBE_$count"
$LBConfigrulename = "ILBCR_$count"

$VNet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName 
$Subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $VNet -Name $SubnetName

$ILB | Add-AzureRmLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -PrivateIpAddress $ILBIP -SubnetId $Subnet.Id 

$ILB | Add-AzureRmLoadBalancerProbeConfig -Name $LBProbeName  -Protocol Tcp -Port $Probeport -ProbeCount 2 -IntervalInSeconds 15  | Set-AzureRmLoadBalancer 

$ILB = Get-AzureRmLoadBalancer -Name $ILBname -ResourceGroupName $ResourceGroupName

$FEConfig = get-AzureRMLoadBalancerFrontendIpConfig -Name $FrontEndConfigurationName -LoadBalancer $ILB

$SQLHealthProbe  = Get-AzureRmLoadBalancerProbeConfig -Name $LBProbeName -LoadBalancer $ILB

$BEConfig = Get-AzureRmLoadBalancerBackendAddressPoolConfig -Name $ILB.BackendAddressPools[0].Name -LoadBalancer $ILB 

$ILB | Add-AzureRmLoadBalancerRuleConfig -Name $LBConfigRuleName -FrontendIpConfiguration $FEConfig  -BackendAddressPool $BEConfig -Probe $SQLHealthProbe -Protocol tcp -FrontendPort  $ListenerPort -BackendPort $ListenerPort -LoadDistribution Default -EnableFloatingIP | Set-AzureRmLoadBalancer   
``` 



## Configure el clúster para que utilice la dirección IP del equilibrador de carga. 

El siguiente paso consiste en configurar el agente de escucha del clúster y conectarlo. Para ello, siga estos pasos:

1. Creación del agente de escucha del grupo de disponibilidad en el clúster de conmutación por error

1. Conexión del agente de escucha

## 1\. Creación del agente de escucha del grupo de disponibilidad en el clúster de conmutación por error

En este paso, agregue un punto de acceso cliente al clúster de conmutación por error con el Administrador de clústeres de conmutación por error. Después, use PowerShell para configurar el recurso de clúster de forma que escuche en el puerto de sondeo.

- Use RDP para conectarse a la máquina virtual de Azure que hospeda la réplica principal.

- Abra el Administrador de clústeres de conmutación por error.

- Seleccione el nodo **Redes** y anota el nombre de red del clúster. Use este nombre en la variable `$ClusterNetworkName` del script de PowerShell.

- Amplía el nombre del clúster y haz clic en **Funciones**.

- En el panel **Roles**, haz clic en el nombre del grupo de disponibilidad y después selecciona **Agregar recurso** > **Punto de acceso cliente**.

- En la casilla **Nombre**, crea un nombre para este nuevo agente de escucha, después haz clic en **Siguiente** dos veces y haz clic en **Finalizar**. No pongas el agente de escucha o el recurso en línea en este momento.

 >[AZURE.NOTE] Las aplicaciones utilizarán el nombre del nuevo agente de escucha como nombre de red para conectarse a las bases de datos del grupo de disponibilidad de SQL Server.

- Haz clic en la pestaña **Recursos** y después amplía el punto de acceso de cliente que acabas de crear. Haga clic con el botón derecho en el recurso de IP y, después, en Propiedades. Anote el nombre de la dirección IP. Este es el nombre que va a utilizar en la variable `$IPResourceName` del script de PowerShell.

- En **Dirección IP**, haga clic en **Dirección IP estática** y especifique la misma dirección que utilizó al definir la dirección IP del equilibrador de carga en Azure Portal.

- Deshabilite NetBIOS para esta dirección y haga clic en **Aceptar**. Repite este paso en cada recurso de IP si tu solución abarca varias redes virtuales de Azure.

- Haga que el recurso de grupo de disponibilidad de SQL Server dependa de la dirección IP. Haga clic con el botón derecho en el recurso en el Administrador de clústeres; esta opción se encuentra en la pestaña **Recursos**, en **Otros recursos**.

- En el nodo de clúster donde actualmente se hospeda la réplica principal, abra una instancia de PowerShell ISE con privilegios elevados y pegue los siguientes comandos en un nuevo script. En la pestaña **Dependencias**, haga clic en el nombre del agente de escucha.
        
    ```powershell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>

    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

- Actualice las variables y ejecute el script de PowerShell para configurar la dirección IP y el puerto del nuevo agente de escucha.

 >[AZURE.NOTE] Si los servidores SQL Server se encuentran en distintas regiones, debe ejecutar el script de PowerShell dos veces. La primera vez, utilice el nombre de red del clúster, el nombre del recurso IP del clúster y la dirección IP del equilibrador de carga correspondientes al primer grupo de recursos. La segunda vez, utilice el nombre de red del clúster, el nombre del recurso IP del clúster y la dirección IP del equilibrador de carga correspondientes al segundo grupo de recursos.

Ahora, el clúster dispone de un recurso de agente de escucha del grupo de disponibilidad.

## 2\. Conexión del agente de escucha

Una vez que el recurso de agente de escucha del grupo de disponibilidad está configurado, puede ponerlo en línea. De este modo, las aplicaciones podrán conectarse a las bases de datos del grupo de disponibilidad con el agente de escucha.

- Desplázate atrás al Administrador de clústeres de conmutación por error. Amplia las **Funciones** y después resalta tu grupo de disponibilidad. En la pestaña **Recursos**, haga clic con el botón derecho en el nombre del agente de escucha y haga clic en **Propiedades**.

- Haz clic en la pestaña **Dependencias**. Si aparecen varios recursos, comprueba que las direcciones IP tienen dependencias OR, y no AND. Haga clic en **Aceptar**.

- Haz clic en el nombre del agente de escucha y luego haz clic en **Poner en línea**.


- Una vez que el agente de escucha está en línea, en la pestaña de **Recursos**, haz clic con el botón derecho en el grupo de disponibilidad y haz clic en **Propiedades**.

- Crea una dependencia en el recurso del nombre del agente de escucha (no en el nombre de los recursos de dirección IP). Haga clic en **Aceptar**.


- Abra SQL Server Management Studio y conéctese a la réplica principal.


- Vaya a **Alta disponibilidad de AlwaysOn** | **Grupos de disponibilidad** | **Agentes de escucha del grupo de disponibilidad**.


- Ahora tienes que ver el nombre del agente de escucha que creaste en el Administrador de clústeres de conmutación por error. Haga clic con el botón derecho en el nombre del agente de escucha y luego en **Propiedades**.


- En el cuadro **Puerto**, especifique el número de puerto del agente de escucha del grupo de disponibilidad mediante el valor de $EndpointPort que estableció antes (1433 era el valor predeterminado) y haga clic en **Aceptar**.

Ahora tiene un grupo de disponibilidad de SQL Server en las máquinas virtuales de Azure que se ejecuta en el modo de Resource Manager.

## Comprobación de la conexión con el agente de escucha

Para probar la conexión:

1. Conéctese con RDP a un servidor SQL Server que esté en la misma red virtual, pero que no sea el propietario de la réplica; por ejemplo, a otro servidor SQL Server del clúster.

1. Use la utilidad **sqlcmd** para probar la conexión. Por ejemplo, el siguiente script establece una conexión **sqlcmd** con la réplica principal por medio del agente de escucha con autenticación de Windows:

        sqlmd -S <listenerName> -E

    Si el agente de escucha usa un puerto distinto del predeterminado (1433), especifíquelo en la cadena de conexión. Por ejemplo, el siguiente comando sqlcmd se conecta a un agente de escucha en el puerto 1435:
    
        sqlcmd -S <listenerName>,1435 -E

La conexión SQLCMD se establece automáticamente con la instancia de SQL Server en la que se hospede la réplica principal.

>[AZURE.NOTE] Asegúrese de que el puerto especificado esté abierto en el firewall de los dos servidores SQL Server. En estos dos servidores, es necesario definir una regla de entrada para el puerto TCP. Consulte [Agregar o editar regla de firewall](http://technet.microsoft.com/library/cc753558.aspx) para más información.

## Pautas y limitaciones

Cuando utilice un equilibrador de carga interno, tenga en cuenta las siguientes instrucciones que se aplican al agente de escucha del grupo de disponibilidad de Azure:

- Como solo hay un equilibrador de carga interno, el acceso al agente de escucha se realizará desde la misma red virtual.

## Para obtener más información

Para más información, consulte [Configuración manual de grupos de disponibilidad AlwaysOn en máquinas virtuales de Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

### Cmdlets de PowerShell

Use los siguientes cmdlets de PowerShell para crear un equilibrador de carga interno para máquinas virtuales de Azure.

- `New-AzureRmLoadBalancer` crea un equilibrador de carga. Consulte [New-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt619450.aspx) para más información.

- `New-AzureRMLoadBalancerFrontendIpConfig` crea una configuración de dirección IP de front-end para un equilibrador de carga. Consulte [New-AzureRMLoadBalancerFrontendIpConfig](http://msdn.microsoft.com/library/mt603510.aspx) para más información.

- `New-AzureRmLoadBalancerRuleConfig` crea una configuración de regla para un equilibrador de carga. Consulte [New-AzureRmLoadBalancerRuleConfig](http://msdn.microsoft.com/library/mt619391.aspx) para más información.

- `New-AzureRMLoadBalancerBackendAddressPoolConfig` crea una configuración de grupo de direcciones de back-end para un equilibrador de carga. Consulte [New-AzureRmLoadBalancerBackendAddressPoolConfig](http://msdn.microsoft.com/library/mt603791.aspx) para más información.

- `New-AzureRmLoadBalancerProbeConfig` crea una configuración de sondeo para un equilibrador de carga. Consulte [New-AzureRmLoadBalancerProbeConfig](http://msdn.microsoft.com/library/mt603847.aspx) para más información.

Si necesita quitar un equilibrador de carga de un grupo de recursos de Azure, use `Remove-AzureRmLoadBalancer`. Para más información, consulte [Remove-AzureRmLoadBalancer](http://msdn.microsoft.com/library/mt603862.aspx).

<!---HONumber=AcomDC_0928_2016-->