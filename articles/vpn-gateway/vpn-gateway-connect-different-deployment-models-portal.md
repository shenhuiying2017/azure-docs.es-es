---
title: "Conexión de redes virtuales clásicas a redes virtuales de Azure Resource Manager: portal | Microsoft Docs"
description: "Aprenda a crear una conexión VPN entre redes virtuales clásicas y redes de Resource Manager mediante VPN Gateway y el portal"
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
ms.date: 04/21/2017
ms.author: cherylmc
ms.translationtype: HT
ms.sourcegitcommit: 9569f94d736049f8a0bb61beef0734050ecf2738
ms.openlocfilehash: ad5700f1a85567a3e7f4ef80b778183929cb0d68
ms.contentlocale: es-es
ms.lasthandoff: 08/31/2017

---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Conexión de redes virtuales a partir de diferentes modelos de implementación con el portal

Este artículo muestra cómo conectar redes virtuales clásicas a redes virtuales de Resource Manager para permitir que los recursos que se encuentran en modelos de implementación independientes se comuniquen entre sí. En los pasos de este artículo se usa fundamentalmente Azure Portal, pero también se puede crear esta configuración con PowerShell si se selecciona el artículo en esta lista.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

La conexión de una red virtual clásica a una red virtual de Resource Manager es similar a la conexión de una red virtual a una ubicación de sitio local. Ambos tipos de conectividad usan una puerta de enlace de VPN para proporcionar un túnel seguro con IPsec/IKE. Puede crear una conexión entre redes virtuales que estén en diferentes suscripciones y en diferentes regiones. También puede conectar redes virtuales que tengan ya conexiones a redes locales, siempre que la puerta de enlace con la que se hayan configurado sea dinámica o basada en ruta. Para más información acerca de las conexiones de red virtual a red virtual, consulte [P+F sobre conexiones de red virtual a red virtual](#faq) al final de este artículo. 

Si las redes virtuales están en la misma región, es posible que quiera conectarlas mediante emparejamiento de VNET. El emparejamiento de VNET no usa VPN Gateway. Para más información, consulte [Emparejamiento de VNET](../virtual-network/virtual-network-peering-overview.md). 

### <a name="before"></a>Antes de empezar

* En estos pasos se presume que ya se crearon ambas redes virtuales. Si usa este artículo como ejercicio y no tiene redes virtuales, los pasos incluyen vínculos que le ayudarán a crearlas.
* Compruebe que los intervalos de direcciones de las redes virtuales no se superponen entre sí ni con alguno de los intervalos de otras conexiones con las que puedan estar conectadas las puertas de enlace.
* Instale los cmdlets de PowerShell más recientes para Resource Manager y Service Management (clásico). En este artículo se usan Azure Portal y PowerShell. PowerShell es necesario para crear la conexión desde la red virtual clásica a la red virtual de Resource Manager. Para más información, vea [Instalación y configuración de Azure PowerShell](/powershell/azure/overview). 

### <a name="values"></a>Configuración de ejemplo

Puede usar estos valores para crear un entorno de prueba o hacer referencia a ellos para comprender mejor los ejemplos de este artículo.

**Red virtual clásica**

Nombre de red virtual = ClassicVNet <br>
Espacio de direcciones = 10.0.0.0/24 <br>
Subred-1 = 10.0.0.0/27 <br>
Grupo de recursos = ClassicRG <br>
Ubicación = Oeste de EE. UU. <br>
GatewaySubnet = 10.0.0.32/28 <br>
Sitio local = RMVNetLocal <br>

**Red virtual de Resource Manager**

Nombre de red virtual = RMVNet <br>
Espacio de direcciones = 192.168.0.0/16 <br>
Subred-1 = 192.168.1.0/24 <br>
Subred de la puerta de enlace = 192.168.0.0/26 <br>
Grupo de recursos = GR1 <br>
Ubicación = Este de EE. UU. <br>
Nombre de la puerta de enlace de red virtual = PuertaDeEnlaceRM <br>
Tipo de puerta de enlace = VPN <br>
Tipo de VPN = basada en enrutamiento <br>
Nombre de dirección IP pública de puerta de enlace = rmgwpip <br>
Puerta de enlace de red local = LocalRedVClásica <br>
Nombre de conexión = RMtoClassic

### <a name="connection-overview"></a>Información general sobre la conexión

Para esta configuración se crea una conexión de puerta de enlace VPN a través de un túnel VPN de IPsec/IKE entre las redes virtuales. Asegúrese de que ninguno de los intervalos de red virtual se superponga con otro o con cualquiera de las redes locales a las que se conectan.

En la tabla siguiente se muestra un ejemplo de cómo se definen los sitios locales y las redes virtuales de ejemplo:

| Virtual Network | Espacio de direcciones | Region | Se conecta a un sitio de red local |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Oeste de EE. UU. | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |Este de EE. UU. |ClassicVNetLocal (10.0.0.0/24) |

## <a name="classicvnet"></a>Sección 1: Configuración de la red virtual clásica

En esta sección se creará la red local (sitio local) y la puerta de enlace de la red virtual para la red virtual clásica. Si no tiene una red virtual clásica y lleva a cabo estos pasos como ejercicio, puede crear una red virtual según [este artículo](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) y los valores de configuración de [ejemplo](#values) indicados anteriormente.

Al usar el portal para crear una red virtual clásica, debe ir a la hoja de la red virtual siguiendo los pasos siguientes; en caso contrario, no aparece la opción para crear una red virtual clásica:

1. Haga clic en "+" para abrir la hoja "Nuevo".
2. En el campo "Buscar en el Marketplace", escriba "Red virtual". Si en su lugar, selecciona Redes -> Red virtual, no obtendrá la opción para crear una red virtual clásica.
3. En la lista de resultados, busque "Red virtual" y haga clic en él para abrir la hoja Red virtual. 
4. En la hoja de la red virtual, seleccione "Clásica" para crear una red virtual clásica. 

Si ya tiene una red virtual con una puerta de enlace VPN, compruebe que la puerta de enlace sea dinámica. Si es estática, primero debe eliminar la puerta de enlace VPN y luego continuar.

Las capturas de pantalla se proporcionan a modo de ejemplo. Asegúrese de reemplazar los valores por los suyos o use los valores de [ejemplo](#values).

### 1. <a name="local"></a>Configuración del sitio local

Abra [Azure Portal](https://ms.portal.azure.com) e inicie sesión con su cuenta de Azure.

1. Vaya a **Todos los recursos** y ubique **ClassicVNet** en la lista.
2. En la hoja **Información general**, en la sección **Conexiones VPN**, haga clic en el gráfico **Puerta de enlace** para crear una puerta de enlace.

    ![Configurar una puerta de enlace VPN](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Configurar una puerta de enlace VPN")
3. En la hoja **Nueva conexión VPN**, para **Tipo de conexión**, seleccione **De sitio a sitio**.
4. En **Sitio local**, haga clic en **Configurar los valores obligatorios**. Con esto se abre la hoja **Sitio local**.
5. En la hoja **Sitio Local**, cree un nombre para hacer referencia a la red virtual de Resource Manager. Por ejemplo, "RMVNetLocal".
6. Si la puerta de enlace VPN para la red virtual de Resource Manager ya tiene una dirección IP pública, use el valor para el campo **Dirección IP de la VPN Gateway**. Si lleva a cabo estos pasos como ejercicio o todavía no tiene una puerta de enlace de red virtual para la red virtual de Resource Manager, puede inventar una dirección IP de marcador de posición. Asegúrese de que la dirección IP de marcador de posición utiliza un formato válido. Más adelante, reemplaza la dirección IP de marcador de posición por la dirección IP pública de la puerta de enlace de red virtual de Resource Manager.
7. Para **Client Address Space** (Espacio de direcciones de clientes), use los valores de los espacios de direcciones IP de red virtual para la red virtual de Resource Manager. Este valor se usa para especificar los espacios de direcciones a fin de enrutar a la red virtual de Resource Manager.
8. Haga clic en **Aceptar** para guardar los valores y volver a la hoja **Nueva conexión VPN**.

### <a name="classicgw"></a>2. Creación de la puerta de enlace de red virtual

1. En la hoja **Nueva conexión VPN**, seleccione la casilla **Crear puerta de enlace inmediatamente** y haga clic en **Configuración de puerta de enlace opcional** para abrir la hoja **Configuración de puerta de enlace**. 

    ![Abrir hoja de configuración de puerta de enlace](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Abrir hoja de configuración de puerta de enlace")
2. Haga clic en **Subred: Configurar los valores obligatorios** para abrir la hoja **Agregar subred**. El **nombre** ya está configurado con valor requerido **GatewaySubnet**.
3. **Intervalo de direcciones** hace referencia al intervalo para la subred de puerta de enlace. Si bien puede crear una subred de puerta de enlace con un intervalo de direcciones de /29 (3 direcciones), se recomienda crear una subred de puerta de enlace que incluya más direcciones IP. Esto permitirá configuraciones futuras que puedan requerir más direcciones IP disponibles. Si es posible, utilice /27 o /28. Si usa estos pasos como ejercicio, puede hacer referencia a los valores de [ejemplo](#values). Haga clic en **Aceptar** para crear la subred de puerta de enlace.
4. En la hoja **Configuración de puerta de enlace**, **Tamaño** hace referencia a la SKU de puerta de enlace. Seleccione la SKU de puerta de enlace para la puerta de enlace VPN.
5. Compruebe que el **Tipo de enrutamiento** sea **Dinámico** y haga clic en **Aceptar** para volver a la hoja **Nueva conexión VPN**.
6. En la hoja **Nueva conexión VPN**, haga clic en **Aceptar** para empezar a crear la puerta de enlace VPN. Una puerta de enlace VPN puede tardar hasta 45 minutos en completarse.

### <a name="ip"></a>3. Copia de la dirección IP pública de la puerta de enlace de la red virtual

Una vez que se haya creado la puerta de enlace de la red virtual, puede ver la dirección IP de la puerta de enlace. 

1. Desplácese a la red virtual clásica y haga clic en **Información general**.
2. Haga clic en **Conexiones VPN** para abrir la hoja de conexiones. En la hoja de conexiones VPN, puede ver la dirección IP pública. Se trata de la dirección IP pública asignada a la puerta de enlace de red virtual. 
3. Escriba o copie la dirección IP. Se usa en pasos posteriores al trabajar con las opciones de configuración de puerta de enlace de red local de Resource Manager. También puede ver el estado de las conexiones de puerta de enlace. Observe que el sitio de red local que creó aparece como "Conectando". El estado cambiará una vez creadas las conexiones.
4. Cierre la hoja después de copiar la dirección IP de puerta de enlace.

## <a name="rmvnet"></a>Sección 2: Configuración de redes virtuales de Resource Manager

En esta sección se crea la puerta de enlace de red virtual y la puerta de enlace de red local para la red de Resource Manager. Si no tiene una red virtual de Resource Manager y lleva a cabo estos pasos como ejercicio, puede crear una red virtual según [este artículo](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) y los valores de configuración de [ejemplo](#values) indicados anteriormente.

Las capturas de pantalla se proporcionan a modo de ejemplo. Asegúrese de reemplazar los valores por los suyos o use los valores de [ejemplo](#values).

### <a name="1-create-a-gateway-subnet"></a>1. Creación de una subred de puerta de enlace

Antes de crear una puerta de enlace de red virtual, primero deberá crear una subred de puerta de enlace. Cree una subred de puerta de enlace con un recuento CIDR de /28 o mayor. (/27, /26, etc.)

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

### <a name="creategw"></a>2. Creación de una puerta de enlace de red virtual

[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

### <a name="createlng"></a>3. Creación de una puerta de enlace de red local

La puerta de enlace de red local especifica el intervalo de direcciones y la dirección IP pública asociados a la red virtual clásica y su puerta de enlace de red virtual.

Si lleva a cabo estos pasos como ejercicio, haga referencia a esta configuración:

| Virtual Network | Espacio de direcciones | Region | Se conecta a un sitio de red local |Dirección IP pública de puerta de enlace|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |Oeste de EE. UU. | RMVNetLocal (192.168.0.0/16) |La dirección IP pública que se asigna a la puerta de enlace ClassicVNet|
| RMVNet | (192.168.0.0/16) |Este de EE. UU. |ClassicVNetLocal (10.0.0.0/24) |La dirección IP pública que se asigna a la puerta de enlace RMVNet.|

[!INCLUDE [vpn-gateway-add-lng-rm-portal](../../includes/vpn-gateway-add-lng-rm-portal-include.md)]

## <a name="modifylng"></a>Sección 3: modificación de la configuración del sitio local de la red virtual clásica

En esta sección se reemplaza la dirección IP de marcador de posición que usó al especificar la configuración del sitio local con la dirección IP de puerta de enlace VPN de Resource Manager. Esta sección utiliza los cmdlets de PowerShell (SM) clásicos.

1. En Azure Portal, vaya a la red virtual clásica.
2. En la hoja de la red virtual, haga clic en **Información general**.
3. En la sección **Conexiones VPN**, haga clic en el nombre del sitio local en el gráfico.

    ![Conexiones VPN](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "Conexiones VPN")
4. En la hoja **Conexiones VPN de sitio a sitio**, haga clic en el nombre del sitio.

    ![Nombre del sitio](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Nombre del sitio local")
5. En la hoja de conexión para el sitio local, haga clic en el nombre del sitio local para abrir la hoja **Sitio local**.

    ![Abrir sitio local](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Abrir sitio local")
6. En la hoja **Sitio local**, reemplace la **dirección IP de puerta de enlace VPN** por la dirección IP de la puerta de enlace de Resource Manager.

    ![Dirección IP de puerta de enlace](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "Dirección IP de puerta de enlace")
7. Haga clic en **Aceptar** para actualizar la dirección IP.

## <a name="RMtoclassic"></a>Sección 4: Creación de conexión de red virtual de Resource Manager a red virtual clásica

En estos pasos se configura la conexión desde la red virtual de Resource Manager a la red virtual clásica con Azure Portal.

1. En **Todos los recursos**, ubique la puerta de enlace de red local. En el ejemplo, la puerta de enlace de red local es **ClassicVNetLocal**.
2. Haga clic en **Configuración** y compruebe que el valor de dirección IP es la puerta de enlace VPN para la red virtual clásica. Actualice si es necesario y, luego, haga clic en **Guardar**. Cierre la hoja.
3. En **Todos los recursos**, haga clic en la puerta de enlace de red local.
4. Haga clic en **Conexiones** para abrir la hoja Conexiones.
5. En la hoja **Conexiones**, haga clic en **+** para agregar una conexión.
6. En la hoja **Agregar conexión**, asigne un nombre a la conexión. Por ejemplo, "RMtoClassic".
7. En esta hoja ya está seleccionada la opción **De sitio a sitio**.
8. Seleccione la puerta de enlace de red virtual que desea asociar con este sitio.
9. Cree una **clave compartida**. Esta clave se usa también en la conexión que crea desde la red virtual clásica a la red virtual de Resource Manager. Puede generar la clave o inventar una. En el ejemplo usamos "abc123", pero puede (y debe) usar un valor más complejo.
10. Haga clic en **Aceptar** para crear la conexión.

##<a name="classictoRM"></a>Sección 5: Creación de conexión de red virtual clásica a red virtual de Resource Manager

En estos pasos se configura la conexión desde la red virtual clásica a la red virtual de Resource Manager. Estos pasos requieren PowerShell. Esta conexión no se puede crear en el portal. Asegúrese de que ha descargado e instalado los cmdlets de PowerShell tanto del modelo clásico (SM) como del modelo de Resource Manager (RM).

### <a name="1-connect-to-your-azure-account"></a>1. Conexión a la cuenta de Azure

Abra la consola de PowerShell con derechos elevados e inicie sesión en la cuenta de Azure. El siguiente cmdlet pide las credenciales de inicio de sesión de la cuenta de Azure. Después de iniciar la sesión, se descarga la configuración de la cuenta para que esté disponible para Azure PowerShell.

```powershell
Login-AzureRmAccount
```
   
Si tiene más de una suscripción de Azure, obtenga una lista de todas ellas.

```powershell
Get-AzureRmSubscription
```

Especifique la suscripción que desea usar. 

```powershell
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```

Agregue la cuenta de Azure para usar los cmdlets clásicos de PowerShell (SM). Para ello puede utilizar el siguiente comando:

```powershell
Add-AzureAccount
```

### <a name="2-view-the-network-configuration-file-values"></a>2. Visualización de los valores de archivo de configuración de red

Cuando crea una red virtual en Azure Portal, el nombre completo que Azure usa no aparece en Azure Portal. Por ejemplo, una red virtual que parece llamarse "ClassicVNet" en Azure Portal puede que tenga un nombre mucho más largo en el archivo de configuración de la red. El nombre podría ser similar al siguiente: "Group ClassicRG ClassicVNet". En estos pasos se descarga el archivo de configuración de red y se ven los valores.

Cree un directorio en el equipo y, a continuación, exporte el archivo de configuración de red al directorio. En este ejemplo, se exporta el archivo de configuración de red a C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Abra el archivo con un editor de texto y consulte el nombre de la red virtual clásica. Use los nombres que aparecen en el archivo de configuración de red cuando ejecute los cmdlets de PowerShell.

- Los nombres de las redes virtuales aparecen como **VirtualNetworkSite name =**
- Los nombres de los sitios aparecen como **LocalNetworkSite name=**

### <a name="3-create-the-connection"></a>3. Creación de la conexión

Establezca la clave compartida y cree la conexión desde la red virtual clásica a la red virtual de Resource Manager. No se puede establecer la clave compartida mediante el portal. Asegúrese de ejecutar estos pasos mientras la sesión esté iniciada con la versión clásica de los cmdlets de PowerShell. Para ello, use **Add-AzureAccount**. De lo contrario, no podrá establecer '-AzureVNetGatewayKey'.

- En este ejemplo, **-VNetName** es el nombre de la red virtual clásica tal como se encuentra en el archivo de configuración de red. 
- **-LocalNetworkSiteName** es el nombre que especificó para el sitio local, según se encontró en el archivo de configuración de red.
- **-SharedKey** es un valor que se puede generar y especificar. En este ejemplo, hemos utilizado *abc123* pero puede generar algo más complejo. Lo importante es que el valor que especifique aquí debe ser el mismo que el que se especificó al crear la conexión entre la red virtual de Resource Manager y la red virtual clásica.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

##<a name="verify"></a>Sección 6: Comprobación de las conexiones

Puede comprobar las conexiones mediante Azure Portal o PowerShell. Al comprobar, es posible que necesite esperar un minuto o dos mientras se crea la conexión. Cuando una conexión se realiza correctamente, el estado de conectividad cambia de "Conectando" a "Conectado".

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>Comprobación de la conexión de la red virtual clásica a la red virtual de Resource Manager

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

###<a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>Comprobación de la conexión de la red virtual de Resource Manager a la red virtual clásica

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="faq"></a>P+F sobre conexiones de red virtual a red virtual

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

