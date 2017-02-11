---
title: "Configuración de una red virtual y una puerta de enlace para ExpressRoute en el portal clásico | Microsoft Docs"
description: "Este artículo le guía en la configuración de una red virtual para ExpressRoute mediante el modelo de implementación clásica y el portal clásico."
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 688817d9-51c8-4372-9af8-33fa098c7c5a
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/20/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1206d7444f32216597a6f546c71131b2de0ec3f8


---
# <a name="create-a-virtual-network-for-expressroute-in-the-classic-portal"></a>Creación de una red virtual para ExpressRoute en el Portal clásico
Los pasos de este artículo le guiarán en la configuración de una red virtual y una puerta de enlace de red virtual para su uso con ExpressRoute mediante el modelo de implementación clásica y el portal clásico.

Si busca instrucciones para el modelo de implementación de Resource Manager, puede usar los siguientes artículos que le guiarán a través de cómo [crear una red virtual mediante PowerShell](../virtual-network/virtual-networks-create-vnet-arm-ps.md) y [agregar una puerta de enlace de VPN a una red virtual de Resource Manager para ExpressRoute](expressroute-howto-add-gateway-resource-manager.md).

**Información sobre los modelos de implementación de Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="create-a-classic-vnet-and-gateway"></a>Creación de una red virtual y puerta de enlace clásicas
Con los siguientes pasos podrá crear una red virtual y una puerta de enlace de red virtual clásicas. Si ya dispone de una red virtual clásica, consulte la sección [Configuración de una red virtual clásica ya existente](#config) en este artículo.

1. Inicie sesión en el [Portal de Azure clásico](http://manage.windowsazure.com).
2. En la esquina inferior izquierda de la pantalla, haga clic en **Nuevo**. En el panel de navegación, haga clic en **Servicios de red** y, a continuación, haga clic en **Red virtual**. Haga clic en **Creación personalizada** para iniciar el Asistente para configuración.
3. En la página **Detalles de red virtual** , escriba la siguiente información:
   
   * **Nombre** : nombre de la red virtual. Usará este nombre de red virtual al implementar las máquinas virtuales y las instancias PaaS, por lo que probablemente no querrá que este nombre sea muy complicado.
   * **Ubicación** : la ubicación está directamente relacionada con la ubicación física (región) en la que desea que residan los recursos (máquinas virtuales). Por ejemplo, si desea que las máquinas virtuales que implementa en la red virtual se encuentren físicamente en el Este de EE.UU., seleccione esa ubicación. No se puede cambiar la región asociada con la red virtual después de crearla.
4. En la página **Servidores DNS y conectividad VPN** , escriba la información siguiente y, a continuación, haga clic en la flecha siguiente situada en la parte inferior derecha. 
   
   * **Servidores DNS** : escriba el nombre del servidor DNS y la dirección IP, o seleccione un servidor DNS previamente registrado del menú contextual. Mediante este valor no se crea un servidor DNS. Le permite especificar el servidor DNS que desea usar para la resolución de nombres para esta red virtual.
   * **Conectividad de sitio a sitio**: seleccione la casilla **Configurar una VPN de sitio a sitio**.
   * **ExpressRoute**: active la casilla **Usar ExpressRoute**. Esta opción solo aparece si seleccionó **Configurar una VPN de sitio a sitio**.
   * **Red local** : es necesario disponer de un sitio de red local para ExpressRoute. Sin embargo, en el caso de una conexión de ExpressRoute, se omitirán los prefijos de dirección especificados para el sitio de red local. En su lugar, los prefijos de dirección anunciados a Microsoft a través del circuito de ExpressRoute se usarán con fines de enrutamiento.<BR>Si ya tiene una red local creada para la conexión de ExpressRoute, puede seleccionarla en la lista desplegable. En caso contrario, seleccione **Especifique una nueva red local**.
5. Aparece la página **Conectividad de sitio a sitio** si seleccionó la opción para especificar una nueva red local en el paso anterior. Para configurar la red local, escriba la siguiente información y, después, haga clic en la flecha siguiente. 
   
   * **Nombre** : el nombre que quiere para el sitio de red local.
   * **Espacio de direcciones** : incluidas la dirección IP de inicio y el CIDR (recuento de direcciones). Puede especificar cualquier intervalo de direcciones, siempre que no se solape con el intervalo de direcciones de la red virtual. Normalmente, esto debería especificar los intervalos de direcciones para las redes locales, pero en el caso de ExpressRoute, no se utiliza esta configuración. Sin embargo, esta configuración es necesaria para crear la red local cuando se utiliza el portal clásico.
   * **Agregar espacio de direcciones** : este ajuste no es relevante para ExpressRoute.
6. En la página **Espacios de direcciones de la red virtual** , escriba la información siguiente y, después, haga clic en la marca de verificación de la parte inferior derecha para configurar la red. 
   
   * **Espacio de direcciones** : incluidas la dirección IP de inicio y el recuento de direcciones. Compruebe que los espacios de direcciones especificados no se solapan con los espacios de direcciones que existen en la red local.
   * **Agregar subred** : incluidas Dirección IP de inicio y Recuento de direcciones. No se necesitan subredes adicionales.
   * **Agregar subred de puerta de enlace** : haga clic para agregar la subred de puerta de enlace. La subred de puerta de enlace se utiliza únicamente para la puerta de enlace de red virtual y es necesaria para esta configuración.<BR>El CIDR de la subred de puerta de enlace (recuento de direcciones) para ExpressRoute debe ser /28 o superior (/27, /26, etc.). Esto permite suficientes direcciones IP en esa subred para hacer que la configuración funcione. En el portal clásico, si ha seleccionado la casilla para usar ExpressRoute, el portal especifica una puerta de enlace con /28.  No se puede ajustar el recuento de direcciones de CIDR en el portal clásico. La subred de puerta de enlace aparecerá como **Gateway** en el portal clásico, aunque el nombre real de la subred de puerta de enlace que se crea es realmente **GatewaySubnet**. Puede ver este nombre mediante PowerShell o en el Portal de Azure.
7. Haga clic en la marca de verificación de la parte inferior derecha de la página y se empezará a crear la red virtual. Cuando termine, verá el mensaje **Creado** indicado en el **Estado** de la página **Redes** del portal clásico.

## <a name="a-namegwacreate-the-gateway"></a><a name="gw"></a>Creación de la puerta de enlace
1. En la página **Redes**, haga clic en la red virtual recién creada y luego en **Panel** en la parte superior de la página.
2. En la parte inferior de la página del **Panel**, haga clic en **Crear puerta de enlace** y seleccione **Enrutamiento dinámico**. Haga clic en **Sí** para confirmar que desea crear una puerta de enlace.
3. Cuando comience a crearse la puerta de enlace, verá un mensaje que le informa de que la puerta de enlace se inició. La puerta de enlace puede tardar hasta 45 minutos en crearse.
4. Vincule la red a un circuito. Siga las instrucciones del artículo [Vinculación de redes virtuales a circuitos ExpressRoute](expressroute-howto-linkvnet-classic.md).

## <a name="a-nameconfigaconfigure-an-existing-classic-vnet-for-expressroute"></a><a name="config"></a>Configuración de una red virtual clásica existente para ExpressRoute
Si ya dispone de una red virtual clásica, puede configurarla para conectarse a ExpressRoute en el portal clásico. La configuración es la misma que la descrita en las secciones anteriores, así que lea esas secciones para familiarizarse con la configuración necesaria. Si desea crear una conexión coexistente entre ExpressRoute y la conexión de sitio a sitio, consulte [este artículo](expressroute-howto-coexist-classic.md) para conocer los pasos. Son diferentes de los pasos de este artículo.

1. Debe crear la red local antes de actualizar el resto de la configuración de la red virtual. Para crear una nueva red local, necesaria al configurar ExpressRoute a través del portal clásico, haga clic en **Nuevo** **>** **Servicios de red** **>** **Red virtual** **>** **Agregar red local**. Siga los pasos del asistente para crear la red local.
2. Utilice la página **Configurar** para actualizar el resto de la configuración de la red virtual y asociar esta a la red local.
3. Después de completar la configuración, vaya a la sección [Creación de la puerta de enlace](#gw) de este artículo para crear la puerta de enlace.

## <a name="next-steps"></a>Pasos siguientes
* Si quiere agregar máquinas virtuales a la red virtual, consulte las [rutas de aprendizaje de máquinas virtuales](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/).
* Si quiere obtener más información sobre ExpressRoute, consulte [Información técnica de ExpressRoute](expressroute-introduction.md).




<!--HONumber=Nov16_HO3-->


