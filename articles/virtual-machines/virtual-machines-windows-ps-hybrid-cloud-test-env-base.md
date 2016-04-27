<properties 
	pageTitle="Entorno de prueba de nube híbrida | Microsoft Azure" 
	description="Aprenda a crear un entorno de nube híbrida para profesionales de TI o para pruebas de desarrollo, completo con una red local simplificada." 
	services="virtual-machines-windows" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines-windows" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="Windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/01/2016" 
	ms.author="josephd"/>

# Configuración de un entorno de nube híbrida para pruebas

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementación clásica.
 
En este tema se le guiará en el proceso de creación de un entorno de nube híbrida con Microsoft Azure para pruebas. Aquí está la configuración resultante.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph5.png)

Esto simula un entorno de producción real híbrido desde su ubicación en Internet. Consta de:

-  Una red local simplificada (la subred de red corporativa).
-  Una red virtual entre locales hospedada en Azure (TestVNET).
-  Una conexión VPN de sitio a sitio.
-  Un controlador de dominio secundario en la red virtual TestVNET.

Esta configuración proporciona una base y un punto de partida común desde el que puede:

-  Desarrollar y probar las aplicaciones en un entorno de nube híbrida.
-  Crear configuraciones de pruebas de los equipos, algunos en la subred de la red corporativa y otros dentro de la red virtual TestVNET, para cargas de trabajo de TI basadas en la nube híbrida.

Hay cinco fases principales para configurar este entorno de prueba de nube híbrida:

1.	Configuración de los equipos en la subred de la red corporativa.
2.	Configuración de RRAS1.
3.	Creación de la red virtual de Azure entre locales.
4.	Creación de la conexión VPN de sitio a sitio.
5.	Configuración de DC2. 

Si todavía no dispone de una suscripción de Azure, puede registrarse para obtener una cuenta gratuita en [Probar Azure](https://azure.microsoft.com/pricing/free-trial/). Si tiene una suscripción de MSDN o de Visual Studio, consulte [Crédito mensual de Azure para suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

>[AZURE.NOTE] Las máquinas virtuales y las puertas de enlace de redes virtuales en Azure incurren en un coste económico constante cuando se están ejecutando. Una puerta de enlace de VPN de Azure se implementa como un conjunto de dos máquinas virtuales de Azure. Para obtener más información, consulte [Precios: red virtual](https://azure.microsoft.com/pricing/details/virtual-network/). Para minimizar los costos derivados de ejecutar una Puerta de enlace de VPN, hay que crear el entorno de prueba y realizar las pruebas y demostraciones necesarias lo más rápido posible.

Esta configuración requiere una subred de prueba de hasta cuatro equipos conectados directamente a Internet mediante una dirección IP pública. Si no tiene estos recursos, también puede [configurar un entorno de nube híbrida simulado para pruebas](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md). El entorno de prueba de nube híbrida simulada solo requiere una suscripción de Azure.

## Fase 1: Configuración de los equipos en la subred Corpnet

Siga las instrucciones de la sección "Pasos para configurar la subred Corpnet" de la [Test Lab Guide: Configuración base para Windows Server 2012 R2](http://www.microsoft.com/download/details.aspx?id=39638) para configurar los equipos DC1, APP1 y CLIENT1 en una subred denominada Corpnet. **Esta subred debe estar aislada de la red de su organización, ya que se conectará directamente a Internet a través del equipo RRAS1.**

A continuación, inicie sesión en DC1 con las credenciales de CORP\\User1. Para configurar el dominio CORP para que los usuarios y equipos usen su controlador de dominio local para la autenticación, ejecute estos comandos desde un símbolo del sistema de Windows PowerShell con nivel de administrador.

	New-ADReplicationSite -Name "TestLab" 
	New-ADReplicationSite -Name "TestVNET"
	New-ADReplicationSubnet “Name "10.0.0.0/8" “Site "TestLab"
	New-ADReplicationSubnet “Name "192.168.0.0/16" “Site "TestVNET

Esta es su configuración actual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph1.png)
 
## Fase 2: Configuración de RRAS1

RRAS1 proporciona enrutamiento del tráfico y servicios de dispositivos VPN entre los equipos de la subred de la red corporativa y la red virtual TestVNET. RRAS1 debe tener dos adaptadores de red instalados.

En primer lugar, instale el sistema operativo en RRAS1.

1.	Inicie la instalación de Windows Server 2012 R2.
2.	Siga las instrucciones para completar la instalación, especificando una contraseña segura para la cuenta del administrador local. Inicie sesión con la cuenta del administrador local.
3.	Conecte RRAS1 a una red que tenga acceso a Internet y ejecute Windows Update para instalar las actualizaciones más recientes de Windows Server 2012 R2.
4.	Conecte un adaptador de red a la subred de la red corporativa y el otro directamente a Internet. RRAS1 puede estar situado detrás de un firewall de Internet, pero no debe estar detrás de un traductor de direcciones de red (NAT).

A continuación, configure las propiedades TCP/IP de RRAS1. Necesitará una configuración de dirección IP pública, incluyendo una dirección, una máscara de subred (o longitud de prefijo), y la puerta de enlace predeterminada y los servidores DNS de su proveedor de servicios de Internet (ISP). Necesitará la dirección IP pública de la fase 3.

Utilice estos comandos en un símbolo del sistema de Windows PowerShell con nivel de administrador en RRAS1. Antes de ejecutar estos comandos, introduzca los valores de las variables y quite los caracteres < and >. Puede obtener los nombres actuales de los adaptadores de red desde la pantalla del comando **Get-NetAdapter**.

	$corpnetAdapterName="<Name of the adapter attached to the Corpnet subnet>"
	$internetAdapterName="<Name of the adapter attached to the Internet>"
	[Ipaddress]$publicIP="<Your public IP address>"
	$publicIPpreflength=<Prefix length of your public IP address>
	[IPAddress]$publicDG="<Your ISP default gateway>"
	[IPAddress]$publicDNS="<Your ISP DNS server(s)>"
	Rename-NetAdapter -Name $corpnetAdapterName -NewName Corpnet
	Rename-NetAdapter -Name $internetAdapterName -NewName Internet
	New-NetIPAddress -InterfaceAlias "Internet" -IPAddress $publicIP -PrefixLength $publicIPpreflength “DefaultGateway $publicDG
	Set-DnsClientServerAddress -InterfaceAlias Internet -ServerAddresses $publicDNS
	New-NetIPAddress -InterfaceAlias "Corpnet" -IPAddress 10.0.0.2 -AddressFamily IPv4 -PrefixLength 24
	Set-DnsClientServerAddress -InterfaceAlias "Corpnet" -ServerAddresses 10.0.0.1
	Set-DnsClient -InterfaceAlias "Corpnet" -ConnectionSpecificSuffix corp.contoso.com
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Input" -Protocol ICMPv4
	New-NetFirewallRule -DisplayName "Allow ICMPv4-Output" -Protocol ICMPv4 -Direction Outbound
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_msclient
	Disable-NetAdapterBinding -Name "Internet" -ComponentID ms_server
	ping dc1.corp.contoso.com

En el último comando, compruebe que hay cuatro respuestas desde la dirección IP 10.0.0.1.

Esta es su configuración actual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph2.png)

## Fase 3: Creación de la red virtual de Azure entre locales

Inicie un símbolo del sistema de Azure PowerShell.

> [AZURE.NOTE] El siguiente comando establece el uso de Azure PowerShell 1.0 y versiones posteriores. Para más información, vea [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/).

Inicie sesión en su cuenta.

	Login-AzureRMAccount

Obtenga el nombre de la suscripción con el comando siguiente.

	Get-AzureRMSubscription | Sort SubscriptionName | Select SubscriptionName

Establezca su suscripción a Azure. Utilice la misma suscripción que usó para generar la configuración básica. Reemplace todo el contenido dentro de las comillas, incluidos los caracteres < and >, por los nombres correctos.

	$subscr="<subscription name>"
	Get-AzureRmSubscription –SubscriptionName $subscr | Select-AzureRmSubscription

Después, cree un grupo de recursos para el entorno de prueba híbrida.

Para determinar el nombre único del grupo de recursos, utilice este comando para enumerar los grupos de recursos existente.

	Get-AzureRMResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Cree un nuevo grupo de recursos con estos comandos.

	$rgName="<resource group name>"
	$locName="<an Azure location, such as West US>"
	New-AzureRMResourceGroup -Name $rgName -Location $locName

Las máquinas virtuales basadas en Administrador de recursos requieren una cuenta de almacenamiento basada en Administrador de recursos. Debe seleccionar un nombre único global para cada cuenta de almacenamiento que contenga solo letras minúsculas y números. Puede usar este comando para enumerar las cuentas de almacenamiento existentes.

	Get-AzureRMStorageAccount | Sort Name | Select Name

Cree una cuenta de almacenamiento con estos comandos.

	$rgName="<your new resource group name>"
	$locName="<the location of your new resource group>"
	$saName="<unique storage account name >"
	New-AzureRMStorageAccount -Name $saName -ResourceGroupName $rgName –Type Standard_LRS -Location $locName

Después, hay que crear la Red Virtual de Azure que hospedará el entorno de nube híbrida y protegerlo con un grupo de seguridad de red.

	$locShortName="<the location of your new resource group in lowercase with spaces removed, example: westus>"
	$gwSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "192.168.255.248/29"
	$vmSubnet=New-AzureRMVirtualNetworkSubnetConfig -Name "TestSubnet" -AddressPrefix "192.168.0.0/24"
	New-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName -Location $locName -AddressPrefix "192.168.0.0/16" -Subnet $gwSubnet,$vmSubnet -DNSServer "10.0.0.1"
	$rule1=New-AzureRMNetworkSecurityRuleConfig -Name "RDPTraffic" -Description "Allow RDP to all VMs on the subnet" -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389
	New-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName -Location $locShortName -SecurityRules $rule1
	$vnet=Get-AzureRMVirtualNetwork -ResourceGroupName $rgName -Name "TestVNET"
	$nsg=Get-AzureRMNetworkSecurityGroup -Name "TestSubnet" -ResourceGroupName $rgName
	Set-AzureRMVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name TestSubnet -AddressPrefix "192.168.0.0/24" -NetworkSecurityGroup $nsg

Use estos comandos para crear las puertas de enlace para la conexión VPN de sitio a sitio. Necesitará la dirección IP pública de la interfaz de Internet de RRAS1 de la fase 2.

	$localGatewayIP="<the public IP address assigned to the Internet interface of RRAS1>"
	$vnet=Get-AzureRMVirtualNetwork -Name TestVNET -ResourceGroupName $rgName
	
	# Attach a virtual network gateway to a public IP address and the gateway subnet
	$publicGatewayVipName="HybCloudPublicIPAddress"
	$vnetGatewayIpConfigName="HybCloudPublicIPConfig"
	New-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$publicGatewayVip=Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName
	$vnetGatewayIpConfig=New-AzureRMVirtualNetworkGatewayIpConfig -Name $vnetGatewayIpConfigName -PublicIpAddressId $publicGatewayVip.Id -SubnetId $vnet.Subnets[0].Id
	
	# Create the Azure gateway
	$vnetGatewayName="HybCloudAzureGateway"
	$vnetGateway=New-AzureRMVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $rgName -Location $locName -GatewayType Vpn -VpnType RouteBased -IpConfigurations $vnetGatewayIpConfig
	
	# Create the gateway for the local network
	$localGatewayName="HybCloudLocalNetGateway"
	$localNetworkPrefix="10.0.0.0/8"
	$localGateway=New-AzureRMLocalNetworkGateway -Name $localGatewayName -ResourceGroupName $rgName -Location $locName -GatewayIpAddress $localGatewayIP -AddressPrefix $localNetworkPrefix

Tenga en cuenta que las puertas de enlace nuevas pueden tardar 20 minutos o más en crearse.

Luego, use el siguiente comando para determinar la dirección IP pública de la Puerta de enlace de VPN para la red virtual.

	Get-AzureRMPublicIpAddress -Name $vnetGatewayIpConfigName -ResourceGroupName $rgName

Anote la dirección IP que aparece en el campo **IPAddress**. La necesitará en la fase 4.

Tras ello, solicite una clave previamente compartida aleatoria y criptográficamente segura de 32 caracteres al administrador de red o de seguridad. Como alternativa, use la información que encontrará en [Create a random string for an IPsec preshared key](http://social.technet.microsoft.com/wiki/contents/articles/32330.create-a-random-string-for-an-ipsec-preshared-key.aspx) (Creación de una cadena aleatoria para una clave previamente compartida IPsec) para obtener una clave previamente compartida.

Use estos comandos para crear la conexión VPN de sitio a sitio en Azure.

	# Define the Azure virtual network VPN connection
	$vnetConnectionKey="<32-character pre-shared key>"
	$vnetConnectionName="HybCloudS2SConnection"
	$vnetConnection=New-AzureRMVirtualNetworkGatewayConnection -Name $vnetConnectionName -ResourceGroupName $rgName -Location $locName -ConnectionType IPsec -SharedKey $vnetConnectionKey -VirtualNetworkGateway1 $vnetGateway -LocalNetworkGateway2 $localGateway

Se trata de la configuración actual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph3.png)
 
## Fase 4: Creación de la conexión VPN de sitio a sitio

En primer lugar, configure RRAS1 con el servicio de acceso remoto y enrutamiento para que actúe como el dispositivo VPN de la subred de la red corporativa. Inicie sesión en RRAS1 como administrador local y ejecute estos comandos en un símbolo del sistema de Windows PowerShell.

	Import-Module ServerManager
	Install-WindowsFeature RemoteAccess -IncludeManagementTools
	Add-WindowsFeature -name Routing -IncludeManagementTools

Después, configure RRAS1 para recibir la conexión VPN de sitio a sitio desde la puerta de enlace VPN de Azure. Reinicie RRAS1 e inicie sesión como administrador local y ejecute estos comandos en un símbolo del sistema de Windows PowerShell. Hay que proporcionar la dirección IP de la Puerta de enlace de VPN de Azure y el valor de la clave precompartida de la fase 3.

	$PresharedKey="<32-character pre-shared key>"
	Import-Module RemoteAccess
	Install-RemoteAccess -VpnType VpnS2S
	Add-VpnS2SInterface -Protocol IKEv2 -AuthenticationMethod PSKOnly -Persistent -NumberOfTries 3 -ResponderAuthenticationMethod PSKOnly -Name S2StoTestVNET -Destination "<public IP address of the Azure VPN gateway>" -IPv4Subnet @("192.168.0.0/24:100") -SharedSecret $PresharedKey
	Set-VpnServerIPsecConfiguration -EncryptionType MaximumEncryption
	Set-VpnServerIPsecConfiguration -SADataSizeForRenegotiationKilobytes 33553408
	New-ItemProperty -Path HKLM:\System\CurrentControlSet\Services\RemoteAccess\Parameters\IKEV2 -Name SkipConfigPayload -PropertyType DWord -Value 1
	Restart-Service RemoteAccess

Deje pasar unos minutos para que la conexión entre RRAS1 y la Puerta de enlace de VPN de Azure se establezca.

A continuación, configure RRAS1 para que admita tráfico traducido para ubicaciones de Internet. En RRAS1:

1.	En la pantalla de inicio, escriba **rras** y haga clic en **Enrutamiento y acceso remoto**. 
2.	En el árbol de consola, abra el nombre del servidor y haga clic en **IPv4**.
3.	Haga clic con el botón derecho en **General** y, a continuación, haga clic en **Nuevo protocolo de enrutamiento**.
4.	Haga clic en **NAT** y, a continuación, en **Aceptar**.
5.	En el árbol de consola, haga clic con el botón derecho en **NAT**, haga clic en **Nueva interfaz**, en **Corpnet** y, a continuación, en **Aceptar** dos veces.
6.	Haga clic con el botón derecho en **NAT**, haga clic en **Nueva interfaz**, haga clic en **Internet** y, a continuación, en **Aceptar**.
7.	En la pestaña **NAT**, haga clic en **Interfaz pública conectada a Internet**, seleccione **Habilitar NAT en esta interfaz** y, a continuación, haga clic en **Aceptar**.

Después, configure DC1, APP1 y CLIENT1 para utilizar RRAS1 como puerta de enlace predeterminada.
 
En DC1, ejecute estos comandos en un símbolo del sistema de Windows PowerShell con nivel de administrador.

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2
	Set-DhcpServerv4OptionValue -Router 10.0.0.2

Si el nombre de la interfaz no es Ethernet, use el comando **Get-NetAdapter** para determinar el nombre de interfaz.

En APP1, ejecute este comando en un símbolo del sistema de Windows PowerShell con nivel de administrador.

	New-NetRoute -DestinationPrefix "0.0.0.0/0" -InterfaceAlias "Ethernet" -NextHop 10.0.0.2

En CLIENT1, ejecute este comando en un símbolo del sistema de Windows PowerShell con nivel de administrador.

	ipconfig /renew

Esta es su configuración actual.
 

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph4.png)


## Fase 5: Configuración de DC2

En primer lugar, cree una máquina virtual de Azure de DC2 con estos comandos en el símbolo del sistema de Azure PowerShell en el equipo local.

	$rgName="<your resource group name>"
	$locName="<your Azure location, such as West US>"
	$saName="<your storage account name>"
	
	$vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
	$subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
	$pip=New-AzureRMPublicIpAddress -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRMNetworkInterface -Name DC2-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip -PrivateIpAddress 192.168.0.4
	$vm=New-AzureRMVMConfig -VMName DC2 -VMSize Standard_A1
	$storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestVNET-ADDSDisk.vhd"
	Add-AzureRMVMDataDisk -VM $vm -Name ADDS-Data -DiskSizeInGB 20 -VhdUri $vhdURI  -CreateOption empty
	$cred=Get-Credential -Message "Type the name and password of the local administrator account for DC2."
	$vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName DC2 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/DC2-TestLab-OSDisk.vhd"
	$vm=Set-AzureRMVMOSDisk -VM $vm -Name DC2-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Luego, use el Portal de Azure para conectarse a la nueva máquina virtual de DC2 con las credenciales de la cuenta de administrador local.

A continuación, configure una regla del Firewall de Windows para permitir el tráfico para probar la conectividad básica. Desde un símbolo del sistema de Windows PowerShell con nivel de administrador en DS2, ejecute:

	Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
	ping dc1.corp.contoso.com

El comando ping debería producir cuatro respuestas correctas desde la dirección IP 10.0.0.1. Si usa la *configuración de nube híbrida simulada*, verá cuatro respuestas correctas desde la dirección IP 10.0.0.4. Esto es una prueba del tráfico a través de la conexión VPN de sitio a sitio o la conexión VNet a VNet.

A continuación, agregue un disco de datos adicional como un nuevo volumen con la letra de unidad F:.

1.	En el panel izquierdo del Administrador de servidores, haga clic en **Servicios de archivos y almacenamiento** y, a continuación, haga clic en **Discos**.
2.	En el panel de contenido, en el grupo **Discos**, haga clic en **disco 2** (con la **partición** establecida en **Desconocida**).
3.	Haga clic en **Tareas** y, a continuación, haga clic en **Nuevo volumen**.
4.	En la página Antes de empezar del Asistente para volumen nuevo, haga clic en **Siguiente**.
5.	En la página Selección del servidor y del disco, haga clic en **Disco 2** y, a continuación, haga clic en **Siguiente**. Cuando se le solicite, haga clic en **Aceptar**.
6.	En la página Especificación del tamaño de la página de volumen, haga clic en **Siguiente**.
7.	En la página Asignación a una letra de unidad o carpeta, haga clic en **Siguiente**.
8.	En la página Selección de la configuración del sistema de archivos, haga clic en **Siguiente**.
9.	En la página Confirmación de las selecciones, haga clic en **Crear**.
10.	Cuando haya terminado, haga clic en **Cerrar**.

A continuación, configure DC2 como un controlador de dominio de réplica para el dominio corp.contoso.com. Ejecute estos comandos desde el símbolo del sistema de Windows PowerShell en DC2.

	Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
	Install-ADDSDomainController -Credential (Get-Credential CORP\User1) -DomainName "corp.contoso.com" -InstallDns:$true -DatabasePath "F:\NTDS" -LogPath "F:\Logs" -SysvolPath "F:\SYSVOL"

Tenga en cuenta que se le pedirá que proporcione la contraseña de CORP\\User1 y una contraseña de Modo de restauración de servicios de directorio (DSRM), y que reinicie DC2.

Ahora que la red virtual TestVNET tiene su propio servidor DNS (DC2), debe configurar la red virtual de TestVNET para utilizar este servidor DNS.

1.	En el panel izquierdo del Portal de Azure, haga clic en el icono de las redes virtuales y elija **TestVNET**.
2.	En la pestaña **Configuración**, haga clic en **Servidores DNS**.
3.	En **Servidor DNS principal**, escriba **192.168.0.4** para sustituir el valor 10.0.0.4.
4.	Haga clic en Guardar.

Después, reinicie DC2 para que use la nueva configuración de servidor DNS con estos comandos en el símbolo del sistema de Azure PowerShell.

	Stop-AzureRmVM -ResourceGroupName $rgName -Name DC2
	Start-AzureRmVM -ResourceGroupName $rgName -Name DC2

Se trata de la configuración actual.

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-base/virtual-machines-windows-ps-hybrid-cloud-test-env-base-ph5.png)

Su entorno de nube híbrida ya está listo para las pruebas.
 
## Pasos siguientes

- Configure una [granja de intranet de SharePoint](virtual-machines-windows-ps-hybrid-cloud-test-env-sp.md), una [aplicación de LOB basada en web](virtual-machines-windows-ps-hybrid-cloud-test-env-lob.md) o un [servidor de sincronización de directorios (DirSync) de Office 365](virtual-machines-windows-ps-hybrid-cloud-test-env-dirsync.md) en este entorno.

<!---HONumber=AcomDC_0413_2016-->