---
title: "Conexión de redes virtuales clásicas a redes virtuales de Azure Resource Manager: portal | Microsoft Docs"
description: "Aprenda a crear una conexión VPN entre redes virtuales clásicas y redes de Resource Manager mediante la Puerta de enlace de VPN y el portal"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 5a90498c-4520-4bd3-a833-ad85924ecaf9
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 10985eaebd9148ef4fbd22eac0ba50076ca71ef5
ms.openlocfilehash: c20d72f25571e582310ae65ad1d887dbdb7b011c


---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Conexión de redes virtuales a partir de diferentes modelos de implementación con el portal
> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Actualmente, Azure tiene dos modelos de administración: el clásico y el de Resource Manager (RM). Si lleva un tiempo usando Azure, es probable que tenga máquinas virtuales de Azure y roles de instancia que se ejecuten en una red virtual clásica. Es posible que sus máquinas virtuales e instancias de roles más recientes se estén ejecutando en una red virtual creada en Resource Manager. Este artículo le guiará a través de la conexión de redes virtuales clásicas a redes virtuales de Resource Manager para permitir que los recursos que se encuentran en modelos de implementación independientes se comuniquen entre sí mediante una conexión de puerta de enlace. 

Puede crear una conexión entre redes virtuales que estén en diferentes suscripciones y en diferentes regiones. También puede conectar redes virtuales que tengan ya conexiones a redes locales, siempre que la puerta de enlace con la que se hayan configurado sea dinámica o basada en ruta. Para más información acerca de las conexiones de red virtual a red virtual, consulte la sección [Consideraciones de red virtual a red virtual](#faq) al final de este artículo.

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implementación y métodos para conexiones de red virtual a red virtual
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

La siguiente tabla se actualiza cada vez que hay nuevos artículos y nuevas herramientas disponibles para esta configuración. Cuando aparezca un artículo, creamos un vínculo directo a él desde la tabla.<br><br>

**De red virtual a red virtual**

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**Emparejamiento de VNET**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="a-namebeforeabefore-beginning"></a><a name="before"></a>Antes de comenzar
Los siguientes pasos le guiarán a través de los valores necesarios para configurar una puerta de enlace dinámica o basada en ruta para cada red virtual y crear una conexión VPN entre las puertas de enlace. Esta configuración no admite puertas de enlace estáticas o basadas en directivas. 

En este artículo se van a usar Azure Portal y PowerShell. PowerShell es necesario para crear las conexiones entre las redes virtuales. No se pueden crear las conexiones para esta configuración con el portal.

### <a name="prerequisites"></a>Requisitos previos
* Se han creado ambas redes virtuales.
* Los intervalos de direcciones de las redes virtuales no se superponen entre sí ni con alguno de los intervalos de otras conexiones con las que puedan estar conectadas las puertas de enlace.
* Tiene instalados los últimos cmdlets de PowerShell. Para obtener más información, vea [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs) . Asegúrese de instalar los cmdlets tanto de Service Management (SM) como de Resource Manager (RM). 

### <a name="a-namevaluesaexample-settings"></a><a name="values"></a>Configuración de ejemplo
Puede usar la configuración de ejemplo como referencia.

**Configuración de redes virtuales clásicas**

Nombre de la red virtual = RedVClásica <br>
Espacio de direcciones = 10.0.0.0/24 <br>
Subred-1 = 10.0.0.0/27 <br>
Grupo de recursos = ClassicRG <br>
Ubicación = Oeste de EE. UU. <br>
GatewaySubnet = 10.0.0.32/28 <br>
Sitio local = RMVNetLocal <br>

**Configuración de redes virtuales de Resource Manager**

Nombre de red virtual = RedRMV <br>
Espacio de direcciones = 192.168.0.0/16 <br>
Subred-1 = 192.168.1.0/24 <br>
Subred de la puerta de enlace = 192.168.0.0/26 <br>
Grupo de recursos = GR1 <br>
Ubicación = Este de EE. UU. <br>
Nombre de la puerta de enlace de red virtual = PuertaDeEnlaceRM <br>
Tipo de puerta de enlace = VPN <br>
Tipo de VPN = basada en enrutamiento <br>
Nombre IP público de puerta de enlace = pepip <br>
Puerta de enlace de red local = LocalRedVClásica <br>

## <a name="a-namecreatesmgwasection-1-configure-classic-vnet-settings"></a><a name="createsmgw"></a>Sección 1: configuración de la red virtual con el modelo clásico
En esta sección se creará la red local (sitio local) y la puerta de enlace de la red virtual para la red virtual clásica. Las instrucciones de esta sección utilizan Azure Portal. En los siguientes pasos se supone que no se ha creado una puerta de enlace VPN para la red virtual clásica. Si ya tiene una puerta de enlace, compruebe que sea dinámica. Si es estática, primero debe eliminar la puerta de enlace VPN y luego continuar con los pasos siguientes.

### <a name="part-1---configure-the-local-site"></a>Parte 1: configuración del sitio local

Abra [Azure Portal](https://ms.portal.azure.com) e inicie sesión con su cuenta de Azure.

1. Vaya a **Todos los recursos** y busque la red virtual clásica.
2. En la hoja **Información general**, en la sección **Conexiones VPN**, haga clic en el gráfico **Puerta de enlace** para crear una puerta de enlace.

    ![Configurar una puerta de enlace VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Configurar una puerta de enlace VPN")
3. En la hoja **Nueva conexión VPN**, para **Tipo de conexión**, seleccione **De sitio a sitio**.
4. En **Sitio local**, haga clic en **Configurar los valores obligatorios**. Se abrirá la hoja **Sitio local**.
5. En la hoja **Sitio Local**, cree un nombre que va a utilizar para hacer referencia a la red virtual de Resource Manager. Por ejemplo, RMVNetLocal.
6. Si la puerta de enlace VPN para la red virtual de Resource Manager ya tiene una dirección IP pública, utilice el valor para el campo **Dirección IP de la puerta de enlace de VPN**. Si aún no ha creado una puerta de enlace de red virtual para la red virtual de Resource Manager, puede usar una dirección IP de marcador de posición. Asegúrese de que la dirección IP de marcador de posición utiliza un formato válido. Más adelante, debe reemplazar la dirección IP de marcador de posición por la dirección IP pública de la puerta de enlace de red virtual de Resource Manager.
7. Para **Client Address Space** (Espacio de direcciones de clientes), use los valores de los espacios de direcciones IP de red virtual para la red virtual de Resource Manager. Este valor se utiliza para especificar los espacios de direcciones a fin de enrutar a la red virtual de RM.
8. Haga clic en **Aceptar** para guardar los valores y volver a la hoja **Nueva conexión VPN**.

### <a name="part-2---create-the-virtual-network-gateway"></a>Parte 2: creación de la puerta de enlace de la red virtual
1. En la hoja **Nueva conexión VPN**, seleccione la casilla **Crear puerta de enlace inmediatamente** y haga clic en **Configuración de puerta de enlace opcional** para abrir la hoja **Configuración de puerta de enlace**. 

    ![Abrir hoja de configuración de puerta de enlace](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Abrir hoja de configuración de puerta de enlace")
2. Haga clic en **Subred: Configurar los valores obligatorios** para abrir la hoja **Agregar subred**. En esta hoja, verá que el nombre ya está configurado con el valor requerido **GatewaySubnet**.
3. **Intervalo de direcciones** hace referencia al intervalo para la subred de puerta de enlace. Aunque puede crear una subred de puerta de enlace con un intervalo de direcciones /29 (3 direcciones), se recomienda crear una subred de puerta de enlace que contenga más direcciones IP disponibles para dar cabida a posibles futuras configuraciones que requieran más direcciones IP disponibles. Si es posible, utilice /27 o /28. Haga clic en **Aceptar** para crear la subred de puerta de enlace.
4. En la hoja **Configuración de puerta de enlace**, **Tamaño** hace referencia a la SKU de puerta de enlace. Seleccione la SKU de puerta de enlace para la puerta de enlace VPN.
5. Compruebe que el **Tipo de enrutamiento** sea **Dinámico** y haga clic en **Aceptar** para volver a la hoja **Nueva conexión VPN**.
6. En la hoja **Nueva conexión VPN**, haga clic en **Aceptar** para empezar a crear la puerta de enlace VPN. Esto puede tardar hasta 45 minutos en completarse.


### <a name="a-nameipapart-3---copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>Parte 3: copia de la dirección IP pública de la puerta de enlace de la red virtual
Una vez que se haya creado la puerta de enlace de la red virtual, puede ver la dirección IP de la puerta de enlace. 

1. Desplácese a la red virtual clásica y haga clic en **Información general**.
2. Haga clic en **Conexiones VPN** para abrir la hoja de conexiones. En la hoja de conexiones VPN, puede ver la dirección IP pública. Se trata de la dirección IP pública asignada a la puerta de enlace de red virtual. Escriba o copie la dirección IP. La usará en pasos posteriores al trabajar con las opciones de configuración de puerta de enlace de red local de Resource Manager. También puede ver el estado de las conexiones de puerta de enlace. Observe que el sitio de red local que creó aparece como "Conectando". El estado cambiará una vez creadas las conexiones. Cierre la hoja después de copiar la dirección IP de puerta de enlace.

## <a name="a-namecreatermgwasection-2-configure-resource-manager-vnet-settings"></a><a name="creatermgw"></a>Sección 2: configuración de redes virtuales de Resource Manager
En esta sección creará la puerta de enlace y la red local de red virtual para la red virtual de Resource Manager. Las capturas de pantalla se proporcionan a modo de ejemplo. Asegúrese de reemplazar los valores por los suyos. Si va a crear esta configuración como un ejercicio, haga referencia a estos [valores](#values).

### <a name="part-1---create-a-gateway-subnet"></a>Parte 1: creación de una subred de la puerta de enlace
Antes de crear una puerta de enlace de red virtual, primero deberá crear una subred de puerta de enlace. Cree una subred de puerta de enlace con un recuento CIDR de /28 o mayor. (/27, /26, etc.)

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

Desde un explorador, navegue al [Portal de Azure](http://portal.azure.com) e inicie sesión con su cuenta de Azure.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="part-2---create-a-virtual-network-gateway"></a>Parte 2: creación de una puerta de enlace de la red virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

La creación de una puerta de enlace de red virtual puede tardar hasta 45 minutos. Puede esperar a que finalice la creación de la puerta de enlace de la red virtual de Resource Manager o puede proseguir con [Parte 3: creación de una puerta de enlace de la red local](#createlng). Una vez creada la puerta de enlace, se le asigna una dirección IP pública. En pasos posteriores, esta dirección IP se usa para reemplazar la información de dirección IP de marcador de posición para la configuración de sitio local de red virtual clásica que creó en la sección 1. 

### <a name="a-namecreatelngapart-3---create-a-local-network-gateway"></a><a name="createlng"></a>Parte 3: creación de una puerta de enlace de la red local

La puerta de enlace de red local especifica el intervalo de direcciones y la dirección IP pública asociados a la red virtual clásica y su puerta de enlace de red virtual.

- Utilice la dirección IP pública que se asignó a la puerta de enlace de la red virtual clásica en la [sección anterior](#ip). 
- Asigne a la puerta de enlace de la red local un nombre a través del que Azure pueda hacer referencia a ella. Por ejemplo, 'ClassicVNetLocal'.
- Utilice el espacio de direcciones que asignó a la red virtual clásica (no solo la subred).

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]


## <a name="section-3-modify-the-classic-vnet-local-site-settings"></a>Sección 3: modificación de la configuración del sitio local de la red virtual clásica

En esta sección trabajará con la red virtual clásica. Reemplazará la dirección IP de marcador de posición que utilizó al especificar la configuración del sitio local. Esta sección utiliza los cmdlets de PowerShell (SM) clásicos. Si no lo ha hecho ya, vea la puerta de enlace de red virtual para la red virtual de Resource Manager y copie la dirección IP pública. Se trata de la dirección IP que va a usar para reemplazar la dirección IP de marcador de posición.

Compruebe que ha descargado la versión más reciente de estos cmdlets, tal como se especifica en la sección [Antes de comenzar](#before).

1. En Azure Portal, vaya a la red virtual clásica.
2. En la hoja de la red virtual, haga clic en **Información general**.
3. En la sección **Conexiones VPN**, haga clic en el nombre del sitio local en el gráfico.

    ![Conexiones VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "Conexiones VPN")
4. En la hoja **Conexiones VPN de sitio a sitio**, haga clic en el nombre del sitio.

    ![Nombre del sitio](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Nombre del sitio local")
5. En la hoja de conexión para el sitio local, haga clic en el nombre del sitio local para abrir la hoja **Sitio local**.

    ![Abrir sitio local](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Abrir sitio local")
6. En la hoja **Sitio local**, reemplace la dirección IP de puerta de enlace VPN por la dirección IP de la puerta de enlace de Resource Manager.

    ![Dirección IP de puerta de enlace](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "Dirección IP de puerta de enlace")
7. Haga clic en **Aceptar** para actualizar la dirección IP.


## <a name="a-nameconnectasection-4-create-the-connection"></a><a name="connect"></a>Sección 4: creación de la conexión
En esta sección creará la conexión entre las redes virtuales. Estos pasos requieren PowerShell. No puede crear esta conexión en ninguno de los portales. Asegúrese de que ha descargado e instalado los cmdlets de PowerShell tanto del modelo clásico (SM) como del modelo de Resource Manager (RM).

### <a name="part-1---log-in-to-your-azure-account"></a>Parte 1: inicio de sesión en la cuenta de Azure

1. Abra la consola de PowerShell con derechos elevados e inicie sesión en la cuenta de Azure. El siguiente cmdlet pide las credenciales de inicio de sesión de la cuenta de Azure. Después de iniciar la sesión, se descarga la configuración de la cuenta para que esté disponible para Azure PowerShell.
   
        Login-AzureRmAccount 
   
2. Si tiene más de una suscripción de Azure, obtenga una lista de todas ellas.
   
        Get-AzureRmSubscription
   
3. Especifique la suscripción que desea usar. 
   
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

4. Agregue su cuenta de Azure para usar los cmdlets de PowerShell clásicos. Para ello puede utilizar el siguiente comando:
   
        Add-AzureAccount

### <a name="part-2---download-your-network-configuration-file"></a>Parte 2: descarga del archivo de configuración de red

A veces se cambian los nombres de los sitios de las redes virtuales clásicas y de la red local en el archivo de configuración de red al crear la configuración de la red virtual clásica en Azure Portal debido a las diferencias en los modelos de implementación. Por ejemplo, con Azure Portal, hemos nombrado la red virtual clásica 'Classic VNet' y la hemos creado en un grupo de recursos llamado 'ClassicRG'. El nombre que se encuentra en el archivo de configuración de red se convierte en 'Group ClassicRG Classic VNet'. Al especificar el nombre de una red virtual que contiene espacios, utilice comillas alrededor del valor.

1. Exporte el archivo de configuración de red de Azure mediante la ejecución del comando siguiente. Puede cambiar la ubicación del archivo que se va a exportar a una ubicación diferente si es necesario.
   
        Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
3. Utilice un editor de texto, como el Bloc de notas, para abrir el archivo .xml y ver el contenido. Compruebe los valores de los nombres de la red virtual clásica y el sitio de la red local.

### <a name="part-3---set-the-shared-key"></a>Parte 3: actualización de la clave compartida

Establezca la clave compartida para la conexión de la red virtual clásica a la red virtual de Resource Manager. Al usar estos cmdlets, asegúrese de comprobar los valores de `-VNetName` y `-LocalNetworkSiteName` utilizan el mismo archivo de configuración de red. Al especificar nombres que contienen espacios, utilice comillas alrededor del valor. 

- En este ejemplo, `-VNetName` es el nombre de la red virtual clásica. 
- `-LocalNetworkSiteName` es el nombre especificado para el sitio local.
- `-SharedKey` es un valor que puede generar y especificar. En este ejemplo, hemos utilizado *abc123* pero puede generar algo más complejo. Lo importante es que el valor que especifique aquí debe ser el mismo que el que va a especificar en el paso siguiente, cuando cree la conexión.

En la consola de PowerShell, establezca la clave compartida; para ello, ejecute el ejemplo siguiente y asegúrese de que reemplaza los valores por los suyos propios. La devolución para este ejemplo debe mostrar **Estado: Correcto**.
   
        Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
        -LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
### <a name="part-4---create-the-vpn-connection-by-running-the-following-commands"></a>Parte 4: creación de la conexión VPN mediante los comandos siguientes:
   
1. Establezca las variables.
   
        $vnet01gateway = Get-AzureRMLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
        $vnet02gateway = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   
2. Cree la conexión. En este ejemplo, `-Name` es el nombre que desea dar a la conexión y no algo que ya ha creado. En el ejemplo siguiente, se crea una conexión llamada 'RM-Classic'. Tenga en cuenta que `-ConnectionType` es 'IPsec', no 'Vnet2Vnet' y que `-SharedKey` coincide con la clave establecida anteriormente.
   
        New-AzureRmVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
        -Location "East US" -VirtualNetworkGateway1 `
        $vnet02gateway -LocalNetworkGateway2 `
        $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="section-5-verify-your-connections"></a>Sección 5: comprobación de las conexiones
Puede comprobar las conexiones mediante Azure Portal o PowerShell. Al comprobar, es posible que necesite esperar un minuto o dos mientras se crea la conexión. Cuando una conexión se realiza correctamente, el estado de conectividad irá de 'Conectando' a 'Conectado'.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Comprobación de la conexión de la red virtual clásica a la red virtual de Resource Manager

####<a name="verify-your-connection-using-powershell"></a>Comprobación de la conexión mediante PowerShell


[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

####<a name="verify-your-connection-using-the-azure-portal"></a>Comprobación de la conexión mediante Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


###<a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Comprobación de la conexión de la red virtual de Resource Manager a la red virtual clásica

####<a name="verify-your-connection-using-powershell"></a>Comprobación de la conexión mediante PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

####<a name="verify-your-connection-using-the-azure-portal"></a>Comprobación de la conexión mediante Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="a-namefaqavnet-to-vnet-considerations"></a><a name="faq"></a>Consideraciones de red virtual a red virtual

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]




<!--HONumber=Jan17_HO4-->


