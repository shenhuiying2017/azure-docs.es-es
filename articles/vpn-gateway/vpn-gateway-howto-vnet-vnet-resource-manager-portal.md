---
title: "Conexión de una red virtual de Azure a otra red virtual: portal | Microsoft Docs"
description: "Crear una conexión de VPN Gateway entre redes virtuales mediante Resource Manager y Azure Portal."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/21/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 50934bcc065b4039467d7371d4bbac11f5933888
ms.lasthandoff: 04/25/2017


---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-the-azure-portal"></a>Configuración de una conexión de VPN Gateway de red virtual a red virtual mediante Azure Portal

En este artículo se explica cómo crear una conexión de VPN Gateway entre redes virtuales. Las redes virtuales pueden estar en la misma región o en distintas, así como pertenecer a una única suscripción o a varias. Los pasos descritos en este artículo se aplican al modelo de implementación de Resource Manager y Azure Portal. También se puede crear esta configuración con una herramienta o modelo de implementación distintos, mediante la selección de una opción diferente en la lista siguiente:

> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Clásico - Azure Portal](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Conexión de diferentes modelos de implementación - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Conexión de diferentes modelos de implementación - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![diagrama de v2v](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

La conexión de una red virtual a otra es muy parecida a la conexión de una red virtual a una ubicación de un sitio local. Ambos tipos de conectividad usan una puerta de enlace de VPN para proporcionar un túnel seguro con IPsec/IKE. Si las redes virtuales están en la misma región, podría pensar en conectarlas mediante emparejamiento de VNET. El emparejamiento de VNET no usa VPN Gateway. Para más información, consulte [Emparejamiento de VNET](../virtual-network/virtual-network-peering-overview.md).

Se puede combinar la comunicación entre redes virtuales con configuraciones de varios sitios. Esto permite establecer topologías de red que combinan la conectividad entre entornos locales con la conectividad entre redes virtuales, como se muestra en el diagrama siguiente:

![Acerca de las conexiones](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Acerca de las conexiones")

### <a name="why-connect-virtual-networks"></a>¿Por qué debería conectarse a redes virtuales?

Puede que desee conectar redes virtuales por las siguientes razones:

* **Presencia geográfica y redundancia geográfica entre regiones**
  
  * Puede configurar su propia replicación geográfica o sincronización con conectividad segura sin recurrir a los puntos de conexión a Internet.
  * Con el Equilibrador de carga y el Administrador de tráfico de Azure, puede configurar cargas de trabajo de alta disponibilidad con redundancia geográfica en varias regiones de Azure. Por ejemplo, puede configurar AlwaysOn de SQL con grupos de disponibilidad distribuidos en varias regiones de Azure.
* **Aplicaciones regionales de niveles múltiples con aislamiento o un límite administrativo**
  
  * Dentro de la misma región, se pueden configurar aplicaciones de niveles múltiples con varias redes virtuales conectadas entre sí para cumplir requisitos administrativos o de aislamiento.

Para más información acerca de las conexiones de red virtual a red virtual, consulte [P+F sobre conexiones de red virtual a red virtual](#faq) al final de este artículo. Tenga en cuenta que si las redes virtuales se encuentran en distintas suscripciones, no se puede crear la conexión en el portal. Puede usar [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

### <a name="values"></a>Configuración de ejemplo
Al usar estos pasos como un ejercicio, puede utilizar los valores de ejemplo. En los ejemplos, usamos varios espacios de direcciones para cada red virtual. Sin embargo, las configuraciones de red virtual a red virtual no requieren varios espacios de direcciones.

**Valores para TestVNet1:**

* Nombre de red virtual: TestVNet1
* Espacio de direcciones: 10.11.0.0/16
  * Nombre de subred: FrontEnd
  * Intervalo de direcciones de subred: 10.11.0.0/24
* Grupo de recursos: TestRG1
* Ubicación: Este de EE. UU.
* Espacio de direcciones: 10.12.0.0/16
  * Nombre de subred: BackEnd
  * Intervalo de direcciones de subred: 10.12.0.0/24
* Nombre de subred de puerta de enlace: GatewaySubnet (se rellenará de forma automática en el portal)
  * Intervalo de direcciones de subred de puerta de enlace: 10.11.255.0/27
* Servidor DNS: utilice la dirección IP del servidor DNS
* Nombre de la puerta de enlace de red virtual: TestVNet1GW
* Tipo de puerta de enlace: VPN
* Tipo de VPN: basada en rutas
* SKU: seleccione la SKU de puerta de enlace que desee utilizar
* Nombre de dirección IP pública: TestVNet1GWIP
* Valores de conexión:
  * Nombre: TestVNet1toTestVNet4
  * Clave compartida: puede crear la clave compartida. En este ejemplo, usaremos abc123. Lo importante es que al crear la conexión entre las redes virtuales, el valor debe coincidir.

**Valores para TestVNet4:**

* Nombre de red virtual: TestVNet4
* Espacio de direcciones: 10.41.0.0/16
  * Nombre de subred: FrontEnd
  * Intervalo de direcciones de subred: 10.41.0.0/24
* Grupo de recursos: TestRG1
* Ubicación: Oeste de EE. UU.
* Espacio de direcciones: 10.42.0.0/16
  * Nombre de subred: BackEnd
  * Intervalo de direcciones de subred: 10.42.0.0/24
* Nombre de GatewaySubnet: GatewaySubnet (se rellenará de forma automática en el portal)
  * Intervalo de direcciones de GatewaySubnet: 10.41.255.0/27
* Servidor DNS: utilice la dirección IP del servidor DNS
* Nombre de puerta de enlace de red virtual: TestVNet4GW
* Tipo de puerta de enlace: VPN
* Tipo de VPN: basada en rutas
* SKU: seleccione la SKU de puerta de enlace que desee utilizar
* Nombre de dirección IP pública: TestVNet4GWIP
* Valores de conexión:
  * Nombre: TestVNet4toTestVNet1
  * Clave compartida: puede crear la clave compartida. En este ejemplo, usaremos abc123. Lo importante es que al crear la conexión entre las redes virtuales, el valor debe coincidir.

## <a name="CreatVNet"></a>1. Creación y configuración de TestVNet1
Si ya dispone de una red virtual, compruebe que la configuración sea compatible con el diseño de la puerta de enlace de VPN. Preste especial atención a las subredes que se pueden superponer con otras redes. Si tiene subredes superpuestas, la conexión no funcionará correctamente. Si la red virtual está configurada correctamente, puede comenzar con los pasos descritos en la sección [Especificación de un servidor DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Creación de una red virtual
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="subnets"></a>2. Incorporación de un espacio de direcciones adicional y creación de subredes
Una vez que la red virtual se haya creado, puede agregar un espacio de direcciones adicional y crear subredes.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="gatewaysubnet"></a>3. Creación de una subred de puerta de enlace
Antes de conectar la red virtual a una puerta de enlace, es preciso crear la subred de la puerta de enlace de la red virtual a la que desea conectarse. Si es posible, es preferible crear una subred de puerta de enlace con un bloque CIDR de /28 o /27 para proporcionar suficientes direcciones IP para adaptarse a nuevos requisitos de configuración que puedan surgir en el futuro.

Si va a crear esta configuración como ejercicio, consulte estos [valores de ejemplo](#values) al crear la subred de la puerta de enlace.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Creación de una subred de puerta de enlace
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="DNSServer"></a>4. Especificación de un servidor DNS (opcional)
No se requiere la DNS para las conexiones de red virtual a red virtual. Sin embargo, si desea tener resolución de nombres para los recursos que se implementan en su red virtual, debe especificar un servidor DNS. Esta configuración le permite especificar el servidor DNS que desea usar para la resolución de nombres para esta red virtual. No crea un servidor DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. Creación de una puerta de enlace de red virtual
En este paso, se crea la puerta de enlace para la red virtual. La creación de una puerta de enlace suele tardar 45 minutos o más, según la SKU de la puerta de enlace seleccionada. Si va a crear esta configuración como ejercicio, puede consultar [los valores de ejemplo](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Creación de una puerta de enlace de red virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="CreateTestVNet4"></a>6. Creación y configuración de TestVNet4
Una vez que haya configurado TestVNet1, cree TestVNet4 repitiendo los pasos anteriores, pero reemplazando los valores por los de TestVNet4. No es preciso esperar a que la puerta de enlace de red virtual de TestVNet1 haya terminado de crearse para configurar TestVNet4. Si utiliza sus propios valores, asegúrese de que los espacios de direcciones no se superponen con las redes virtuales a las que desea conectarse.

## <a name="TestVNet1Connection"></a>7. Configuración de la conexión TestVNet1
Cuando se hayan completado las puertas de enlace de red virtual de TestVNet1 y TestVNet4, puede crear las conexiones de dichas puertas de enlace. En esta sección, creará una conexión de VNet1 a VNet4. Estos pasos solo funcionan para las redes virtuales en la misma suscripción. Si las redes virtuales se encuentran en distintas suscripciones, debe usar PowerShell para realizar la conexión. Vea el artículo [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

1. En **Todos los recursos**, navegue hasta la puerta de enlace de red virtual de la red virtual. Por ejemplo, **TestVNet1GW**. Haga clic en **TestVNet1GW** para abrir la hoja de la puerta de enlace de la red virtual.
   
    ![Hoja de conexiones](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "Hoja de conexiones")
2. Haga clic en **+ Agregar** para abrir la hoja **Agregar conexión**.
3. En la hoja **Agregar conexión**, en el campo Nombre, escriba el nombre de la conexión. Por ejemplo, **TestVNet1toTestVNet4**.
   
    ![Nombre de conexión](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "Nombre de conexión")
4. En **Tipo de conexión**, seleccione **De VNet a VNet** en la lista desplegable.
5. El valor del campo **Primera puerta enlace de red virtual** se rellena automáticamente porque la conexión se crea desde la puerta de enlace de red virtual especificada.
6. El campo **Segunda puerta enlace de red virtual** es la puerta de enlace de la red virtual con la que desea crear una conexión. Haga clic en **Elegir otra puerta de enlace de red virtual** para abrir la hoja **Elegir puerta de enlace de red virtual**.
   
    ![Agregar conexión](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "Agregar una conexión")
7. Fíjese en las puertas de enlace de red virtual que se enumeran en esta hoja. Observe que solo se muestran las que se encuentran en su suscripción. Si desea conectarse a una puerta de enlace de red virtual que no está en su suscripción, consulte el [artículo de PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). 
8. Haga clic en la puerta de enlace de red virtual a la que desea conectarse.
9. En el campo **Clave compartida**, escriba una clave compartida para la conexión. Dicha clave puede generarla o crearla manualmente. En una conexión de sitio a sitio, la clave del dispositivo local y la de la conexión de puerta de enlace de red virtual serán exactamente iguales. Aquí el concepto es similar, salvo en que en lugar de conectarse a un dispositivo VPN, la conexión se establece con otra puerta de enlace de red virtual.
   
    ![Clave compartida](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "Clave compartida")
10. Haga clic en **Aceptar** en la parte inferior de la hoja para guardar los cambios.

## <a name="TestVNet4Connection"></a>8. Configuración de la conexión TestVNet4
A continuación, cree una conexión de TestVNet4 a TestVNet1. Utilice el mismo método que para crear la conexión de TestVNet1 a TestVNet4. Asegúrese de que utiliza la misma clave compartida.

## <a name="VerifyConnection"></a>9. Comprobación de la conexión
Compruebe la conexión. Realice las siguientes acciones en todas las puertas de enlace de red virtual:

1. Busque la hoja de la puerta de enlace de red virtual. Por ejemplo, **TestVNet4GW**. 
2. En la hoja de la puerta de enlace de red virtual, haga clic en **Conexiones** para ver la hoja de conexiones de la puerta de enlace de red virtual.

Vea las conexiones y compruebe el estado. Una vez creada la conexión, en Estado verá los valores **Correcto** y **Conectado**.

![Correcto](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Correcto")

Puede hacer doble clic en las conexiones por separado para más información acerca de cada una de ellas.

![Essentials](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="faq"></a>P+F sobre conexiones de red virtual a red virtual
Vea los detalles de preguntas más frecuentes para más información acerca de las conexiones de red virtual a red virtual.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte la [documentación sobre máquinas virtuales](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) para más información.

