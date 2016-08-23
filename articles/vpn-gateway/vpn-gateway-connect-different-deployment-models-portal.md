<properties 
   pageTitle="Conexión de redes virtuales implementadas con el modelo clásico a redes virtuales con el modelo de Resource Manager en el portal | Microsoft Azure"
   description="Aprenda a crear una conexión VPN entre redes virtuales clásicas y redes de Resource Manager mediante la Puerta de enlace de VPN y el portal"
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

# Conexión a redes virtuales a partir de diferentes modelos de implementación del portal

> [AZURE.SELECTOR]
- [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
- [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)


Actualmente, Azure tiene dos modelos de administración: el clásico y el de Resource Manager (RM). Si lleva un tiempo usando Azure, es probable que tenga máquinas virtuales de Azure y roles de instancia que se ejecuten en una red virtual clásica. Es posible que sus máquinas virtuales e instancias de roles más recientes se estén ejecutando en una red virtual creada en Resource Manager. Este artículo le guiará a través de la conexión de redes virtuales clásicas a redes virtuales de Resource Manager para permitir que los recursos que se encuentran en modelos de implementación independientes se comuniquen entre sí mediante una conexión de puerta de enlace.

Puede crear una conexión entre redes virtuales que estén en diferentes suscripciones, en diferentes regiones y en diferentes modelos de implementación. También puede conectar redes virtuales que tengan ya conexiones a redes locales, siempre que la puerta de enlace con la que se hayan configurado sea dinámica o basada en ruta. Para más información acerca de las conexiones de red virtual a red virtual, consulte [P+F sobre conexiones de red virtual a red virtual](#faq) al final de este artículo.

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## Antes de comenzar

Los siguientes pasos le guiarán a través de los valores necesarios para configurar una puerta de enlace dinámica o basada en ruta para cada red virtual y crear una conexión VPN entre las puertas de enlace. Esta configuración no admite puertas de enlace estáticas o basadas en directivas. En este artículo se usan el portal clásico, el Portal de Azure y PowerShell. Actualmente, esta configuración no se puede crear solo con el Portal de Azure.

Antes de comenzar, compruebe lo siguiente:

 - Se han creado ambas redes virtuales.
 - Los intervalos de direcciones de las redes virtuales no se superponen entre sí ni con alguno de los intervalos de otras conexiones con las que puedan estar conectadas las puertas de enlace.
 - Tiene instalados los últimos cmdlets de PowerShell (versión 1.0.2 o posterior). Para obtener más información, vea [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md). Asegúrese de instalar los cmdlets tanto de Service Management (SM) como de Resource Manager (RM).

### <a name="values"></a>Configuración de ejemplo

La configuración de ejemplo puede usarla como referencia cuando utilice los cmdlets de PowerShell en los pasos siguientes.

**Configuración de redes virtuales clásicas**

Nombre de red virtual = ClassicVNet <br> Ubicación = Oeste de EE. UU. (West US)<br> Espacios de direcciones de la red virtual = 10.0.0.0/8 <br> Subnet-1 = 10.0.0.0/11 <br> GatewaySubnet = 10.32.0.0/29 <br> Nombre de red local = RMVNetLocal <br>

**Configuración de redes virtuales de Resource Manager**

Nombre de red virtual = RMVNet <br> Grupo de recursos = RG1 <br> Espacios de direcciones IP de red virtual = 192.168.1.0/16 <br> Subnet -1 = 192.168.1.0/24 <br> GatewaySubnet = 192.168.0.0/26 <br> Ubicación = Este de EE. UU. (East US) <br> Nombre de puerta de enlace de red virtual = RMGateway <br> Nombre de dirección IP pública de puerta de enlace = gwpip <br> Tipo de puerta de enlace = VPN <br> Tipo de VPN = Basada en ruta <br> Puerta de enlace de red local = ClassicVNetLocal <br>

## <a name="createsmgw"></a>Sección 1: configuración de la red virtual con el modelo clásico

En esta sección, se crean la red local y la puerta de enlace de la red virtual con el modelo clásico. Las instrucciones de esta sección utilizan el portal clásico. Actualmente, el Portal de Azure no ofrece todos los valores que pertenecen a una red virtual clásica.

### Parte 1: creación de una nueva red local

Abra el [portal clásico](https://manage.windowsazure.com) e inicie sesión con su cuenta de Azure.

1. En la esquina inferior izquierda de la pantalla, haga clic en **NUEVO** > **Servicios de red** > **Red virtual** > **Agregar red local**.

2. En la ventana **Especifique los datos de la red local**, escriba el nombre de la red virtual de RM a la que desee conectarse. En el cuadro **Dirección IP del dispositivo VPN (opcional)**, escriba cualquier dirección IP pública válida. Esto es solo un marcador de posición temporal. Esta dirección IP se cambiará más adelante. Haga clic en el botón de flecha de la esquina inferior derecha de la ventana.
 
3. En la página **Especifique el espacio de direcciones**, en el cuadro de texto **Dirección IP de inicio**, escriba el prefijo de red y el bloque de CIDR de la red virtual implementada con el modelo de Resource Manager a la que desea conectarse. Este valor se utiliza para especificar el espacio de direcciones para enrutar a la red virtual de RM.

### Parte 2: asociación de la red local a su red virtual

1. Haga clic en **Redes virtuales** en la parte superior de la página para cambiar a la pantalla Redes virtuales y, después, haga clic para seleccionar su red virtual clásica. En la página de su red virtual, haga clic en **Configurar** para navegare a la página de configuración.

2. En la sección de la configuración de la **conectividad de sitio a sitio**, active la casilla **Conectar con la red local**. A continuación, seleccione la red local que ha creado. Si ha creado varias redes locales, asegúrese de seleccionar en la lista desplegable la que creó para representar la VNet implementada con el modelo de Resource Manager.

3. Haga clic en **Guardar** en la parte inferior de la página.

### Parte 3: creación de la puerta de enlace

1. Después de guardar la configuración, haga clic en **Panel** en la parte superior de la página para cambiar a la página Panel. En la parte inferior de la página Panel, haga clic en **Crear puerta de enlace** y luego en **Enrutamiento dinámico**. Haga clic en **Sí** para empezar a crear la puerta de enlace. Para esta configuración es obligatorio usar una puerta de enlace con enrutamiento dinámico.

2. Espere hasta que se cree la puerta de enlace. Esta operación a veces puede tardar 45 minutos, o incluso más, en completarse.

### <a name="ip"></a>Parte 4: visualización la dirección IP pública de la puerta de enlace

Una vez que se haya creado la puerta de enlace, puede ver su dirección IP en la página **Panel**. Se trata de la dirección IP pública de la puerta de enlace. Escriba o copie la dirección IP pública, ya que se utilizará en pasos posteriores al crear la red local para la configuración de la red virtual de Resource Manager.


## <a name="creatermgw"></a>Sección 2: configuración de redes virtuales de Resource Manager

En esta sección se creará la puerta de enlace de la red virtual y la red local de la red virtual con el modelo de Resource Manager. No empiece a realizar los siguientes pasos hasta que haya recuperado la dirección IP pública de la puerta de enlace de la red virtual con la implementación mediante el modelo clásico.

Las capturas de pantalla se proporcionan a modo de ejemplos. Asegúrese de reemplazar los valores por los suyos. Si va a crear esta configuración como un ejercicio, haga referencia a estos [valores](#values).


### Parte 1: creación de una subred de la puerta de enlace

Antes de conectar la red virtual a una puerta de enlace, es preciso crear la subred de la puerta de enlace de la red virtual a la que desea conectarse. Crear una subred de puerta de enlace con un recuento CIDR de /28, o mayor (/27, /26, etc.)

Desde un explorador, navegue al [Portal de Azure](http://portal.azure.com) e inicie sesión con su cuenta de Azure.

[AZURE.INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### Parte 2: creación de una puerta de enlace de la red virtual


[AZURE.INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]


### Parte 3: creación de una puerta de enlace de la red local

La puerta de enlace de la red local suele hacer referencia a su ubicación local. Indica a Azure qué intervalos de direcciones IP se van a enrutar a la ubicación y la dirección IP pública del dispositivo de dicha ubicación. Sin embargo, en este caso, hace referencia al intervalo de direcciones y la dirección IP pública asociados a su red virtual clásica y a la puerta de enlace de la red virtual.

Asigne a la puerta de enlace de la red local un nombre a través del que Azure pueda hacer referencia a ella. Puede crear la puerta de enlace de la red local mientras se crea la puerta de enlace de la red virtual. Para esta configuración, utilice la dirección IP pública que se asignó a la puerta de enlace de la red virtual clásica en la [sección anterior](#ip).

[AZURE.INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


### Parte 4: copia de la dirección IP pública

Una vez que la puerta de enlace de la red virtual haya terminado de crearse, copie la dirección IP pública asociada a dicha puerta de enlace. Utilícela cuando configure la red local para la red virtual implementada con el modelo clásico.


## Sección 3: modificación de la red local para la red virtual clásica

Abra el [portal clásico](https://manage.windowsazure.com).

2. En el portal clásico, desplácese hacia la izquierda y haga clic en **Redes**. En la página de **redes**, haga clic en **Redes locales** en la parte superior de la página.

3. Haga clic para seleccionar la red local que configuró en la parte 1. En la parte inferior de la página, haga clic en **Edición**.

4. En la página **Especifique los datos de la red local**, reemplace la dirección IP del marcador de posición por la dirección IP pública de la puerta de enlace de Resource Manager que creó en la sección anterior. Haga clic en la flecha para pasar a la siguiente página. Compruebe que el **espacio de direcciones** es correcto y haga clic en la marca de verificación para aceptar los cambios.

## <a name="connect"></a>Sección 4: creación de la conexión

En esta sección, se crea la conexión entre las redes virtuales. Los pasos para hacerlo requieren PowerShell. Esta conexión no se puede crear en ninguno de los portales. Asegúrese de que ha descargado e instalado los cmdlets de PowerShell tanto del modelo clásico (SM) como del modelo de Resource Manager (RM).


1. Inicie sesión en su cuenta de Azure en la consola de PowerShell. El siguiente cmdlet pide las credenciales de inicio de sesión de la cuenta de Azure. Después de iniciar la sesión, se descarga la configuración de la cuenta para que esté disponible para Azure PowerShell.

		Login-AzureRmAccount 

 	Si tiene más de una suscripción de Azure, obtenga una lista de todas ellas.

		Get-AzureRmSubscription

	Especifique la suscripción que desea usar.

		Select-AzureRmSubscription -SubscriptionName "Name of subscription"


2. Agregue su cuenta de Azure para usar los cmdlets de PowerShell clásicos. Para ello puede utilizar el siguiente comando:

		Add-AzureAccount

3. Establezca la clave compartida con la ejecución del siguiente ejemplo. En este ejemplo, `-VNetName` es el nombre de la red virtual creada con el modelo clásico y `-LocalNetworkSiteName` es el nombre especificado para la red local cuando se configuró en el portal clásico. `-SharedKey` es un valor que puede generar y especificar. El valor que especifique aquí debe ser el mismo que va a especificar en el paso siguiente, cuando cree la conexión.

		Set-AzureVNetGatewayKey -VNetName ClassicVNet `
		-LocalNetworkSiteName RMVNetLocal -SharedKey abc123

4. Ejecute los comandos siguientes para crear la conexión de VPN.
	
	**Establecimiento de las variables**

		$vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
		$vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1

	**Creación de la conexión**<br> Tenga en cuenta que `-ConnectionType` es 'IPsec', no 'Vnet2Vnet'. En este ejemplo, `-Name` es el nombre que desea asignar a la conexión. En el ejemplo siguiente se crea una conexión llamada '*rm-to-classic-connection*'.
		
		New-AzureRmVirtualNetworkGatewayConnection -Name rm-to-classic-connection -ResourceGroupName RG1 `
		-Location "East US" -VirtualNetworkGateway1 `
		$vnet02gateway -LocalNetworkGateway2 `
		$vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## Comprobación de la conexión

La conexión se puede comprobar mediante el portal clásico, el Portal de Azure o PowerShell. Para hacerlo, puede seguir estos pasos: Reemplace los valores por los suyos.

[AZURE.INCLUDE [vpn-gateway-verify connection](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="faq"></a>P+F sobre conexiones de red virtual a red virtual

Vea los detalles de preguntas más frecuentes para más información acerca de las conexiones de red virtual a red virtual.

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

<!---HONumber=AcomDC_0810_2016-->