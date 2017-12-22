---
title: "Creación de una conexión entre redes virtuales: Clásico: Azure Portal | Microsoft Docs"
description: "Cómo conectar redes virtuales de Azure simultáneamente mediante PowerShell y Azure Portal."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/05/2017
ms.author: cherylmc
ms.openlocfilehash: 1e7a7af26fbfb728aa5a6b8a0d63b71f678256bf
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="configure-a-vnet-to-vnet-connection-classic"></a>Configuración de una conexión de red virtual a red virtual (clásico)

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

En este artículo se explica cómo crear una conexión de VPN Gateway entre redes virtuales. Las redes virtuales pueden estar en la misma región o en distintas, así como pertenecer a una única suscripción o a varias. Los pasos de este artículo se corresponden al modelo de implementación clásica y a Azure Portal. También se puede crear esta configuración con una herramienta o modelo de implementación distintos, mediante la selección de una opción diferente en la lista siguiente:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [CLI de Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal de Azure clásico](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Conexión de diferentes modelos de implementación - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Conexión de diferentes modelos de implementación - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![Diagrama de conectividad VNet a VNet](./media/vpn-gateway-howto-vnet-vnet-portal-classic/v2vclassic.png)

## <a name="about-vnet-to-vnet-connections"></a>Acerca de conexiones de red virtual a red virtual

La conexión de una red virtual a otra (de red virtual a red virtual) en el modelo de implementación clásica con una VPN Gateway es parecida a la conexión de una red virtual a la ubicación de un sitio local. Ambos tipos de conectividad usan una VPN Gateway para proporcionar un túnel seguro con IPsec/IKE.

Las redes virtuales que se conecten pueden estar en suscripciones y regiones distintas. Puede combinar la comunicación entre redes virtuales con configuraciones de varios sitios. Esto permite establecer topologías de red que combinen la conectividad entre entornos con la conectividad entre redes virtuales.

![Conexiones de red virtual a red virtual](./media/vpn-gateway-howto-vnet-vnet-portal-classic/aboutconnections.png)

### <a name="why"></a>¿Por qué debería conectarse a redes virtuales?

Puede que desee conectar redes virtuales por las siguientes razones:

* **Presencia geográfica y redundancia geográfica entre regiones**

  * Puede configurar su propia replicación geográfica o sincronización con conectividad segura sin recurrir a los puntos de conexión a Internet.
  * Con Azure Load Balancer y Microsoft, o con una tecnología de agrupación en clústeres de otros fabricantes, puede configurar cargas de trabajo de alta disponibilidad y redundancia geográfica en varias regiones de Azure. Por ejemplo, puede configurar AlwaysOn de SQL con grupos de disponibilidad distribuidos en varias regiones de Azure.
* **Aplicaciones regionales de varios niveles con límite de aislamiento sólido**

  * En la misma región se pueden configurar aplicaciones de niveles múltiples con varias redes virtuales conectadas entre sí, con un aislamiento sólido y una comunicación entre niveles segura.
* **Comunicación entre suscripciones y entre organizaciones en Azure**

  * Si tiene varias suscripciones a Azure, puede conectar cargas de trabajo de distintas suscripciones simultáneamente entre redes virtuales de forma segura.
  * Asimismo, tanto las empresas como los proveedores de servicios pueden habilitar la comunicación entre organizaciones con tecnología VPN segura en Azure.

Para más información acerca de las conexiones de red virtual a red virtual, consulte [Consideraciones de red virtual a red virtual](#faq) al final de este artículo.

### <a name="before-you-begin"></a>Antes de empezar

Antes de comenzar este ejercicio, descargue e instale la versión más reciente de los cmdlets de PowerShell para Azure Service Management (SM). Para más información, vea [Instalación y configuración de Azure PowerShell](/powershell/azure/overview). Usamos el portal para la mayoría de los pasos, pero debe usar PowerShell para crear las conexiones entre las redes virtuales. No se pueden crear las conexiones mediante Azure Portal.

## <a name="plan"></a>Paso 1: Planeamiento de los intervalos de direcciones IP

Es importante decidir los intervalos que usará para configurar las redes virtuales. Para esta configuración, debe asegurarse de que ninguno de los intervalos de red virtual se superpongan entre sí o con cualquiera de las redes locales a las que se conectan.

En la tabla siguiente se muestra un ejemplo de cómo definir las redes virtuales. Use los intervalos solo como referencia. Escriba los intervalos para las redes virtuales. Necesitará esta información en pasos posteriores.

**Ejemplo**

| Virtual Network | Espacio de direcciones | Region | Se conecta a un sitio de red local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Este de EE. UU. |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Oeste de EE. UU. |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

## <a name="vnetvalues"></a>Paso 2: Creación de las redes virtuales

Cree dos redes virtuales en [Azure Portal](https://portal.azure.com). Si desea ver los pasos para crear redes virtuales clásicas, consulte [Creación de una red virtual clásica](../virtual-network/virtual-networks-create-vnet-classic-pportal.md). 

Al usar el portal para crear una red virtual clásica, debe ir a la página de la red virtual siguiendo los pasos siguientes; en caso contrario, no aparecerá la opción para crear una red virtual clásica:

1. Haga clic en "+" para abrir la página "Nuevo".
2. En el campo "Buscar en el Marketplace", escriba "Virtual Network". Si en su lugar, selecciona Redes -> Virtual Network, no obtendrá la opción para crear una red virtual clásica.
3. En la lista de resultados, busque "Virtual Network" y haga clic en esa opción para abrir la página Virtual Network. 
4. En la página de red virtual, seleccione "Clásica" para crear una red virtual clásica. 

Si usa este artículo como ejercicio, puede usar los valores de ejemplo siguientes:

**Valores para TestVNet1**

Nombre: TestVNet1<br>
Espacio de direcciones: 10.11.0.0/16, 10.12.0.0/16 (opcional)<br>
Nombre de subred: predeterminado<br>
Intervalo de direcciones de subred: 10.11.0.1/24<br>
Grupo de recursos: ClassicRG<br>
Ubicación: Este de EE. UU.<br>
GatewaySubnet: 10.11.1.0/27

**Valores para TestVNet4**

Nombre: TestVNet4<br>
Espacio de direcciones: 10.41.0.0/16, 10.42.0.0/16 (opcional)<br>
Nombre de subred: predeterminado<br>
Intervalo de direcciones de subred: 10.41.0.1/24<br>
Grupo de recursos: ClassicRG<br>
Ubicación: Oeste de EE. UU.<br>
GatewaySubnet: 10.41.1.0/27

**Cuando cree las redes virtuales, recuerde la siguiente configuración:**

* **Espacios de direcciones de la red virtual**: en la página Espacios de direcciones de la red virtual, especifique el intervalo de direcciones que desea usar para la red virtual. Estas son las direcciones IP dinámicas que se asignarán a las máquinas virtuales y a las demás instancias de rol implementadas en esta red virtual.<br>Los espacios de direcciones que selecciona no se pueden superponer con los espacios de direcciones de ninguna de las otras redes virtuales o las ubicaciones locales a las que se conectará esta red virtual.

* **Ubicación** : al crear una red virtual, la debe asociar a una ubicación de Azure (región). Por ejemplo, Por ejemplo, si desea que las máquinas virtuales que implemente en la red virtual se encuentren físicamente en Oeste de EE.UU., seleccione esa ubicación. No se puede cambiar la ubicación asociada a la red virtual después de crearla.

**Después de crear las redes virtuales, puede agregar la configuración siguiente:**

* **Espacio de direcciones**: no se requiere espacio de direcciones adicional para esta configuración, pero puede agregar espacio de direcciones adicional después de crear la red virtual.

* **Subredes**: no se requieren subredes adicionales para esta configuración, pero puede que quiera que las máquinas virtuales estén en una subred independiente de las otras instancias de rol.

* **Servidores DNS**: escriba el nombre del servidor DNS y la dirección IP. Mediante este valor no se crea un servidor DNS. Le permite especificar el servidor DNS que desea usar para la resolución de nombres para esta red virtual.

En esta sección se configura el tipo de conexión, el sitio local y se crea la puerta de enlace.

## <a name="localsite"></a>Paso 3: Configuración del sitio local

Azure usa la configuración especificada en cada sitio de red local para determinar cómo enrutar el tráfico entre las redes virtuales. Cada red virtual debe señalar a la red local correspondiente a la que se desea redirigir el tráfico. Puede determinar el nombre que quiere utilizar para hacer referencia a cada sitio de red local. Se recomienda usar un nombre descriptivo.

Por ejemplo, TestVNet1 se conecta a un sitio de red local que cree con el nombre "VNet4Local". La configuración de VNet4Local contiene los prefijos de dirección para TestVNet4.

El sitio local para cada red virtual es la otra red virtual. Los valores de ejemplo siguientes se usan para esta configuración:

| Virtual Network | Espacio de direcciones | Region | Se conecta a un sitio de red local |
|:--- |:--- |:--- |:--- |
| TestVNet1 |TestVNet1<br>(10.11.0.0/16)<br>(10.12.0.0/16) |Este de EE. UU. |VNet4Local<br>(10.41.0.0/16)<br>(10.42.0.0/16) |
| TestVNet4 |TestVNet4<br>(10.41.0.0/16)<br>(10.42.0.0/16) |Oeste de EE. UU. |VNet1Local<br>(10.11.0.0/16)<br>(10.12.0.0/16) |

1. Ubique TestVNet1 en Azure Portal. En la sección **Conexiones VPN** de la página, haga clic en **Puerta de enlace**.

    ![Sin puerta de enlace](./media/vpn-gateway-howto-vnet-vnet-portal-classic/nogateway.png)
2. En la página **Nueva conexión VPN**, seleccione **De sitio a sitio**.
3. Haga clic en **Sitio local** para abrir la página Sitio local y ajuste la configuración.
4. En la página **Sitio local**, asígnele un nombre al sitio local. En el ejemplo, le asignamos el nombre "VNet4Local" al sitio local.
5. Para la **dirección IP de VPN Gateway**, puede usar la dirección IP que desee, siempre que tenga un formato válido. Normalmente, se usaría la dirección IP externa real para un dispositivo VPN. Pero para las configuraciones de red virtual a red virtual, se usa la dirección IP pública asignada a la puerta de enlace para la red virtual. Dado que aún no ha creado la puerta de enlace de red virtual, se especifica cualquier dirección IP pública válida como marcador de posición.<br>No lo deje en blanco: no es opcional para esta configuración. En un paso posterior, volverá a estos parámetros y los configurará con las direcciones IP de puerta de enlace de red virtual correspondientes una vez que Azure la genere.
6. En el **espacio de direcciones de cliente**, use el espacio de direcciones de la otra red virtual. Consulte el ejemplo de planeación. Haga clic en **Aceptar** para guardar la configuración y vuelva a la página **Nueva conexión VPN**.

    ![sitio local](./media/vpn-gateway-howto-vnet-vnet-portal-classic/localsite.png)

## <a name="gw"></a>Paso 4: Creación de la puerta de enlace de red virtual

Cada red virtual debe tener una puerta de enlace de red virtual. La puerta de enlace de red virtual enruta el tráfico y lo cifra.

1. En la página **Nueva conexión VPN**, seleccione la casilla **Crear puerta de enlace inmediatamente**.
2. Haga clic en **Subred, tamaño y tipo de enrutamiento**. En la página **Configuración de la puerta de enlace**, haga clic en **Subred**.
3. El nombre de la subred de puerta de enlace se rellena automáticamente con el nombre "GatewaySubnet" requerido. **Intervalo de direcciones** contiene las direcciones IP que se asignan a los servicios de VPN Gateway. Algunas configuraciones permiten una subred de puerta de enlace de /29, pero es mejor usar una de /28 o /27 para incorporar futuras configuraciones que pueden requerir más direcciones IP para los servicios de puerta de enlace. En la configuración de ejemplo, usamos 10.11.1.0/27. Ajuste el espacio de direcciones y, luego, haga clic en **Aceptar**.
4. Configure el **tamaño de la puerta de enlace**. Esta configuración se refiere a la [SKU de puerta de enlace](vpn-gateway-about-vpn-gateway-settings.md#gwsku).
5. Configure el **tipo de enrutamiento**. El tipo de enrutamiento de esta configuración debe ser **Dinámico**. No puede cambiar el tipo de enrutamiento más adelantes, a menos que anule la puerta de enlace y cree una nueva.
6. Haga clic en **Aceptar**.
7. En la página **Nueva conexión VPN**, haga clic en **Aceptar** para empezar a crear la puerta de enlace de red virtual. La creación de una puerta de enlace suele tardar 45 minutos o más, según la SKU de la puerta de enlace seleccionada.

## <a name="vnet4settings"></a>Paso 5: Configuración de las opciones de TestVNet4

Repita los pasos para [crear un sitio local](#localsite) y [crear la puerta de enlace de red virtual](#gw) para configurar TestVNet4, reemplazando los valores cuando sea necesario. Si hace esto como ejercicio, use los [valores de ejemplo](#vnetvalues).

## <a name="updatelocal"></a>Paso 6: Actualización de los sitios locales

Una vez que se crean las puertas de enlace de red virtual para ambas redes virtuales, debe ajustar los valores de **dirección IP de VPN Gateway** de los sitios locales.

|Nombre de red virtual|Sitio conectado|Dirección IP de la puerta de enlace|
|:--- |:--- |:--- |
|TestVNet1|VNet4Local|Dirección IP de VPN Gateway para TestVNet4|
|TestVNet4|VNet1Local|Dirección IP de VPN Gateway para TestVNet1|

### <a name="part-1---get-the-virtual-network-gateway-public-ip-address"></a>Parte 1: Obtención de la dirección IP pública de puerta de enlace de red virtual

1. Ubique la red virtual en Azure Portal.
2. Haga clic para abrir la página **Información general** de la red virtual. En la página, en **Conexiones VPN**, puede ver la dirección IP de la puerta de enlace de red virtual.

  ![Dirección IP pública](./media/vpn-gateway-howto-vnet-vnet-portal-classic/publicIP.png)
3. Copie la dirección IP. La usará en la sección siguiente.
4. Repita estos pasos para TestVNet4

### <a name="part-2---modify-the-local-sites"></a>Parte 2: Modificación de los sitios locales

1. Ubique la red virtual en Azure Portal.
2. Haga clic en el sitio local en la página **Información general** de la red virtual.

  ![Sitio local creado](./media/vpn-gateway-howto-vnet-vnet-portal-classic/local.png)
3. En la página **Conexiones VPN de sitio a sitio**, haga clic en el nombre del sitio local que desea modificar.

  ![Sitio local abierto](./media/vpn-gateway-howto-vnet-vnet-portal-classic/openlocal.png)
4. Haga clic en el **sitio local** que desea modificar.

  ![modificación de sitio](./media/vpn-gateway-howto-vnet-vnet-portal-classic/connections.png)
5. Actualice la **dirección IP de VPN Gateway** y haga clic en **Aceptar** para guardar la configuración.

  ![dirección IP de puerta de enlace](./media/vpn-gateway-howto-vnet-vnet-portal-classic/gwupdate.png)
6. Cierre las otras páginas.
7. Repita estos pasos para TestVNet4.

## <a name="getvalues"></a>Paso 7: Recuperación de valores desde el archivo de configuración de red

Cuando crea redes virtuales clásicas en Azure Portal, el nombre que ve no es el nombre completo que usa para PowerShell. Por ejemplo, una red virtual que pareciera tener el nombre **TestVNet1** en el portal podría tener un nombre mucho más largo en el archivo de configuración de red. El nombre podría ser similar al siguiente: **Group ClassicRG TestVNet1**. Cuando cree conexiones, es importante usar los valores que ve en el archivo de configuración de red.

En los pasos siguientes, se conectará a la cuenta de Azure y descargará y verá el archivo de configuración de red para obtener los valores requeridos para las conexiones.

1. Descargue e instale la versión más reciente de los cmdlets de PowerShell para Azure Service Management (SM). Para más información, vea [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

2. Abra la consola de PowerShell con privilegios elevados y conéctela a su cuenta. Use el siguiente ejemplo para conectarse:

  ```powershell
  Login-AzureRmAccount
  ```

  Compruebe las suscripciones para la cuenta.

  ```powershell
  Get-AzureRmSubscription
  ```

  Si tiene varias suscripciones, seleccione la que quiera usar.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```

  A continuación, use el cmdlet siguiente para agregar la suscripción de Azure a PowerShell para el modelo de implementación clásica.

  ```powershell
  Add-AzureAccount
  ```
3. Exporte y visualice el archivo de configuración de red. Cree un directorio en el equipo y, a continuación, exporte el archivo de configuración de red al directorio. En este ejemplo, se exporta el archivo de configuración de red a **C:\AzureNet**.

  ```powershell
  Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
  ```
4. Abra el archivo con un editor de texto y consulte los nombres de las redes virtuales y los sitios. Estos serán el nombre que verá cuando cree las conexiones.<br>Los nombres de las redes virtuales aparecen como **VirtualNetworkSite name =**<br>Los nombres de los sitios aparecen como **LocalNetworkSiteRef name =**

## <a name="createconnections"></a>Paso 8: Creación de las conexiones de VPN Gateway

Una vez completados todos los pasos anteriores, puede establecer las claves compartidas previamente de IPsec/IKE y cree la conexión. Este conjunto de pasos usa PowerShell. Las conexiones de red virtual a red virtual del modelo de implementación clásica no se puede configurar en Azure Portal.

En los ejemplos, verá que la clave compartida es exactamente la misma. Siempre debe coincidir con la clave compartida. Asegúrese de reemplazar los valores de estos ejemplos por los nombres exactos de las redes locales y los sitios de red local.

1. Cree la conexión de TestVNet1 a TestVNet4.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet1' `
  -LocalNetworkSiteName '17BE5E2C_VNet4Local' -SharedKey A1b2C3D4
  ```
2. Cree la conexión de TestVNet4 a TestVNet1.

  ```powershell
  Set-AzureVNetGatewayKey -VNetName 'Group ClassicRG TestVNet4' `
  -LocalNetworkSiteName 'F7F7BFC7_VNet1Local' -SharedKey A1b2C3D4
  ```
3. Espere a que se inicialicen las conexiones. Una vez que se inicializa la puerta de enlace, el estado pasa a ser "Correcto".

  ```
  Error          :
  HttpStatusCode : OK
  Id             :
  Status         : Successful
  RequestId      :
  StatusCode     : OK
  ```

## <a name="faq"></a>Consideraciones de red virtual a red virtual para redes virtuales clásicas
* Las redes virtuales pueden estar en la misma suscripción o en suscripciones distintas.
* Las redes virtuales pueden estar en la misma región de Azure o en regiones distintas (ubicaciones).
* Un servicio en la nube o un punto de conexión de equilibrio de carga no puede abarcar varias redes virtuales, aunque estas estén conectadas entre sí.
* La conexión simultánea de varias redes virtuales no requiere dispositivos VPN locales.
* VNet a VNet admite la conexión a Azure Virtual Networks. No admite la conexión de máquinas virtuales o servicios en la nube que no estén implementados en una red virtual.
* Red virtual a red virtual requiere puertas de enlace de enrutamiento dinámico. No se admiten puertas de enlace de enrutamiento estático de Azure.
* La conectividad de red virtual se puede usar de forma simultánea con VPN de varios sitios. Existe un máximo de 10 túneles de VPN para una VPN Gateway de red virtual conectada a otras redes virtuales o sitios locales.
* Los espacios de direcciones de las redes virtuales y de los sitios de red local no se pueden solapar. Los espacios de direcciones solapados provocarán un error al crear redes virtuales o al cargar archivos de configuración netcfg.
* No se admiten los túneles redundantes entre un par de redes virtuales.
* Todos los túneles de VPN para la red virtual, incluidas las VPN de punto a sitio (P2S), comparten el ancho de banda disponible para la VPN Gateway y el mismo Acuerdo de Nivel de Servicio de tiempo de actividad de VPN Gateway de Azure.
* El tráfico VNet a VNet viaja a través de la red troncal de Azure.

## <a name="next-steps"></a>Pasos siguientes
Compruebe las conexiones. Consulte [Comprobación de una conexión de VPN Gateway](vpn-gateway-verify-connection-resource-manager.md).
