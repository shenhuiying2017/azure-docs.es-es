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
ms.date: 11/29/2017
ms.author: cherylmc
ms.openlocfilehash: 406cb4faf53bde5f615593e2e904d91a1d90a729
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-the-azure-portal"></a>Configuración de una conexión de VPN Gateway de red virtual a red virtual mediante Azure Portal

En este artículo se muestra cómo conectar redes virtuales mediante el tipo de conexión de red virtual a red virtual. Las redes virtuales pueden estar en la misma región o en distintas, así como pertenecer a una única suscripción o a varias. Al conectar redes virtuales de distintas suscripciones, estas no necesitan estar asociadas con el mismo inquilino de Active Directory. 

Los pasos descritos en este artículo se aplican al modelo de implementación de Resource Manager y utilizan Azure Portal. También se puede crear esta configuración con una herramienta o modelo de implementación distintos, mediante la selección de una opción diferente en la lista siguiente:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [CLI de Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal de Azure clásico](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Conexión de diferentes modelos de implementación - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Conexión de diferentes modelos de implementación - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

![diagrama de v2v](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

## <a name="about"></a>Acerca de la conexión de redes virtuales

Hay varias formas de conectar redes virtuales. Las siguientes secciones describen distintas formas de conectar redes virtuales.

### <a name="vnet-to-vnet"></a>De red virtual a red virtual

La configuración de una conexión entre redes virtuales es una buena manera de conectar redes virtuales fácilmente. La conexión de una red virtual a otra mediante el tipo de conexión entre redes virtuales (VNet2VNet) es parecida a la creación de una conexión IPsec de sitio a sitio en una ubicación local. Ambos tipos de conectividad usan una puerta de enlace de VPN para proporcionar un túnel seguro mediante IPsec/IKE y los dos funcionan de la misma forma en lo relativo a la comunicación. La diferencia entre ambos tipos de conexión radica en la manera en que se configura la puerta de enlace de red local. Al crear una conexión entre redes virtuales, no se ve el espacio de direcciones de la puerta de enlace de red local. Se crea y rellena automáticamente. Si actualiza el espacio de direcciones de una de las redes virtuales, la otra sabe automáticamente cómo realizar el enrutamiento al espacio de direcciones actualizado. La creación de una conexión entre redes virtuales suele ser más rápida y sencilla que la creación de una conexión de sitio a sitio entre redes virtuales.

### <a name="site-to-site-ipsec"></a>De sitio a sitio (IPsec)

Si puntualmente trabaja con una configuración de red complicada, puede que prefiera conectar sus redes virtuales mediante los pasos que se indican en [Creación de una red virtual con una conexión VPN de sitio a sitio mediante PowerShell](vpn-gateway-howto-site-to-site-resource-manager-portal.md) en su lugar. Cuando se usan las instrucciones para la conexión IPsec de sitio a sitio, las puertas de enlace de red locales se crean y se configuran manualmente. La puerta de enlace de red local de cada red virtual trata a la otra red virtual como un sitio local. De esta forma, puede especificar más espacio de direcciones para la puerta de enlace de red local a fin de enrutar el tráfico. Si el espacio de direcciones de una red virtual cambia, es preciso que actualice la puerta de enlace de red local correspondiente para reflejarlo. No se actualiza automáticamente.

### <a name="vnet-peering"></a>Emparejamiento de VNET

Puede que desee considerar conectar sus redes virtuales mediante el emparejamiento de VNET. El emparejamiento de VNET no utiliza una puerta de enlace de VPN y tiene distintas restricciones. Además, [los precios del emparejamiento de VNET](https://azure.microsoft.com/pricing/details/virtual-network) se calculan de forma diferente que los [precios de VPN Gateway entre redes virtuales](https://azure.microsoft.com/pricing/details/vpn-gateway). Para más información, consulte [Emparejamiento de VNET](../virtual-network/virtual-network-peering-overview.md).

## <a name="why"></a>¿Por qué crear una conexión de red virtual a red virtual?

Puede que desee conectar redes virtuales con una conexión entre redes virtuales por las siguientes razones:

* **Presencia geográfica y redundancia geográfica entre regiones**

  * Puede configurar su propia replicación geográfica o sincronización con conectividad segura sin recurrir a los puntos de conexión a Internet.
  * Con el Equilibrador de carga y el Administrador de tráfico de Azure, puede configurar cargas de trabajo de alta disponibilidad con redundancia geográfica en varias regiones de Azure. Por ejemplo, puede configurar AlwaysOn de SQL con grupos de disponibilidad distribuidos en varias regiones de Azure.
* **Aplicaciones regionales de niveles múltiples con aislamiento o un límite administrativo**

  * Dentro de la misma región, se pueden configurar aplicaciones de niveles múltiples con varias redes virtuales conectadas entre sí para cumplir requisitos administrativos o de aislamiento.

Se puede combinar la comunicación entre redes virtuales con configuraciones de varios sitios. Esto permite establecer topologías de red que combinan la conectividad entre entornos locales con la conectividad entre redes virtuales, como se muestra en el diagrama siguiente:

![Acerca de las conexiones](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Acerca de las conexiones")

Este artículo le ayuda a conectar redes virtuales mediante el tipo de conexión entre redes virtuales. Al usar estos pasos como un ejercicio, puede utilizar los valores de ejemplo. En el ejemplo, las redes virtuales se encuentran en la misma suscripción, pero en distintos grupos de recursos. Si las redes virtuales se encuentran en distintas suscripciones, no se puede crear la conexión en el portal. Puede usar [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) o [CLI](vpn-gateway-howto-vnet-vnet-cli.md). Para más información acerca de las conexiones de red virtual a red virtual, consulte [P+F sobre conexiones de red virtual a red virtual](#faq) al final de este artículo.

### <a name="values"></a>Configuración de ejemplo

**Valores para TestVNet1:**

* Nombre de red virtual: TestVNet1
* Espacio de direcciones: 10.11.0.0/16
* Suscripción: seleccione la suscripción que desea usar
* Grupo de recursos: TestRG1
* Ubicación: Este de EE. UU.
* Nombre de subred: FrontEnd
* Intervalo de direcciones de subred: 10.11.0.0/24
* Nombre de subred de puerta de enlace: GatewaySubnet (se rellenará de forma automática en el portal)
* Intervalo de direcciones de subred de puerta de enlace: 10.11.255.0/27
* Servidor DNS: utilice la dirección IP del servidor DNS
* Nombre de la puerta de enlace de red virtual: TestVNet1GW
* Tipo de puerta de enlace: VPN
* Tipo de VPN: basada en rutas
* SKU: seleccione la SKU de puerta de enlace que desee utilizar
* Nombre de dirección IP pública: TestVNet1GWIP
* Nombre de conexión: TestVNet1toTestVNet4
* Clave compartida: puede crear la clave compartida. En este ejemplo, usaremos abc123. Lo importante es que al crear la conexión entre las redes virtuales, el valor debe coincidir.

**Valores para TestVNet4:**

* Nombre de red virtual: TestVNet4
* Espacio de direcciones: 10.41.0.0/16
* Suscripción: seleccione la suscripción que desea usar
* Grupo de recursos: TestRG4
* Ubicación: Oeste de EE. UU.
* Nombre de subred: FrontEnd
* Intervalo de direcciones de subred: 10.41.0.0/24
* Nombre de GatewaySubnet: GatewaySubnet (se rellenará de forma automática en el portal)
* Intervalo de direcciones de GatewaySubnet: 10.41.255.0/27
* Servidor DNS: utilice la dirección IP del servidor DNS
* Nombre de puerta de enlace de red virtual: TestVNet4GW
* Tipo de puerta de enlace: VPN
* Tipo de VPN: basada en rutas
* SKU: seleccione la SKU de puerta de enlace que desee utilizar
* Nombre de dirección IP pública: TestVNet4GWIP
* Nombre de conexión: TestVNet4toTestVNet1
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

## <a name="dns"></a>4. Especificación de un servidor DNS (opcional)
No se requiere la DNS para las conexiones de red virtual a red virtual. Sin embargo, si desea tener resolución de nombres para los recursos que se implementan en su red virtual, debe especificar un servidor DNS. Esta configuración le permite especificar el servidor DNS que desea usar para la resolución de nombres para esta red virtual. No crea un servidor DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="VNetGateway"></a>5. Creación de una puerta de enlace de red virtual
En este paso, se crea la puerta de enlace para la red virtual. La creación de una puerta de enlace suele tardar 45 minutos o más, según la SKU de la puerta de enlace seleccionada. Si va a crear esta configuración como ejercicio, puede consultar [los valores de ejemplo](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Creación de una puerta de enlace de red virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="CreateTestVNet4"></a>6. Creación y configuración de TestVNet4
Una vez que haya configurado TestVNet1, cree TestVNet4 repitiendo los pasos anteriores, pero reemplazando los valores por los de TestVNet4. No es preciso esperar a que la puerta de enlace de red virtual de TestVNet1 haya terminado de crearse para configurar TestVNet4. Si utiliza sus propios valores, asegúrese de que los espacios de direcciones no se superponen con las redes virtuales a las que desea conectarse.

## <a name="TestVNet1Connection"></a>7. Configuración de la conexión de puerta de enlace TestVNet1
Cuando se hayan completado las puertas de enlace de red virtual de TestVNet1 y TestVNet4, puede crear las conexiones de dichas puertas de enlace. En esta sección, se crea una conexión de VNet1 a VNet4. Estos pasos solo funcionan para las redes virtuales en la misma suscripción. Si las redes virtuales se encuentran en distintas suscripciones, debe usar PowerShell para realizar la conexión. Vea el artículo [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). Sin embargo, si las redes virtuales se encuentran en distintos grupos de recursos de la misma suscripción, puede conectarlas mediante el portal.

1. En **Todos los recursos**, navegue hasta la puerta de enlace de red virtual de la red virtual. Por ejemplo, **TestVNet1GW**. Haga clic en **TestVNet1GW** para abrir la página de la puerta de enlace de la red virtual.

  ![Página de conexiones](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/1to4connect2.png "Página de conexiones")
2. Haga clic en **+ Agregar** para abrir la página **Agregar conexión**.

  ![Agregar conexión](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add.png "Agregar una conexión")
3. En la página **Agregar conexión**, en el campo Nombre, escriba el nombre de la conexión. Por ejemplo, **TestVNet1toTestVNet4**.
4. En **Tipo de conexión**, seleccione **De VNet a VNet** en la lista desplegable.
5. El valor del campo **Primera puerta enlace de red virtual** se rellena automáticamente porque la conexión se crea desde la puerta de enlace de red virtual especificada.
6. El campo **Segunda puerta enlace de red virtual** es la puerta de enlace de la red virtual con la que desea crear una conexión. Haga clic en **Elegir otra puerta de enlace de red virtual** para abrir la página **Elegir puerta de enlace de red virtual**.
7. Fíjese en las puertas de enlace de red virtual que se enumeran en esta página. Observe que solo se muestran las que se encuentran en su suscripción. Si desea conectarse a una puerta de enlace de red virtual que no está en su suscripción, consulte el [artículo de PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).
8. Haga clic en la puerta de enlace de red virtual a la que desea conectarse.
9. En el campo **Clave compartida**, escriba una clave compartida para la conexión. Dicha clave puede generarla o crearla manualmente. En una conexión de sitio a sitio, la clave del dispositivo local y la de la conexión de puerta de enlace de red virtual serán exactamente iguales. Aquí el concepto es similar, salvo en que en lugar de conectarse a un dispositivo VPN, la conexión se establece con otra puerta de enlace de red virtual.
10. Para guardar los cambios, haga clic en **Aceptar** en la parte inferior de la página.

## <a name="TestVNet4Connection"></a>8. Configuración de la conexión de puerta de enlace TestVNet4
A continuación, cree una conexión de TestVNet4 a TestVNet1. En el portal, busque la puerta de enlace de red virtual asociada a TestVNet4. Siga los pasos de la sección anterior, reemplazando los valores para crear una conexión de TestVNet4 a TestVNet1. Asegúrese de que utiliza la misma clave compartida.

## <a name="VerifyConnection"></a>9. Comprobación de las conexiones

Busque la puerta de enlace de red virtual en el portal. En la página de la puerta de enlace de red virtual, haga clic en **Conexiones** para ver la página de conexiones de la puerta de enlace de red virtual. Una vez establecida la conexión, verá el cambio de los valores de estado de **Correcto** y **Conectado**. Haga doble clic en una conexión para abrir la página **Essentials** y ver más información.

![Correcto](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Correcto")

Cuando se inicia el flujo de datos, aparecen los valores para los datos de entrada y los datos de salida.

![Essentials](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="to-add-additional-connections"></a>Para agregar conexiones adicionales

Si desea agregar conexiones adicionales, vaya a la puerta de enlace de red virtual desde la que desea crear la conexión y, después, haga clic en **Conexiones**. Puede crear otra conexión entre redes virtual o bien crear una conexión de IPsec de sitio a sitio en una ubicación local. Asegúrese de ajustar el **Tipo de conexión** para coincidir con el tipo de conexión que desea crear. Antes de crear conexiones adicionales, compruebe que el espacio de direcciones de la red virtual no se superponga con ninguno de los espacios de direcciones a los que desea conectarse. Para saber qué pasos son necesarios para crear una conexión de sitio a sitio, consulte [Creación de una conexión de sitio a sitio](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="faq"></a>P+F sobre conexiones de red virtual a red virtual
Vea los detalles de preguntas más frecuentes para más información acerca de las conexiones de red virtual a red virtual.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Pasos siguientes

Consulte [Seguridad de red](../virtual-network/security-overview.md) para obtener más información sobre cómo limitar el tráfico de red a los recursos de una red virtual.

Consulte [Enrutamiento del tráfico de redes virtuales](../virtual-network/virtual-networks-udr-overview.md) para obtener más información sobre cómo enruta Azure el tráfico entre los recursos locales, de Internet y de Azure.