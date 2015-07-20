<properties
   pageTitle="Configuración de conexiones de ExpressRoute y VPN sitio a sitio para que coexistentan"
   description="Este tutorial le guiará por el proceso de configuración de ExpressRoute y una conexión VPN de sitio a sitio para que coexistan."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/30/2015"
   ms.author="cherylmc"/>

# Configuración de conexiones de ExpressRoute y VPN sitio a sitio coexistentes

Ahora puede conectar ExpressRoute y una VPN sitio a sitio a la misma red virtual. Hay dos escenarios y dos procedimientos de configuración diferentes entre los que se puede elegir.

### Escenarios

Hay dos escenarios distintos. La siguiente ilustración muestra ambos.

- **Escenario 1**: si tiene una red local, ExpressRoute será el vínculo activo y la VPN sitio a sitio, la copia de seguridad. Si se produce un error en la conexión ExpressRoute, se activará la conexión VPN sitio a sitio. Este escenario es el más adecuado para lograr alta disponibilidad.

- **Escenario 2**: si tiene dos redes locales, puede conectar una a Azure mediante ExpressRoute y la otra a través de VPN sitio a sitio. En este caso, ambas conexiones están activas al mismo tiempo.


![Coexistencia](media/expressroute-coexist/coexist1.jpg)


### Procedimientos de configuración

Hay dos procedimientos de configuración independientes entre los que se puede elegir. El procedimiento de configuración que seleccione dependerá de si ya tiene una red virtual existente que desea conectar o si desea crear una nueva red virtual.


- [Creación de una red virtual nueva con conexiones coexistentes](#create-a-new-vnet-with-coexisting-connections): si aún no tiene una red virtual, este procedimiento le guiará a través de la creación de una nueva red virtual y la creación de nuevas conexiones VPN sitio a sitio y ExpressRoute.  


- [Configuración de una red virtual existente para conexiones coexistentes](#configure-your-existing-vnet-for-coexisting-connections): puede que ya tenga una red virtual con una conexión VPN sitio a sitio o una conexión ExpressRoute existentes. Para crear una conexión coexistente tendrá que eliminar la puerta de enlace y luego configurar nuevas puertas de enlace que puedan coexistir. Esto significa que tendrá tiempo de inactividad para las conexiones entre entornos mientras elimina y vuelve a crear la puerta de enlace y las conexiones, pero no necesitará migrar las máquinas virtuales o servicios a una nueva red virtual. Las máquinas virtuales y los servicios podrán comunicarse con el exterior a través del equilibrador de carga mientras configura la puerta de enlace si están configurados para ello.

	Este procedimiento le guiará a través de la eliminación de la puerta de enlace y la creación de nuevas conexiones VPN sitio a sitio y ExpressRoute. Tenga en cuenta que al crear las nuevas conexiones, se deben completar los pasos en un orden muy específico. No utilice las instrucciones que aparecen en otros artículos para crear puertas de enlace y conexiones.

### Notas y limitaciones

- La puerta de enlace de ExpressRoute y la puerta de enlace de VPN sitio a sitio deben ser una SKU de puerta de enlace estándar o de alto rendimiento. Para obtener información acerca de las SKU de puerta de enlace, consulte [SKU de puerta de enlace](../vpn-gateway/vpn-gateway-about-vpngateways.md).
- Si la red local está conectada a ExpressRoute y a una VPN sitio a sitio (escenario 1), debe tener una ruta estática configurada en la red local para enrutar la conexión VPN sitio a sitio a la red Internet pública. 
- Debe crear la puerta de enlace de ExpressRoute antes de agregar la puerta de enlace de la VPN sitio a sitio.
- No se podrá realizar un enrutamiento (a través de Azure) entre una red local conectada a través de una VPN sitio a sitio y una red local conectada a través de ExpressRoute.
- Para ambos procedimientos se supone que ya tiene un circuito ExpressRoute configurado. Si no es así, consulte los siguientes artículos: 

	- [Configuración de una conexión ExpressRoute a través de un proveedor de servicios de red (NSP)](expressroute-configuring-nsps.md) 
	- [Configuración de una conexión ExpressRoute a través de un proveedor de Exchange (EXP)](expressroute-configuring-exps.md)


## Creación de una red virtual nueva con conexiones coexistentes

1. Compruebe que tiene la versión más reciente de los cmdlets de PowerShell. Los cmdlets de PowerShell más recientes se pueden descargar e instalar desde la sección de PowerShell de la [página de descarga](http://azure.microsoft.com/downloads/).
2. Cree un esquema para la red virtual. Para obtener más información sobre cómo trabajar con el archivo de configuración de red, consulte [Configuración de una red virtual con un archivo de configuración de red](../virtual-network/virtual-networks-using-network-configuration-file.md). Para obtener más información sobre el esquema de configuración, consulte [Esquema de configuración de red virtual de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).

	Al crear el esquema, asegúrese de que usa los valores siguientes:

	- La subred de puerta de enlace para la red virtual debe ser /27 (o un prefijo más corto).
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

	`Set-AzureVNetConfig -ConfigurationPath 'C:\NetworkConfig.xml'`
4. Cree una puerta de enlace de ExpressRoute. Asegúrese de especificar GatewaySKU como *Standard* o *HighPerformance* y GatewayType como *DynamicRouting*. 

	Use el ejemplo siguiente, sustituyendo los valores por los suyos propios.

	`New-AzureVNetGateway -VNetName MyAzureVNET -GatewayType DynamicRouting -GatewaySKU HighPerformance`

5. Vincule la puerta de enlace de ExpressRoute al circuito ExpressRoute. Una vez completado este paso, se ha establecido la conexión entre su red local y Azure a través de ExpressRoute.

	`New-AzureDedicatedCircuitLink -ServiceKey <service-key> -VNetName MyAzureVNET`

6. A continuación, cree la puerta de enlace de la VPN sitio a sitio. GatewaySKU debe ser *Standard* o *HighPerformance* y GatewayType debe ser *DynamicRouting*.

	`New-AzureVirtualNetworkGateway -VNetName MyAzureVNET -GatewayName S2SVPN -GatewayType DynamicRouting -GatewaySKU  HighPerformance`

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

	**Importante:** el sitio local para la VPN sitio a sitio no está definido en netcfg. En su lugar, es preciso usar este cmdlet para especificar los parámetros del sitio local. No se puede definir con el Portal de administración o el archivo netcfg.

	Use el ejemplo siguiente, reemplazando los valores por los suyos propios:

	`New-AzureLocalNetworkGateway -GatewayName MyLocalNetwork -IpAddress <local-network- gateway-public-IP> -AddressSpace <local-network-address-space>`

	Para recuperar la configuración de la puerta de enlace de red virtual, incluido el identificador de la puerta de enlace y la dirección IP pública, use el cmdlet `Get-AzureVirtualNetworkGateway`. Observe el ejemplo siguiente:

		Get-AzureLocalNetworkGateway
		
		GatewayId            : 532cb428-8c8c-4596-9a4f-7ae3a9fcd01b
		GatewayName          : MyLocalNetwork
		IpAddress            : 23.39.x.y
		AddressSpace         : {10.1.2.0/24}
		OperationDescription : Get-AzureLocalNetworkGateway
		OperationId          : ddc4bfae-502c-adc7-bd7d-1efbc00b3fe5
		OperationStatus      : Succeeded

	
8. Configure el dispositivo VPN local para que se conecte a la nueva puerta de enlace. Al configurar el dispositivo VPN, use la información que recuperó en el paso 6. Para obtener más información acerca de la configuración del dispositivo VPN, consulte [Configuración de dispositivos VPN](vpn-gateway-configure-vpn-gateway-mp.md/#gather-information-for-your-vpn-device-configuration).
	

9. Vincule la puerta de enlace de VPN sitio a sitio en Azure a la puerta de enlace local.

	En este ejemplo, connectedEntityId es el id. de puerta de enlace local, que se encuentra ejecutando `Get-AzureLocalNetworkGateway`. Puede encontrar virtualNetworkGatewayId con el cmdlet `Get-AzureVirtualNetworkGateway`. Después de este paso, se establece la conexión entre la red local y Azure a través de la conexión VPN sitio a sitio.


	`New-AzureVirtualNetworkGatewayConnection -connectedEntityId <local-network-gateway-id> -gatewayConnectionName Azure2Local -gatewayConnectionType IPsec -sharedKey abc123 -virtualNetworkGatewayId <azure-s2s-vpn-gateway-id>`


## Configuración de la red virtual existente para conexiones coexistentes

Si tiene una red virtual conectada a través de una conexión VPN sitio a sitio o ExpressRoute, para habilitar ambas conexiones para conectarse a la red virtual existente, primero debe eliminar la puerta de enlace existente. Esto significa que las instalaciones locales perderán la conexión a la red virtual a través de la puerta de enlace mientras trabaja en esta configuración.

**Antes de empezar la configuración:** compruebe que quedan suficientes direcciones IP en la red virtual, con el fin de que pueda aumentar el tamaño de la subred de puerta de enlace.


1. Asegúrese de que tiene la versión más reciente de los cmdlets de PowerShell. Los cmdlets de PowerShell más recientes se pueden descargar e instalar desde la sección de PowerShell de la [página de descarga](http://azure.microsoft.com/downloads/).
 
2. Elimine la puerta de enlace de la VPN sitio a sitio. Use el siguiente cmdlet, reemplazando los valores por los suyos propios.

	`Remove-AzureVNetGateway –VnetName MyAzureVNET`

2. Exporte el esquema de red virtual. Use el siguiente cmdlet de PowerShell, reemplazando los valores por los suyos propios.

	`Get-AzureVNetConfig –ExportToFile “C:\NetworkConfig.xml”`
3. Edite el esquema del archivo de configuración de red para que la subred de puerta de enlace sea /27 (o un prefijo más corto). Observe el ejemplo siguiente. Para obtener más información sobre cómo trabajar con el archivo de configuración de red, consulte [Configuración de una red virtual con un archivo de configuración de red](../virtual-network/virtual-networks-using-network-configuration-file.md). Para obtener más información sobre el esquema de configuración, consulte [Esquema de configuración de red virtual de Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).


          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.17.159.224/27</AddressPrefix>
          </Subnet>
4. Si la puerta de enlace anterior era una VPN sitio a sitio, también debe cambiar el tipo de conexión a **Dedicado**.

		         <Gateway>
		          <ConnectionsToLocalNetwork>
		            <LocalNetworkSiteRef name="MyLocalNetwork">
		              <Connection type="Dedicated" />
		            </LocalNetworkSiteRef>
		          </ConnectionsToLocalNetwork>
		        </Gateway>
5. Ya tiene una red virtual sin puertas de enlace. Puede continuar con el **paso 3** del artículo [Creación de una red virtual nueva con conexiones coexistentes](#create-a-new-vnet-with-coexisting-connections) para crear nuevas puertas de enlace y completar las conexiones.



## Pasos siguientes
	
Más información acerca de ExpressRoute. Consulte [Información técnica de ExpressRoute](expressroute-introduction.md).

Más información acerca de las puertas de enlace de VPN. Consulte [Información acerca de las puertas de enlace de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

<!---HONumber=July15_HO2-->