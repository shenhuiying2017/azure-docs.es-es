<properties
   pageTitle="Configuración de conexiones de ExpressRoute y VPN de sitio a sitio coexistentes | Microsoft Azure"
   description="Este artículo le guiará por el proceso de configurar conexiones ExpressRoute y VPN de sitio a sitio que puedan coexistir."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-service-management"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/02/2015"
   ms.author="cherylmc"/>

# Configuración de conexiones de ExpressRoute y VPN de sitio a sitio para que coexistan para una red virtual

Tener la capacidad de configurar VPN de sitio a sitio y ExpressRoute tiene varias ventajas. Puede configurar una VPN de sitio a sitio como una ruta de acceso seguro de conmutación por error para ExressRoute o usar VPN de sitio a sitio para conectarse a sitios que no forman parte de la red, pero que se conectan a través de ExpressRoute. En este artículo trataremos los pasos para configurar ambos escenarios. Este artículo se aplica a conexiones creadas con el modelo de implementación clásico.

>[AZURE.IMPORTANT]Es importante saber que, actualmente, Azure funciona con dos modelos de implementación: el Administrador de recursos y el clásico. Antes de comenzar con la configuración, asegúrate de que comprendes los modelos y las herramientas de implementación. Para obtener información sobre los modelos de implementación, vea [Modelos de implementación de Azure](../azure-classic-rm.md).


Los circuitos ExpressRoute deben configurarse previamente antes de seguir las instrucciones siguientes. Asegúrese de que ha seguido las guías para [crear un circuito ExpressRoute](expressroute-howto-circuit-classic.md) y [configurar el enrutamiento](expressroute-howto-routing-classic.md) antes de seguir los pasos que se indican a continuación.

## Límites y limitaciones

- **El enrutamiento de tránsito no se admite:** no se puede realizar un enrutamiento (a través de Azure) entre una red local conectada a través de una VPN de sitio a sitio y una red local conectada a través de ExpressRoute.
- **Punto a sitio no se admite:** no se pueden habilitar conexiones VPN de punto a sitio a la misma red virtual que esté conectada a ExpressRoute. VPN de punto a sitio y ExpressRoute no pueden coexistir en la misma red virtual.
- **No se puede habilitar la tunelización forzada en la puerta de enlace de VPN de sitio a sitio:** solo puede "forzar" todo el tráfico enlazado a Internet a la red local a través de ExpressRoute. 
- **Solo puertas de enlace de rendimiento alto o estándar:** debe usar una puerta de enlace de rendimiento alto o estándar para la puerta de enlace de ExpressRoute y la puerta de enlace de VPN de sitio a sitio. Para información sobre las SKU de puerta de enlace, consulte [SKU de puerta de enlace](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- **Requisito de ruta estática**: si la red local está conectada a ExpressRoute y a una VPN de sitio a sitio, debe tener configurada una ruta estática en la red local para enrutar la conexión de VPN de sitio a sitio a la red Internet pública.
- **Primero debe estar configurada la puerta de enlace de ExpressRoute:** debe crear la puerta de enlace de ExpressRoute antes de agregar la puerta de enlace de VPN de sitio a sitio.

## Configuración de una VPN de sitio a sitio como una ruta de acceso de conmutación por error para ExpressRoute

Puede configurar una conexión VPN de sitio a sitio como una copia de seguridad para ExpressRoute. Esto se aplica únicamente a las redes virtuales vinculadas a la ruta de acceso de emparejamiento privada de Azure. No hay ninguna solución de conmutación por error basada en VPN para servicios accesibles a través de emparejamientos de Microsoft y públicos de Azure. El circuito ExpressRoute siempre es el vínculo principal. Los datos fluirán a través de la ruta de acceso VPN de sitio a sitio solo si se produce un error en el circuito ExpressRoute.

![Coexistencia](media/expressroute-howto-coexist-classic/scenario1.jpg)

## Configuración de una VPN de sitio a sitio para conectarse a sitios no conectados a través de ExpressRoute

Puede configurar la red donde algunos sitios se conectan directamente a Azure a través de una VPN de sitio a sitio y algunos sitios a través de ExpressRoute.

![Coexistencia](media/expressroute-howto-coexist-classic/scenario2.jpg)

>[AZURE.NOTE]No se puede configurar una red virtual como un enrutador de tránsito.

## Creación y configuración

Hay dos conjuntos diferentes de los procedimientos entre los que elegir para configurar las conexiones que pueden coexistir. El procedimiento de configuración que seleccione dependerá de si ya tiene una red virtual existente a la que quiere conectarse o si desea crear una nueva red virtual.

- **Creación de una nueva red virtual con conexiones coexistentes:**
	
	Si aún no tiene una red virtual, este procedimiento le guiará a través de la creación de una nueva red virtual y la creación de nuevas conexiones VPN de sitio a sitio y ExpressRoute. Para realizar la configuración, siga los pasos descritos en la sección del artículo **Creación de una nueva red virtual con conectividad de sitio a sitio y ExpressRoute**.

- **Configuración de la red virtual existente para conexiones coexistentes**

	Puede que ya tenga una red virtual con una conexión VPN de sitio a sitio o una conexión ExpressRoute existente. La sección **Configuración de conexiones coexistentes para la red virtual existente** le guiará a través de la eliminación de la puerta de enlace y la creación de nuevas conexiones VPN de sitio a sitio y ExpressRoute. Tenga en cuenta que al crear las nuevas conexiones, se deben completar los pasos en un orden muy específico. No utilice las instrucciones que aparecen en otros artículos para crear puertas de enlace y conexiones.

	En este procedimiento, para crear conexiones que puedan coexistir, tendrá que eliminar la puerta de enlace y luego configurar nuevas puertas de enlace. Esto significa que tendrá tiempo de inactividad para las conexiones entre entornos mientras elimina y vuelve a crear la puerta de enlace y las conexiones, pero no necesitará migrar las máquinas virtuales o servicios a una nueva red virtual. Las máquinas virtuales y los servicios podrán seguir comunicándose con el exterior a través del equilibrador de carga mientras configura la puerta de enlace si están configurados para ello.


## Creación de una nueva red virtual con conectividad de sitio a sitio y ExpressRoute

Este procedimiento le guiará a través de la creación de una red virtual y creará conexiones de sitio a sitio y ExpressRoute que coexistirán.

1. Compruebe que tiene la versión más reciente de los cmdlets de PowerShell. Los cmdlets de PowerShell más recientes se pueden descargar e instalar desde la sección de PowerShell de la [página de descarga](http://azure.microsoft.com/downloads/).

2. Cree un esquema para la red virtual. Para más información sobre cómo trabajar con el archivo de configuración de red, consulte [Configuración de una red virtual con un archivo de configuración de red](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Para más información sobre el esquema de configuración, consulte [Esquema de configuración de la red virtual de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

	Al crear el esquema, asegúrese de que usa los valores siguientes:

	- La subred de puerta de enlace para la red virtual debe ser /27 o un prefijo más corto (como /26 o /25).
	- El tipo de conexión de puerta de enlace es "Dedicado".

		      <VirtualNetworkSite name="MyAzureVNET" Location="Central US">
		        <AddressSpace>
		          <AddressPrefix>10.17.159.192/26</AddressPrefix>
		        </AddressSpace>
		        <Subnets>
		          <Subnet name="Subnet-1">
		            <AddressPrefix>10.17.159.192/27</AddressPrefix>
		          </Subnet>
		          <Subnet name="GatewaySubnet">
		            <AddressPrefix>10.17.159.224/27</AddressPrefix>
		          </Subnet>
		        </Subnets>
		        <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
		      </VirtualNetworkSite>

3. Después de crear y configurar el archivo de esquema xml, cargue el archivo. Esto creará la red virtual.

	Use el cmdlet siguiente para cargar el archivo, reemplazando el valor por el suyo propio.

		Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'

4. Cree una puerta de enlace de ExpressRoute. Asegúrese de especificar GatewaySKU como *Standard* o *HighPerformance* y GatewayType como *DynamicRouting*.

	Use el ejemplo siguiente y sustituya los valores por los suyos propios.

		New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance

5. Vincule la puerta de enlace de ExpressRoute al circuito ExpressRoute. Una vez completado este paso, se ha establecido la conexión entre su red local y Azure a través de ExpressRoute.

		New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET

6. A continuación, cree la puerta de enlace de VPN de sitio a sitio. GatewaySKU debe ser *Standard* o *HighPerformance* y GatewayType debe ser *DynamicRouting*.

		New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance

	Para recuperar la configuración de la puerta de enlace de red virtual, incluido el identificador de la puerta de enlace y la dirección IP pública, use el cmdlet `Get-AzureVirtualNetworkGateway`.

		Get-AzureVirtualNetworkGateway

		GatewayId            : 348ae011-ffa9-4add-b530-7cb30010565e
		GatewayName          : S2SVPN
		LastEventData        :
		GatewayType          : DynamicRouting
		LastEventTimeStamp   : 5/29/2015 4:41:41 PM
		LastEventMessage     : Successfully created a gateway for the following virtual network: GNSDesMoines
		LastEventID          : 23002
		State                : Provisioned
		VIPAddress           : 104.43.x.y
		DefaultSite          :
		GatewaySKU           : HighPerformance
		Location             :
		VnetId               : 979aabcf-e47f-4136-ab9b-b4780c1e1bd5
		SubnetId             :
		EnableBgp            : False
		OperationDescription : Get-AzureVirtualNetworkGateway
		OperationId          : 42773656-85e1-a6b6-8705-35473f1e6f6a
		OperationStatus      : Succeeded

7. Cree una entidad de puerta de enlace de VPN de sitio local. Este comando no configura la puerta de enlace de VPN local. En su lugar, permite proporcionar la configuración de puerta de enlace local, como la dirección IP pública y el espacio de direcciones local, para que la puerta de enlace de VPN de Azure pueda conectarse a ella.

	> [AZURE.IMPORTANT]El sitio local para la VPN de sitio a sitio no está definido en netcfg. En su lugar, es preciso usar este cmdlet para especificar los parámetros del sitio local. No se puede definir con el Portal de Azure clásico o el archivo netcfg.

	Use el ejemplo siguiente y reemplace los valores por los suyos propios:

	`New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <MyLocalGatewayIp> -AddressSpace <MyLocalNetworkAddress>`

	> [AZURE.NOTE]Si la red local tiene varias rutas, puede pasar todas ellas en una matriz. $MyLocalNetworkAddress = @("10.1.2.0/24","10.1.3.0/24","10.2.1.0/24")


	Para recuperar la configuración de la puerta de enlace de red virtual, lo que incluye el identificador de puerta de enlace y la dirección IP pública, use el cmdlet `Get-AzureVirtualNetworkGateway`. Consulte el ejemplo siguiente.

		Get-AzureLocalNetworkGateway

		GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
		GatewayName          : MyLocalNetwork
		IpAddress            : 23.39.x.y
		AddressSpace         : {10.1.2.0/24}
		OperationDescription : Get-AzureLocalNetworkGateway
		OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
		OperationStatus      : Succeeded


8. Configure el dispositivo VPN local para que se conecte a la nueva puerta de enlace. Al configurar el dispositivo VPN, use la información que recuperó en el paso 6. Para más información sobre la configuración del dispositivo VPN, consulte [Acerca de los dispositivos VPN para conexiones de red virtual de sitio a sitio](http://go.microsoft.com/fwlink/p/?linkid=615099).

9. Vincule la puerta de enlace de VPN de sitio a sitio en Azure a la puerta de enlace local.

	En este ejemplo, connectedEntityId es el identificador de puerta de enlace local. Para encontrarlo, es preciso ejecutar `Get-AzureLocalNetworkGateway`. Para encontrar virtualNetworkGatewayId, use el cmdlet `Get-AzureVirtualNetworkGateway`. Después de este paso, se establece la conexión entre la red local y Azure a través de la conexión VPN de sitio a sitio.


	`New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>`

## Configuración de conexiones coexistentes para la red virtual existente

Si tiene una red virtual conectada a través de una conexión VPN de sitio a sitio o ExpressRoute, para habilitar ambas conexiones para conectarse a la red virtual existente, primero debe eliminar la puerta de enlace existente. Esto significa que las instalaciones locales perderán la conexión a la red virtual a través de la puerta de enlace mientras trabaja en esta configuración.

**Antes de empezar la configuración:** compruebe que quedan suficientes direcciones IP en la red virtual, con el fin de que pueda aumentar el tamaño de la subred de puerta de enlace.

1. Descargue la versión más reciente de los cmdlets de PowerShell. Los cmdlets de PowerShell más recientes se pueden descargar e instalar desde la sección de PowerShell de la [página de descarga](http://azure.microsoft.com/downloads/).

2. Elimine la puerta de enlace de VPN de sitio a sitio. Use el siguiente cmdlet, reemplazando los valores por los suyos propios.

	`Remove-AzureVNetGateway –VnetName MyAzureVNET`

2. Exporte el esquema de red virtual. Use el siguiente cmdlet de PowerShell, reemplazando los valores por los suyos propios.

	`Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”`

3. Edite el esquema del archivo de configuración de red para que la subred de puerta de enlace sea /27 o un prefijo más corto (como /26 o /25). Consulte el ejemplo siguiente. Para más información sobre cómo trabajar con el archivo de configuración de red, consulte [Configuración de una red virtual con un archivo de configuración de red](../virtual-network/virtual-networks-create-vnet-classic-portal.md). Para más información sobre el esquema de configuración, consulte [Esquema de configuración de la red virtual de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>

4. Si la puerta de enlace anterior era una VPN de sitio a sitio, debe cambiar también el tipo de conexión a **Dedicado**.

		         <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>

5. Ya tiene una red virtual sin puertas de enlace. Para crear nuevas puertas de enlace y completar las conexiones, puede continuar con el **paso 3** de esta sección del artículo [Creación de una nueva red virtual con conectividad de sitio a sitio y ExpressRoute](#create-a-new-virtual-network-with-both-expressroute-and-site-to-site-connectivity).

## Pasos siguientes

Para más información sobre ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).

<!---HONumber=AcomDC_1203_2015-->