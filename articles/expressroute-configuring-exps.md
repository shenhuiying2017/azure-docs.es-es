<properties 
   pageTitle="Configuración de ExpressRoute a través de un proveedor de intercambio"
   description="Este tutorial le guiará por el proceso de configuración de ExpressRoute a través de proveedores de intercambio (EXP)."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="04/29/2015"
   ms.author="cherylmc"/>

#  Configurar una conexión ExpressRoute a través de un proveedor de Exchange

Para configurar la conexión ExpressRoute a través de un proveedor de intercambio, debe completar varios pasos en el orden correcto. Al seguir instrucciones, completará las acciones siguientes:

- Crear y administrar circuitos ExpressRoute
- Configurar el intercambio de tráfico \(peering\) de BGP para circuitos ExpressRoute
- Vincular la red virtual al circuito ExpressRoute

##  Requisitos previos de configuración


Antes de comenzar, compruebe que cumple los requisitos previos siguientes:

- Suscripción de Azure 
- Versión más reciente de PowerShell de Azure 
- Los siguientes requisitos de red virtual: 
	- Un conjunto de prefijos de dirección IP para usar en redes virtuales en Azure
	- Un conjunto de prefijos IP locales de IP \(puede incluir direcciones IP públicas\)
	- La puerta de enlace de red virtual debe crearse con una subred /28.
	- Conjunto adicional de prefijos de IP \(/ 28\) fuera de la red virtual. Esto se usará para configurar el intercambio de tráfico de BGP.
	- Número del sistema autónomo \(AS\) de la red. Para obtener más información acerca de los números de AS, consulte [Números de sistema autónomo \(AS\)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml).
	- El hash MD5, si necesita una sesión autenticada en BGP
	- El identificador de la VLAN a la que se enviará el tráfico. Necesitará 2 identificadores de VLAN para cada circuito: uno para las redes virtuales y otro para los servicios hospedados en direcciones IP públicas.
	- [Sistema autónomo \(AS\)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml) de la red.
	- Dos conexiones cruzadas de 1 Gbps/10 Gbps a Ethernet Exchange del proveedor de intercambio.
	- Un par de enrutadores compatibles con BGP para el enrutamiento

##  Flujo de trabajo de implementación


![](./media/expressroute-configuring-exps/expressroute-exp-connectivity-workflow.png)

##  Configuración de opciones mediante PowerShell

Windows PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Para obtener más información, consulte la documentación de PowerShell en [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx).

1. **Importe el módulo de PowerShell para ExpressRoute.**

	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
	    Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1' 
	
2. **Obtenga la lista de proveedores, ubicaciones y anchos de banda admitidos.**

	Antes de crear un circuito, necesitará una lista de proveedores de servicios, ubicaciones admitidas y opciones de ancho de banda para cada ubicación. El siguiente cmdlet de PowerShell devuelve esta información, que usará en pasos posteriores.

    	PS C:\> Get-AzureDedicatedCircuitServiceProvider
		**The information returned will look similar to the example below:**
		
		
		Name                 DedicatedCircuitLocations      DedicatedCircuitBandwidths                                                                                                                                                                                   
		----                 -------------------------      --------------------------                                                                                                                                                                                   
		AT&T                 Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		British Telecom      London,Amsterdam               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Equinix              Amsterdam,Atlanta,Chicago,Dall 200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		                     as,New York,Seattle,Silicon                                                                                                                                                                                                                 
		                     Valley,Washington                                                                                                                                                                                                                           
		                     DC,London,Hong                                                                                                                                                                                                                              
		                     Kong,Singapore,Sydney,Tokyo                                                                                                                                                                                                                 
		IIJ                  Tokyo                          10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Level 3              London,Silicon                 200Mbps:200, 500Mbps:500, 1Gbps:1000                                                                                                                                                                         
		Communications -     Valley,Washington DC                                                                                                                                                                                                                        
		Exchange                                                                                                                                                                                                                                                         
		Level 3              London,Silicon                 10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Communications -     Valley,Washington DC                                                                                                                                                                                                                        
		IPVPN                                                                                                                                                                                                                                                            
		Orange               Amsterdam,London               10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel Domestic     Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		SingTel              Singapore                      10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		International                                                                                                                                                                                                                                                    
		TeleCity Group       Amsterdam,London               200Mbps:200, 500Mbps:500, 1Gbps:1000, 10Gbps:10000                                                                                                                                                           
		Telstra Corporation  Sydney                         10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000                                                                                                                                                   
		Verizon              Silicon Valley,Washington DC   10Mbps:10, 50Mbps:50, 100Mbps:100, 500Mbps:500, 1Gbps:1000
 
3. **Creación de un circuito dedicado**
 
	En el ejemplo siguiente se muestra cómo crear un circuito ExpressRoute de 200 Mbps a través de Equinix Silicon Valley. Si usa otro proveedor y otra configuración, sustituya la información al realizar la solicitud.

	A continuación se muestra un ejemplo de solicitud para una nueva clave de servicio:
      
		#Creating a new circuit
		$Bandwidth = 200
		$CircuitName = "EquinixSVTest"
		$ServiceProvider = "Equinix"
		$Location = "Silicon Valley"
		 
		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location
		 
		#Getting service key
		Get-AzureDedicatedCircuit
 
	La respuesta tendrá un aspecto similar al siguiente ejemplo:
            
		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	Puede recuperar esta información en cualquier momento con el cmdlet Get-AzureCircuit. Si se realiza la llamada sin parámetros, se obtendrá una lista de todos los circuitos. La clave de servicio se mostrará en el campo ServiceKey.

		PS C:\> Get-AzureDedicatedCircuit
				 
		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled


4. **Envíe la clave de servicio a su proveedor de intercambio.**

	El proveedor de intercambio usará la clave de servicio para habilitar su extremo de conexión. Tendrá que proporcionar el proveedor de conectividad con información adicional para habilitar la conectividad.

5. **Compruebe periódicamente el estado y la condición de la clave del circuito.**

	Esto le permitirá saber cuándo el proveedor ha habilitado el circuito. Una vez habilitado el circuito, el parámetro *ServiceProviderProvisioningState* aparecerá como *Aprovisionado*, tal como se muestra en el ejemplo siguiente.

		PS C:\> Get-AzureDedicatedCircuit
				 
		Bandwidth                        : 200
		CircuitName                      : EquinixSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : equinix
		ServiceProviderProvisioningState : Provisioned
		Status                           : Enabled
 
6. **Configure el enrutamiento para la red virtual.**

	Usamos sesiones de BGP para intercambiar rutas y asegurarnos de que contamos con disponibilidad alta. Siga el ejemplo que se muestra a continuación para crear una sesión de BGP para el circuito. Al crear la sesión, sustituya sus propios valores.

		#Setting up a bgp session
		$ServiceKey = "<your key>"
		$PriSN = "<subnet/30 you use IP #1 and Azure uses IP #2>"
		$SecSN = "<subnet/30 use IP #1 and Azure uses IP #2>"
		$ASN = <your ASN>
		$VLAN = <your vlan ID>
		 
		#Create a new bgp peering session
		New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN VlanId $VLAN –AccessType Private
		#Get BGP parameters and Azure ASN
		Get-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Private
		#Update BGP peering config
		Set-AzureBGPPeering  -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private
		#Removing BGP peering config
		Remove-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Private

	Puede obtener información de enrutamiento mediante Get-AzureBGPPeering proporcionando la clave del servicio. También puede actualizar la configuración de BGP mediante Set-AzureBGPPeering. La sesión de BGP no se iniciará cuando se ejecute este comando. El circuito debe vincularse con al menos una red virtual para iniciar la sesión de BGP.

	La respuesta siguiente le proporcionará la información que necesitará para los pasos siguientes. Use el ASN del mismo nivel para configurar BGP en los enrutamientos y reenvíos \(VRF\) del enrutador.
                    
		PS C:\> New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private
				
		AzureAsn            : 12076
		PeerAsn             : 65001
		PrimaryAzurePort    : EQIX-SJC-06GMR-CIS-1-PRI-A
		PrimaryPeerSubnet   : 10.0.1.0/30
		SecondaryAzurePort  : EQIX-SJC-06GMR-CIS-2-SEC-A
		SecondaryPeerSubnet : 10.0.2.0/30
		State               : Enabled
		VlanId              : 100

7. **Configure el enrutamiento para los servicios hospedados en direcciones IP públicas.**
	
	Usamos sesiones de BGP para intercambiar rutas y asegurarnos de que contamos con disponibilidad alta. Siga el ejemplo que se muestra a continuación para crear una sesión de BGP para el circuito. Al crear la sesión, sustituya sus propios valores.

		#Setting up a bgp session
		$ServiceKey = "<your key>"
		$PriSN = "<subnet/30 subnet you use IP #1 and Azure uses IP #2>"
		$SecSN = "< subnet/30 subnet you use IP #1 and Azure uses IP #2>"
		$ASN = <your ASN> 
		$VLAN = <your vlan ID>
		 
		#Create a new bgp peering session
		New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Public
		#Get BGP parameters and Azure ASN
		Get-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Public
		#Update BGP peering config
		Set-AzureBGPPeering  -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Public
		#Removing BGP peering config
		Remove-AzureBGPPeering -ServiceKey $ServiceKey –AccessType Public
 
	Puede obtener información de enrutamiento mediante Get-AzureBGPPeering proporcionando la clave del servicio. También puede actualizar la configuración de BGP mediante Set-AzureBGPPeering. La sesión de BGP no se iniciará cuando se ejecute este comando. El circuito debe vincularse con al menos una red virtual para iniciar la sesión de BGP.

	La respuesta siguiente le proporcionará la información que necesitará para los pasos siguientes. Use el ASN del mismo nivel para configurar BGP en los enrutamientos y reenvíos \(VRF\) del enrutador.

		PS C:\> New-AzureBGPPeering -ServiceKey $ServiceKey -PrimaryPeerSubnet $PriSN -SecondaryPeerSubnet $SecSN -PeerAsn $ASN -VlanId $VLAN –AccessType Private
		 
		AzureAsn            : 12076
		PeerAsn             : 65001
		PrimaryAzurePort    : EQIX-SJC-06GMR-CIS-1-PRI-A
		PrimaryPeerSubnet   : 10.0.1.8/30
		SecondaryAzurePort  : EQIX-SJC-06GMR-CIS-2-SEC-A
		SecondaryPeerSubnet : 10.0.2.8/30
		State               : Enabled
		VlanId              : 101
 
8. **Configure la red virtual y la puerta de enlace.**

	Vea [Configuración de una red virtual y una puerta de enlace para ExpressRoute](https://msdn.microsoft.com/library/azure/dn643737.aspx). Tenga en cuenta que la subred de la puerta de enlace debe ser/28 para poder trabajar con una conexión ExpressRoute.

9. **Vincule la red a un circuito.** Siga las instrucciones que se indican a continuación solo después de confirmar que el circuito ha pasado al estado y condición siguientes:
	- ServiceProviderProvisioningState: aprovisionado
	- Estado: habilitado
	 
			PS C:\> $Vnet = "MyTestVNet"
			New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
<!--HONumber=54-->