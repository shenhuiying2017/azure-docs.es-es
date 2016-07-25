<properties 
   pageTitle="Configuración de la tunelización forzada para las conexiones de sitio a sitio mediante el modelo de implementación clásica | Microsoft Azure"
   description="Cómo redirigir o forzar todo el tráfico vinculado a Internet a la ubicación local."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="cherylmc" />

# Configuración de la tunelización forzada mediante el modelo de implementación clásica

> [AZURE.SELECTOR]
- [PowerShell: administración de servicios](vpn-gateway-about-forced-tunneling.md)
- [PowerShell: administrador de recursos](vpn-gateway-forced-tunneling-rm.md)

La tunelización forzada permite redirigir o forzar todo el tráfico vinculado a Internet de vuelta a su ubicación local a través de un túnel VPN de sitio a sitio para inspección y auditoría. Se trata de un requisito de seguridad crítico en la mayoría de las directivas de las empresas de TI.

Sin la tunelización forzada, el tráfico vinculado a Internet desde las máquinas virtuales en Azure siempre atravesará desde la infraestructura de red de Azure directamente a Internet, sin la opción que permite inspeccionar o auditar el tráfico. Un acceso no autorizado a Internet puede provocar la divulgación de información u otros tipos de infracciones de seguridad.

Este artículo le guiará a través del proceso de configuración de la tunelización forzada para redes virtuales creadas mediante el modelo de implementación clásica.

**Información sobre los modelos de implementación de Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

**Modelos de implementación y herramientas para la tunelización forzada**

Ambos modelos de implementación permiten configurar una conexión de tunelización forzada mediante distintas herramientas. Consulte la tabla siguiente para obtener más información. Esta tabla se actualiza cada vez que hay nuevos artículos, nuevos modelos de implementación y nuevas herramientas disponibles para esta configuración. Cuando aparezca un artículo, creamos un vínculo directo a él desde la tabla.

[AZURE.INCLUDE [vpn-gateway-forcedtunnel](../../includes/vpn-gateway-table-forcedtunnel-include.md)]


## Requisitos y consideraciones

La tunelización forzada en Azure se configura a través de rutas definidas por el usuario (UDR) de redes virtuales. La redirección del tráfico a un sitio local se expresa como una ruta predeterminada a la puerta de enlace de VPN de Azure. La sección siguiente muestra la limitación actual de la tabla de enrutamiento y las rutas de una red virtual de Azure:


-  Cada subred de la red virtual tiene una tabla de enrutamiento del sistema integrada. La tabla de enrutamiento del sistema tiene los siguientes tres grupos de rutas:

	- **Rutas de redes virtuales locales**: directamente a las máquinas virtuales de destino en la misma red virtual.
	
	- **Rutas locales**: a la puerta de enlace de VPN.
	
	- **Ruta predeterminada**: directamente a Internet. Tenga en cuenta que los paquetes destinados a las direcciones IP privadas que no están cubiertos por las dos rutas anteriores se anularán.


-  Con la liberación de las rutas definidas por el usuario, puede crear una tabla de enrutamiento para agregar una ruta predeterminada y, después, asociar la tabla de enrutamiento a las subredes de la red virtual para habilitar la tunelización forzada en esas subredes.

- Deberá establecer un "sitio predeterminado" entre los sitios locales entre entornos conectados a la red virtual.

- La tunelización forzada debe asociarse a una red virtual que tiene una puerta de enlace de VPN de enrutamiento dinámico (no una puerta de enlace estática).
 
- La tunelización forzada ExpressRoute no se configura mediante este mecanismo, sino que se habilita mediante el anuncio de una ruta predeterminada a través de las sesiones de emparejamiento BGP de ExpressRoute. Consulte la [Documentación de ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) para más información.



## Información general sobre la configuración

En el ejemplo anterior, la subred Frontend no usa la tunelización forzada. Las cargas de trabajo de la subred Frontend pueden continuar para aceptar y responder a las solicitudes de los clientes directamente desde Internet. Las subredes Mid-tier y Backend usan la tunelización forzada. Las conexiones salientes desde estas dos subredes a Internet se fuerzan o redirigen a un sitio local a través de uno de los túneles VPN de sitio a sitio.

Esto permite restringir e inspeccionar el acceso a Internet desde sus máquinas virtuales o servicios en la nube en Azure, al tiempo que continúa posibilitando la arquitectura de varios niveles de servicio necesaria. También tiene la opción de aplicar la tunelización forzada a redes virtuales completas si no hay ninguna carga de trabajo a través de Internet en las redes virtuales.


![Tunelización forzada](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)



## Antes de empezar

Antes de comenzar con la configuración, comprueba que dispones de los elementos siguientes:

- Una suscripción de Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) o registrarse para obtener una [cuenta gratis](https://azure.microsoft.com/pricing/free-trial/).

- Una red virtual configurada.

- Versión más reciente de los cmdlets de Azure PowerShell. Consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) para obtener más información sobre cómo instalar los cmdlets de PowerShell.


## Configuración de la tunelización forzada

El siguiente procedimiento le ayudará a especificar la tunelización forzada en una red virtual. Los pasos de configuración corresponden al archivo de configuración de red de la red virtual (netcfg) que aparece a continuación.



	<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
	</VirtualNetworkSite>

En el ejemplo, la red virtual "MultiTier-VNet" tiene tres subredes: *Frontend*, *Midtier* y *Backend*, con cuatro conexiones entre entornos: *DefaultSiteHQ* y tres *ramas*.

Los pasos del procedimiento establecerán *DefaultSiteHQ* como la conexión de sitio predeterminada para la tunelización forzada y configurarán las subredes Midtier y Backend para que usen dicha tunelización.


1. Cree una tabla de enrutamiento. Use el siguiente cmdlet para crear la tabla de enrutamiento.

		New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"

2. Agregue una ruta predeterminada a la tabla de enrutamiento.

	El siguiente ejemplo de cmdlet agrega una ruta predeterminada a la tabla de enrutamiento que creó en el paso 1. Tenga en cuenta que la única ruta admitida es el prefijo de destino de "0.0.0.0/0" para el siguiente salto "VPNGateway".
 
		Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway

3. Asocie la tabla de enrutamiento a las subredes.

	Una vez creada una tabla de enrutamiento y agregada una ruta, use el cmdlet siguiente para agregar o asociar la tabla de enrutamiento a una subred de la red virtual. Los siguientes ejemplos agregan la tabla de enrutamiento de "MyRouteTable" a las subredes Midtier y Backend de VNet MultiTier-VNet.

		Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"

		Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"

4. Asigne un sitio predeterminado para la tunelización forzada.

	En el paso anterior, los scripts del cmdlet de ejemplo crean la tabla de enrutamiento y la tabla de enrutamiento asociada a dos de las subredes de la red virtual. El paso restante consiste en seleccionar un sitio local entre las conexiones de varios sitios de la red virtual como el sitio predeterminado o túnel.

		$DefaultSite = @("DefaultSiteHQ")
		Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"

## Cmdlets de PowerShell adicionales

### Para eliminar una tabla de enrutamiento

	Remove-AzureRouteTable -Name <routeTableName>

### Para mostrar una tabla de enrutamiento

	Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]

### Para eliminar una ruta de una tabla de enrutamiento

	Remove-AzureRouteTable –Name <routeTableName>

### Para quitar una ruta de una subred

	Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### Para mostrar la tabla de enrutamiento asociada a una subred
	
	Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>

### Para quitar un sitio predeterminado de una puerta de enlace de VPN de VNet

	Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>

<!---HONumber=AcomDC_0713_2016-->