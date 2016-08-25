<properties 
   pageTitle="Cómo conectar redes virtuales clásicas a redes virtuales de Resource Manager con PowerShell | Microsoft Azure"
   description="Aprenda a crear una conexión VPN entre redes virtuales clásicas y redes de Resource Manager mediante la Puerta de enlace de VPN y PowerShell"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-service-management,azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="cherylmc" />

# Conexión de redes virtuales a partir de diferentes modelos de implementación con PowerShell

> [AZURE.SELECTOR]
- [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)

Actualmente, Azure tiene dos modelos de administración: el clásico y el de Resource Manager (RM). Si lleva un tiempo usando Azure, es probable que tenga máquinas virtuales de Azure y roles de instancia que se ejecuten en una red virtual clásica. Es posible que sus máquinas virtuales e instancias de roles más recientes se estén ejecutando en una red virtual creada en Resource Manager. Este artículo le guiará a través de la conexión de redes virtuales clásicas a redes virtuales de Resource Manager para permitir que los recursos que se encuentran en modelos de implementación independientes se comuniquen entre sí mediante una conexión de puerta de enlace.

Puede crear una conexión entre redes virtuales que estén en diferentes suscripciones, en diferentes regiones y en diferentes modelos de implementación. También puede conectar redes virtuales que tengan ya conexiones a redes locales, siempre que la puerta de enlace con la que se hayan configurado sea dinámica o basada en ruta. Para más información acerca de las conexiones de red virtual a red virtual, consulte [P+F sobre conexiones de red virtual a red virtual](#faq) al final de este artículo.
[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## Antes de comenzar

Los siguientes pasos le guiarán a través de los valores necesarios para configurar una puerta de enlace dinámica o basada en ruta para cada red virtual y crear una conexión VPN entre las puertas de enlace. Esta configuración no admite puertas de enlace estáticas o basadas en directivas.

Antes de comenzar, compruebe lo siguiente:

 - Se han creado ambas redes virtuales.
 - Los intervalos de direcciones de las redes virtuales no se superponen entre sí ni con alguno de los intervalos de otras conexiones con las que puedan estar conectadas las puertas de enlace.
 - Tiene instalados los últimos cmdlets de PowerShell (versión 1.0.2 o posterior). Para obtener más información, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md). Asegúrese de instalar los cmdlets tanto de Service Management (SM) como de Resource Manager (RM).

### <a name="exampleref"></a>Configuración de ejemplo

Puede usar la configuración de ejemplo como referencia al usar los cmdlets de PowerShell en los pasos siguientes.

**Configuración de redes virtuales clásicas**

Nombre de red virtual = ClassicVNet <br> Ubicación = oeste de EE. UU. (West US) <br> Espacios de direcciones de la red virtual = 10.0.0.0/24 <br> Subnet-1 = 10.0.0.0/27 <br> GatewaySubnet = 10.0.0.32/29 <br> Nombre de red local = RMVNetLocal <br> GatewayType = DynamicRouting

**Configuración de redes virtuales de Resource Manager**

Nombre de red virtual = RMVNet <br> Grupo de recursos = RG1 <br> Espacios de direcciones IP de red virtual = 192.168.1.0/16 <br> Subnet -1 = 192.168.1.0/24 <br> GatewaySubnet = 192.168.0.0/26 <br> Ubicación = este de EE. UU. (East US) <br> Nombre de IP público de puerta de enlace = gwpip <br> Puerta de enlace de red local = ClassicVNetLocal <br> Nombre de puerta de enlace de red virtual = RMGateway <br> Configuración de direccionamiento IP de puerta de enlace = gwipconfig


## <a name="createsmgw"></a>Sección 1: configuración de la red virtual clásica

### Parte 1: exportación del archivo de configuración de red

1. Inicie sesión en su cuenta de Azure en la consola de PowerShell. El cmdlet le pedirá las credenciales de inicio de sesión para la cuenta de Azure. Después de iniciar la sesión, se descarga la configuración de la cuenta a fin de ponerla a disposición para Azure PowerShell. Va a usar los cmdlets de PowerShell de SM para completar esta parte de la configuración.

		Add-AzureAccount

2. Exporte el archivo de configuración de red de Azure mediante la ejecución del comando siguiente. Puede cambiar la ubicación del archivo que se va a exportar a una ubicación diferente si es necesario. Editará el archivo y lo importará a Azure.

		Get-AzureVNetConfig -ExportToFile c:\AzureNet\NetworkConfig.xml

3. Abra el archivo .xml que ha descargado para editarlo. Para obtener un ejemplo del archivo de configuración de red, consulte [Esquema de configuración de red virtual](https://msdn.microsoft.com/library/jj157100.aspx).
 

### Parte 2: comprobación de la subred de puerta de enlace

En el elemento **VirtualNetworkSites**, agregue una subred de puerta de enlace a la red virtual si no hay ya una creada. Al trabajar con el archivo de configuración de red, la puerta de enlace DEBE tener el nombre "GatewaySubnet" o Azure no podrá reconocerla y utilizarla como subred de puerta de enlace.
	
    <VirtualNetworkSites>
      <VirtualNetworkSite name="ClassicVNet" Location="West US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/24</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Subnet-1">
            <AddressPrefix>10.0.0.0/27</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.0.32/29</AddressPrefix>
          </Subnet>
        </Subnets>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
       
### Parte 3: incorporación de sitios de redes locales

El sitio de red local que agregue representa la red virtual de RM a la que desea conectarse. Es posible que deba agregar un elemento **LocalNetworkSites** al archivo si aún no existe. En este punto de la configuración, el valor de VPNGatewayAddress puede ser cualquier dirección IP pública válida ya que todavía no se ha creado la puerta de enlace para la red virtual de Resource Manager. Una vez creada la puerta de enlace, esta dirección IP de marcador de posición se reemplaza por la dirección IP pública correcta que se ha asignado a la puerta de enlace de RM.

    <LocalNetworkSites>
      <LocalNetworkSite name="RMVNetLocal">
        <AddressSpace>
          <AddressPrefix>192.168.1.0/16</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
      </LocalNetworkSite>
    </LocalNetworkSites>

### Parte 4: asociación de la red virtual con el sitio de red local

En esta sección, se especifica el sitio de red local con la que se desea conectar la red virtual. En este caso, es la red virtual de Resource Manager a la que se ha hecho referencia anteriormente. Asegúrese de que los nombres coincidan. En este paso no se crea una puerta de enlace. Se especifica la red local con la que se conectará la puerta de enlace.

		<Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="RMVNetLocal">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>

### Parte 5: guardar el archivo y cargar

Ejecute el comando siguiente para guardar el archivo e importarlo a Azure. Asegúrese de cambiar la ruta de acceso según sea necesario para su entorno.

		Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\ClassicVNet.xml

Debería ver algo parecido a este resultado en el que se muestra que la importación se realizó correctamente.

		OperationDescription        OperationId                      OperationStatus                                                
		--------------------        -----------                      ---------------                                                
		Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 

### Parte 6: creación de la puerta de enlace de red virtual 

Puede crear la puerta de enlace de red virtual con el portal clásico o mediante PowerShell. Actualmente, no es posible crear una puerta de enlace clásica en el Portal de Azure.

#### Creación de la puerta de enlace en el portal clásico

1. En el [Portal clásico](https://manage.windowsazure.com), vaya a **Redes** y haga clic en la red virtual clásica para la que desea crear una puerta de enlace de red virtual. Se abrirá la página principal de la red virtual.
2. Haga clic en **Panel** en la parte superior de la página para cambiar a la página Panel.
3. En la parte inferior de la página Panel, haga clic en **Crear puerta de enlace** y seleccione **Enrutamiento dinámico**.
4. Haga clic en **Sí** para empezar a crear la puerta de enlace.
5. Espere hasta que se cree la puerta de enlace. Esta operación a veces puede tardar 45 minutos, o incluso más, en completarse.
6. Una vez que se haya creado la puerta de enlace, puede ver su dirección IP en la página **Panel**. Se trata de la dirección IP pública de la puerta de enlace. Escriba o copie la dirección IP pública, ya que se utilizará en pasos posteriores al crear la red local para la configuración de la red virtual de Resource Manager.
7. Opcionalmente, puede usar este cmdlet para recuperar la configuración de la puerta de enlace y comprobar su estado: `Get-AzureVirtualNetworkGateway`

#### Creación de la puerta de enlace mediante PowerShell

Para crear la puerta de enlace mediante PowerShell, use el ejemplo siguiente.

	New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting


## <a name="creatermgw"></a>Sección 2: configuración de la puerta de enlace de la red virtual de RM

Para crear una puerta de enlace de VPN para la red virtual de RM, siga estas instrucciones. No empiece con los pasos hasta que haya recuperado la dirección IP pública de la puerta de enlace de la red virtual clásica.

1. **Inicie sesión en su cuenta de Azure** en la consola de PowerShell. El siguiente cmdlet pide las credenciales de inicio de sesión de la cuenta de Azure. Después de iniciar la sesión, se descarga la configuración de la cuenta para que esté disponible para Azure PowerShell.

		Login-AzureRmAccount 

 	Si tiene más de una suscripción de Azure, obtenga una lista de todas ellas.

		Get-AzureRmSubscription

	Especifique la suscripción que desea usar.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2.  **Cree una puerta de enlace de red local**. En una red virtual, la puerta de enlace de red local suele hacer referencia a la ubicación local. En este caso, la puerta de enlace de red local hace referencia a la red virtual clásica. Asígnele un nombre que sirva de referencia a Azure y especifique también el prefijo de espacio de direcciones. Azure usa el prefijo de dirección IP que especifique para identificar qué tráfico enviar a la ubicación local. Si necesita ajustar la información aquí posteriormente, antes de crear la puerta de enlace, puede modificar los valores y volver a ejecutar el ejemplo.<br><br>
	- `-Name` es el nombre de referencia que desea asignar a la puerta de enlace de la red local.<br>
	- `-AddressPrefix` es el espacio de direcciones para la red virtual clásica.<br>
	- `-GatewayIpAddress` es la dirección IP pública de la puerta de enlace de la red virtual clásica. Asegúrese de cambiar el ejemplo siguiente para que refleje la dirección IP correcta.
	
			New-AzureRmLocalNetworkGateway -Name ClassicVNetLocal `
			-Location "West US" -AddressPrefix "10.0.0.0/24" `
			-GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1

3. **Solicite que se asigne una dirección IP pública** a la puerta de enlace de VPN de la red virtual de Azure Resource Manager. No puede especificar la dirección IP que desea usar. Se asigna dinámicamente a la puerta de enlace. Sin embargo, esto no significa que la dirección IP pueda cambiar. La única vez que cambia la dirección IP de la puerta de enlace de VPN de Azure es cuando se elimina y se vuelve a crear la puerta de enlace. No cambiará al modificar el tamaño, restablecer o realizar otro tipo de mantenimiento interno o actualizaciones de la puerta de enlace de VPN de Azure.<br>En este paso, también se establece una variable que se utilizará en un paso posterior.


		$ipaddress = New-AzureRmPublicIpAddress -Name gwpip `
		-ResourceGroupName RG1 -Location 'EastUS' `
		-AllocationMethod Dynamic

4. **Compruebe que la red virtual tenga una subred de puerta de enlace**. Si no existe ninguna subred de puerta de enlace, agregue una. Asegúrese de que la subred de puerta de enlace tenga el nombre *GatewaySubnet*.

5. **Recupere la subred** usada para la puerta de enlace mediante la ejecución del comando siguiente. En este paso, también se establece una variable que se utilizará en el paso siguiente.
	- `-Name` es el nombre de la red virtual de Resource Manager.
	- `-ResourceGroupName` es el grupo de recursos con el que está asociado la red virtual. La subred de puerta de enlace debe existir para esta red virtual y su nombre tiene que ser *GatewaySubnet* para que funcione correctamente.


			$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet `
			-VirtualNetwork (Get-AzureRmVirtualNetwork -Name RMVNet -ResourceGroupName RG1) 

6. **Cree la configuración de direccionamiento IP de la puerta de enlace**. La configuración de puerta de enlace define la subred y la dirección IP pública. Utilice el ejemplo siguiente para crear la configuración de puerta de enlace.<br>En este paso, los parámetros `-SubnetId` y `-PublicIpAddressId` deben recibir la propiedad id desde la subred y desde los objetos de dirección IP, respectivamente. No puede usar una cadena sencilla. Estas variables se establecen en el paso para solicitar una dirección IP pública y el paso para recuperar la subred.

		$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig `
		-Name gwipconfig -SubnetId $subnet.id `
		-PublicIpAddressId $ipaddress.id
	
7. **Cree la puerta de enlace de la red virtual de Resource Manager** mediante la ejecución del comando siguiente. `-VpnType` debe ser *RouteBased*.

		New-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
		-Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
		-IpConfigurations $gwipconfig `
		-EnableBgp $false -VpnType RouteBased

8. **Copie la dirección IP pública** una vez creada la puerta de enlace de VPN. Utilícela cuando configure la red local para la red virtual implementada con el modelo clásico. Puede utilizar el siguiente cmdlet para recuperar la dirección IP pública. La dirección IP pública aparece devuelta como *IpAddress*.

		Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName RG1

## Sección 3: modificación de la red local para la red virtual clásica

Para realizar este paso, puede exportar el archivo de configuración de red, editarlo y guardarlo e importarlo de nuevo a Azure. O bien, puede modificar esta configuración en el portal clásico.

### Modificación en el portal

1. Abra el [Portal clásico](https://manage.windowsazure.com).

2. En el portal clásico, desplácese hacia la izquierda y haga clic en **Redes**. En la página de **redes**, haga clic en **Redes locales** en la parte superior de la página.

3. En la página **Redes locales**, seleccione la red local que configuró en la parte 1, vaya a la sección inferior de la página y haga clic en **Editar**.

4. En la página **Especifique los datos de la red local**, reemplace la dirección IP del marcador de posición por la dirección IP pública de la puerta de enlace de Resource Manager que creó en la sección anterior. Haga clic en la flecha para pasar a la siguiente página. Compruebe que el **espacio de direcciones** es correcto y haga clic en la marca de verificación para aceptar los cambios.

### Modificación mediante el archivo de configuración de red

1. Exporte un archivo de configuración de red.
2. En un editor de texto, modifique la dirección IP de la puerta de enlace de VPN.

	    <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>

3. Guarde los cambios e importe el archivo editado a Azure.


## <a name="connect"></a>Sección 4: creación de una conexión entre las puertas de enlace

La creación de una conexión entre las puertas de enlace requiere PowerShell. Debe agregar su cuenta de Azure para usar los cmdlets de PowerShell clásicos. Puede utilizar el siguiente cmdlet:
		
	Add-AzureAccount

1. **Establezca la clave compartida** con la ejecución del siguiente comando. En este ejemplo, `-VNetName` es el nombre de la red virtual clásica y `-LocalNetworkSiteName` es el nombre especificado para la red local cuando se configura en el portal clásico. `-SharedKey` es un valor que puede generar y especificar. El valor que especifique aquí debe ser el mismo que va a especificar en el paso siguiente, cuando cree la conexión. La devolución para este ejemplo debe mostrar el estado de correcto.

		Set-AzureVNetGatewayKey -VNetName ClassicVNet `
		-LocalNetworkSiteName RMVNetLocal -SharedKey abc123

2. Ejecute los comandos siguientes para crear la conexión de VPN.

	**Establecimiento de las variables**

		$vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
		$vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

	**Creación de la conexión**<br> Tenga en cuenta que `-ConnectionType` es IPsec, no Vnet2Vnet.
		
		New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
		-Location "East US" -VirtualNetworkGateway1 `
		$vnet02gateway -LocalNetworkGateway2 `
		$vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

3. Puede ver la conexión en cualquier portal o mediante el cmdlet `Get-AzureRmVirtualNetworkGatewayConnection`.

		Get-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1

## <a name="faq"></a>P+F sobre conexiones de red virtual a red virtual

Vea los detalles de preguntas más frecuentes para más información acerca de las conexiones de red virtual a red virtual.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

<!---HONumber=AcomDC_0810_2016--->