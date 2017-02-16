---
title: "Conexión de redes virtuales mediante el método de implementación de Resource Manager y Azure Portal | Microsoft Docs"
description: "Crear una conexión de VPN Gateway entre redes virtuales mediante Resource Manager y Azure Portal."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: a7015cfc-764b-46a1-bfac-043d30a275df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 28d81fe312195b9a9094e1ed066f5cba57c76933
ms.openlocfilehash: b85017913316a450fe19f1760abff6a86f933e2e


---
# <a name="configure-a-vnet-to-vnet-connection-using-the-azure-portal"></a>Configuración de una conexión de red virtual a red virtual mediante Azure Portal
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Clásico - Portal clásico](virtual-networks-configure-vnet-to-vnet-connection.md)
> 
> 

Este artículo le guiará a través de los pasos necesarios para crear una conexión entre redes virtuales con el modelo de implementación de Resource Manager mediante VPN Gateway y Azure Portal.

Al usar Azure Portal para conectar redes virtuales, estas deben estar en la misma suscripción. Aunque las redes virtuales se encuentren en suscripciones diferentes, es posible conectarlas mediante los pasos de [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

![diagrama de v2v](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implementación y métodos para conexiones de red virtual a red virtual
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

La siguiente tabla muestra los modelos de implementación disponibles actualmente y los métodos para las configuraciones de red virtual a red virtual. Cuando aparezca algún artículo con pasos de configuración, creamos un vínculo directo a él desde esta tabla.

[!INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**Emparejamiento de VNET**

[!INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]

## <a name="about-vnet-to-vnet-connections"></a>Acerca de conexiones de red virtual a red virtual
La conexión de una red virtual a otra es muy parecida a la conexión de una red virtual a una ubicación de un sitio local. Ambos tipos de conectividad usan una puerta de enlace de VPN de Azure para proporcionar un túnel seguro con IPsec/IKE. Las redes virtuales que se conecten pueden estar en regiones y suscripciones distintas.

Incluso puede combinar la comunicación de red virtual a red virtual con configuraciones de varios sitios. Esto permite establecer topologías de red que combinan la conectividad entre entornos locales con la conectividad entre redes virtuales, como se muestra en el diagrama siguiente:

![Acerca de las conexiones](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "About connections")

### <a name="why-connect-virtual-networks"></a>¿Por qué debería conectarse a redes virtuales?
Puede que desee conectar redes virtuales por las siguientes razones:

* **Presencia geográfica y redundancia geográfica entre regiones**
  
  * Puede configurar su propia replicación geográfica o sincronización con conectividad segura sin recurrir a los puntos de conexión a Internet.
  * Con el Equilibrador de carga y el Administrador de tráfico de Azure, puede configurar cargas de trabajo de alta disponibilidad con redundancia geográfica en varias regiones de Azure. Por ejemplo, puede configurar AlwaysOn de SQL con grupos de disponibilidad distribuidos en varias regiones de Azure.
* **Aplicaciones regionales de niveles múltiples con aislamiento o un límite administrativo**
  
  * Dentro de la misma región, se pueden configurar aplicaciones de niveles múltiples con varias redes virtuales conectadas entre sí para cumplir requisitos administrativos o de aislamiento.

Para más información acerca de las conexiones de red virtual a red virtual, consulte [P+F sobre conexiones de red virtual a red virtual](#faq) al final de este artículo.

### <a name="a-namevaluesaexample-settings"></a><a name="values"></a>Configuración de ejemplo
Al usar estos pasos como un ejercicio, puede utilizar los siguientes valores de ejemplo. En los ejemplos, usamos varios espacios de direcciones para cada red virtual. Sin embargo, las configuraciones de red virtual a red virtual no requieren varios espacios de direcciones.

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

## <a name="a-namecreatvneta1-create-and-configure-testvnet1"></a><a name="CreatVNet"></a>1. Creación y configuración de TestVNet1
Si ya dispone de una red virtual, compruebe que la configuración sea compatible con el diseño de la puerta de enlace de VPN. Preste especial atención a las subredes que se pueden superponer con otras redes. Si tiene subredes superpuestas, la conexión no funcionará correctamente. Si la red virtual está configurada correctamente, puede comenzar con los pasos descritos en la sección [Especificación de un servidor DNS](#dns) .

### <a name="to-create-a-virtual-network"></a>Creación de una red virtual
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="a-namesubnetsa2-add-additional-address-space-and-create-subnets"></a><a name="subnets"></a>2. Incorporación de un espacio de direcciones adicional y creación de subredes
Una vez que la red virtual se haya creado, puede agregar un espacio de direcciones adicional y crear subredes.

[!INCLUDE [vpn-gateway-additional-address-space](../../includes/vpn-gateway-additional-address-space-include.md)]

## <a name="a-namegatewaysubneta3-create-a-gateway-subnet"></a><a name="gatewaysubnet"></a>3. Creación de una subred de puerta de enlace
Antes de conectar la red virtual a una puerta de enlace, es preciso crear la subred de la puerta de enlace de la red virtual a la que desea conectarse. Si es posible, es preferible crear una subred de puerta de enlace con un bloque CIDR de /28 o /27 para proporcionar suficientes direcciones IP para adaptarse a nuevos requisitos de configuración que puedan surgir en el futuro.

Si va a crear esta configuración como ejercicio, consulte estos [valores de ejemplo](#values) al crear la subred de la puerta de enlace.

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-gateway-subnet"></a>Creación de una subred de puerta de enlace
[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-rm-portal-include.md)]

## <a name="a-namednsservera4-specify-a-dns-server-optional"></a><a name="DNSServer"></a>4. Especificación de un servidor DNS (opcional)
Si desea tener resolución de nombres en las máquinas virtuales que se implementan en sus redes virtuales, debe especificar un servidor DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="a-namevnetgatewaya5-create-a-virtual-network-gateway"></a><a name="VNetGateway"></a>5. Creación de una puerta de enlace de red virtual
En este paso, se crea la puerta de enlace para la red virtual. Este paso puede tardar hasta 45 minutos en completarse. Si va a crear esta configuración como ejercicio, puede consultar [los valores de ejemplo](#values).

### <a name="to-create-a-virtual-network-gateway"></a>Creación de una puerta de enlace de red virtual
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="a-namecreatetestvnet4a6-create-and-configure-testvnet4"></a><a name="CreateTestVNet4"></a>6. Creación y configuración de TestVNet4
Una vez que haya configurado TestVNet1, cree TestVNet4 repitiendo los pasos anteriores, pero reemplazando los valores por los de TestVNet4. No es preciso esperar a que la puerta de enlace de red virtual de TestVNet1 haya terminado de crearse para configurar TestVNet4. Si utiliza sus propios valores, asegúrese de que los espacios de direcciones no se superponen con las redes virtuales a las que desea conectarse.

## <a name="a-nametestvnet1connectiona7-configure-the-testvnet1-connection"></a><a name="TestVNet1Connection"></a>7. Configuración de la conexión TestVNet1
Cuando se hayan completado las puertas de enlace de red virtual de TestVNet1 y TestVNet4, puede crear las conexiones de dichas puertas de enlace. En esta sección, creará una conexión de VNet1 a VNet4.

1. En **Todos los recursos**, navegue hasta la puerta de enlace de red virtual de la red virtual. Por ejemplo, **TestVNet1GW**. Haga clic en **TestVNet1GW** para abrir la hoja de la puerta de enlace de la red virtual.
   
    ![Hoja de conexiones](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/settings_connection.png "Connections blade")
2. Haga clic en **+ Agregar** para abrir la hoja **Agregar conexión**.
3. En la hoja **Agregar conexión**, en el campo Nombre, escriba el nombre de la conexión. Por ejemplo, **TestVNet1toTestVNet4**.
   
    ![Nombre de conexión](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v1tov4.png "Connection name")
4. En **Tipo de conexión**, seleccione **De VNet a VNet** en la lista desplegable.
5. El valor del campo **Primera puerta enlace de red virtual** se rellena automáticamente porque la conexión se crea desde la puerta de enlace de red virtual especificada.
6. El campo **Segunda puerta enlace de red virtual** es la puerta de enlace de la red virtual con la que desea crear una conexión. Haga clic en **Elegir otra puerta de enlace de red virtual** para abrir la hoja **Elegir puerta de enlace de red virtual**.
   
    ![Agregar conexión](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/add_connection.png "Add a connection")
7. Fíjese en las puertas de enlace de red virtual que se enumeran en esta hoja. Observe que solo se muestran las que se encuentran en su suscripción. Si desea conectarse a una puerta de enlace de red virtual que no está en su suscripción, consulte el [artículo de PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). 
8. Haga clic en la puerta de enlace de red virtual a la que desea conectarse.
9. En el campo **Clave compartida**, escriba una clave compartida para la conexión. Dicha clave puede generarla o crearla manualmente. En una conexión de sitio a sitio, la clave del dispositivo local y la de la conexión de puerta de enlace de red virtual serán exactamente iguales. Aquí el concepto es similar, salvo en que en lugar de conectarse a un dispositivo VPN, la conexión se establece con otra puerta de enlace de red virtual.
   
    ![Clave compartida](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/sharedkey.png "Shared key")
10. Haga clic en **Aceptar** en la parte inferior de la hoja para guardar los cambios.

## <a name="a-nametestvnet4connectiona8-configure-the-testvnet4-connection"></a><a name="TestVNet4Connection"></a>8. Configuración de la conexión TestVNet4
A continuación, cree una conexión de TestVNet4 a TestVNet1. Utilice el mismo método que para crear la conexión de TestVNet1 a TestVNet4. Asegúrese de que utiliza la misma clave compartida.

## <a name="a-nameverifyconnectiona9-verify-your-connection"></a><a name="VerifyConnection"></a>9. Comprobación de la conexión
Compruebe la conexión. Realice las siguientes acciones en todas las puertas de enlace de red virtual:

1. Busque la hoja de la puerta de enlace de red virtual. Por ejemplo, **TestVNet4GW**. 
2. En la hoja de la puerta de enlace de red virtual, haga clic en **Conexiones** para ver la hoja de conexiones de la puerta de enlace de red virtual.

Vea las conexiones y compruebe el estado. Una vez creada la conexión, en Estado verá los valores **Correcto** y **Conectado**.

![Correcto](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connected.png "Succeeded")

Puede hacer doble clic en las conexiones por separado para más información acerca de cada una de ellas.

![Essentials](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/essentials.png "Essentials")

## <a name="a-namefaqavnet-to-vnet-faq"></a><a name="faq"></a>P+F sobre conexiones de red virtual a red virtual
Vea los detalles de preguntas más frecuentes para más información acerca de las conexiones de red virtual a red virtual.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte la [documentación sobre máquinas virtuales](https://docs.microsoft.com/azure/#pivot=services&panel=Compute) para más información.



<!--HONumber=Nov16_HO4-->


