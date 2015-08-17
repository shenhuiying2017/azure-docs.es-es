<properties 
   pageTitle="Configuración de Expressroute con NSP"
   description="Este tutorial le guiará por el proceso de configuración de ExpressRoute a través de NSP"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/29/2015"
   ms.author="cherylmc"/>

#  Configuración de una conexión ExpressRoute a través de un proveedor de servicios de red

Para configurar la conexión ExpressRoute a través de un proveedor de servicios de red, debe completar varios pasos en el orden correcto. Estas instrucciones le ayudarán a:

- Crear y administrar circuitos ExpressRoute
- Vincular la red virtual al circuito ExpressRoute

##  Requisitos previos de configuración


Antes de comenzar, compruebe que cumple los requisitos previos siguientes:

- Suscripción de Azure
- Versión más reciente de Windows PowerShell
- Los siguientes requisitos de red virtual:
	- Un conjunto de prefijos de dirección IP para usarlo en redes virtuales en Azure.
	- Un conjunto de prefijos IP locales (puede incluir direcciones IP públicas).
	- La puerta de enlace de red virtual debe crearse con una subred /28.
	- Conjunto adicional de prefijos de IP (/ 28) fuera de la red virtual. Se usará para configurar las rutas; tendrá que facilitarle esta información al proveedor de servicios de red.
	- Número del sistema autónomo (AS) de la red. Tendrá que facilitarle esta información al proveedor de servicios de red. Puede usar números de AS privados. Si decide hacerlo, debe ser > 65.000. Para obtener más información acerca de los números de AS, consulte Números de sistema autónomo (AS). 

- Del proveedor de servicios de red:
	- Debe tener un servicio VPN que sea compatible con ExpressRoute. Compruebe con su proveedor de servicios de red si su servicio VPN existente cumple los requisitos.

##  Flujo de trabajo de implementación

![Flujo de trabajo del proveedor de servicios de red](./media/expressroute-configuring-nsps/expressroute-nsp-connectivity-workflow.png)

##  Configuración de opciones mediante PowerShell
Windows PowerShell es un potente entorno de scripting que puede usar para controlar y automatizar la implementación y la administración de sus cargas de trabajo en Azure. Para obtener más información, consulte la documentación de PowerShell en [MSDN](https://msdn.microsoft.com/library/windowsazure/jj156055.aspx)



1. **Importe el módulo de PowerShell para ExpressRoute.**

		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\Azure.psd1'
		Import-Module 'C:\Program Files (x86)\Microsoft SDKs\Azure\PowerShell\ServiceManagement\Azure\ExpressRoute\ExpressRoute.psd1' 

2. **Obtenga la lista de proveedores, ubicaciones y anchos de banda admitidos.**

	Antes de crear un circuito, necesitará una lista de proveedores de servicios, ubicaciones admitidas y opciones de ancho de banda para cada ubicación. El siguiente cmdlet de PowerShell devuelve esta información, que usará en pasos posteriores.

		PS C:\> Get-AzureDedicatedCircuitServiceProvider

	La información devuelta tendrá un aspecto similar al siguiente ejemplo:

		PS C:\> Get-AzureDedicatedCircuitServiceProvider
	
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
		

3. **Realizar una solicitud para una clave de servicio y pasarla al proveedor de exchange.**

	Usará un cmdlet de PowerShell para realizar esta solicitud. En este ejemplo se usará AT&T Netbond como el proveedor de servicios y se especificará un circuito ExpressRoute de 50 Mbps en Silicon Valley. Si usa otro proveedor y otra configuración, sustituya la información al realizar la solicitud.

	A continuación se muestra un ejemplo de solicitud para una nueva clave de servicio:

		##Creating a new circuit
		$Bandwidth = 50
		$CircuitName = "NetBondSVTest"
		$ServiceProvider = "at&t"
		$Location = "Silicon Valley"
		
		New-AzureDedicatedCircuit -CircuitName $CircuitName -ServiceProviderName $ServiceProvider -Bandwidth $Bandwidth -Location $Location
		
		#Getting service key
		Get-AzureDedicatedCircuit

	La respuesta tendrá un aspecto similar al siguiente ejemplo:

		Bandwidth                        : 50
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : *********************************
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	Puede recuperar esta información en cualquier momento con el cmdlet Get-AzureCircuit. Si se realiza la llamada sin parámetros, se obtendrá una lista de todos los circuitos. La clave de servicio se mostrará en el campo ServiceKey.

		PS C:\> Get-AzureDedicatedCircuit
		
		Bandwidth                        : 500
		CircuitName                      : NetBondSVTest
		Location                         : Silicon Valley
		ServiceKey                       : 00-0000-0000-0000-0000000000
		ServiceProviderName              : at&t
		ServiceProviderProvisioningState : NotProvisioned
		Status                           : Enabled

	Cuando se complete este paso, tendrá conectividad al almacenamiento de Azure y a otros servicios.



4. **Configure la red virtual y la puerta de enlace.**

	Vea [Configuración de una red virtual y una puerta de enlace para ExpressRoute](https://msdn.microsoft.com/library/azure/dn643737.aspx). Tenga en cuenta que la subred de la puerta de enlace debe ser/28 para poder trabajar con una conexión ExpressRoute.

5. **Vincule la red a un circuito.**

	Continúe con los pasos siguientes solo después de haber confirmado que
 
	- ServiceProviderState: aprovisionado
	- Estado: habilitado

	Compruebe que tiene creada al menos una red virtual de Azure con una puerta de enlace. La puerta de enlace debe estar en ejecución.

		PS C:\> $Vnet = "MyTestVNet"
		New-AzureDedicatedCircuitLink -ServiceKey $ServiceKey -VNetName $Vnet
		
		Provisioned 

<!---HONumber=August15_HO6-->