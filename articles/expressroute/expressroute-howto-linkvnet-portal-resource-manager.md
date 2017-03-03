---
title: "Vinculación de una red virtual a un circuito ExpressRoute: Azure Portal | Microsoft Docs"
description: "En este documento se proporciona información general de cómo vincular redes virtuales a circuitos ExpressRoute."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f5cb5441-2fba-46d9-99a5-d1d586e7bda4
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/08/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b3c0cca9a6d5171b1248b0f463cbbb26641fc5f2
ms.openlocfilehash: a1a689dbfc35107b52f9b84f74ac8bfac0727a0e
ms.lasthandoff: 02/10/2017


---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Conexión de una red virtual a un circuito ExpressRoute
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Clásico: PowerShell](expressroute-howto-linkvnet-classic.md)
> * [Vídeo: Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> 
>  

Este artículo le ayudará a vincular redes virtuales a circuitos de Azure ExpressRoute a través del modelo de implementación de Resource Manager y Azure Portal. Las redes virtuales pueden estar en la misma suscripción o formar parte de otra suscripción.

**Información sobre los modelos de implementación de Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Requisitos previos de configuración
* Asegúrese de haber revisado los [requisitos previos](expressroute-prerequisites.md), los [requisitos de enrutamiento](expressroute-routing.md) y los [flujos de trabajo](expressroute-workflows.md) antes de comenzar la configuración.
* Tiene que tener un circuito ExpressRoute activo.
  
  * Siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-arm.md) y habilite el circuito mediante el proveedor de conectividad.
  * Asegúrese de que dispone de un emparejamiento privado de Azure configurado para el circuito. Consulte el artículo de [configuración del enrutamiento](expressroute-howto-routing-portal-resource-manager.md) para obtener instrucciones sobre el enrutamiento.
  * Asegúrese de que el emparejamiento privado de Azure está configurado y el emparejamiento BGP entre la red y Microsoft está activo para habilitar la conectividad de extremo a extremo.
  * Asegúrese de que ha creado y aprovisionado totalmente una red virtual y una puerta de enlace de red virtual. Siga las instrucciones para crear una [puerta de enlace de VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) (siga solo los pasos 1 a 5).

* Es posible vincular hasta 10 redes virtuales a un circuito ExpressRoute estándar. Todas las redes virtuales deben pertenecer a la misma región geopolítica al utilizar un circuito de ExpressRoute estándar. 

* Puede vincular una red virtual fuera de la región geopolítica del circuito de ExpressRoute, o bien conectar un mayor número de redes virtuales a este si habilitó el complemento premium de ExpressRoute. Consulte las [preguntas más frecuentes](expressroute-faqs.md) para obtener más detalles sobre el complemento premium.

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Conexión de una red virtual en la misma suscripción a un circuito

### <a name="to-create-a-connection"></a>Para crear una conexión

> [!NOTE]
> La información de la configuración de BGP no aparecerá si el proveedor de nivel 3 configuró los emparejamientos. Si el circuito tiene un estado aprovisionado, ya puede crear conexiones.
>

1. Asegúrese de que el circuito ExpressRoute y el emparejamiento privado de Azure estén correctamente configurados. Siga las instrucciones que aparecen en los artículos para [configurar un circuito ExpressRoute](expressroute-howto-circuit-arm.md) y [configurar el enrutamiento](expressroute-howto-routing-arm.md). El circuito ExpressRoute debe tener un aspecto similar a la imagen que aparece a continuación:

    ![Captura de pantalla de un circuito ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
   
2. Ahora puede iniciar el aprovisionamiento de una conexión para vincular la puerta de enlace de red virtual con el circuito ExpressRoute. Haga clic en **Conexión** > **Agregar** para abrir la hoja **Agregar conexión** y, luego, configure los valores.

    ![Adición de captura de pantalla de conexión](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  

3. Una vez que la conexión esté correctamente configurada, el objeto de conexión mostrará la información de la conexión.

     ![Captura de pantalla de objeto de conexión](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)

### <a name="to-delete-a-connection"></a>Para eliminar una conexión
Puede eliminar una conexión si selecciona el icono **Eliminar** en la hoja correspondiente a su conexión.

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Conexión de una red virtual en una suscripción diferente a un circuito
Puede compartir un circuito ExpressRoute entre varias suscripciones. En la ilustración siguiente se muestra un sencillo esquema de cómo funciona el uso compartido de circuitos ExpressRoute entre varias suscripciones.

![Conectividad entre suscripciones](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Cada una de las nubes más pequeñas dentro de la nube de gran tamaño se usa para representar las suscripciones que pertenecen a diferentes departamentos dentro de una organización.
- Cada departamento de la organización puede usar su propia suscripción para implementar sus servicios, pero puede compartir un único circuito ExpressRoute para volver a conectarse a la red local.
- Un solo departamento (en este ejemplo: TI) puede ser el propietario del circuito ExpressRoute. Otras suscripciones dentro de la organización pueden usar el circuito ExpressRoute.

    > [!NOTE]
    > Los cargos de conectividad y ancho de banda de un circuito dedicado recaerán en el propietario del circuito ExpressRoute. Todas las redes virtuales comparten el mismo ancho de banda.
    > 
    >

### <a name="administration"></a>Administración
El "propietario del circuito" es un usuario avanzado autorizado del recurso de circuito ExpressRoute. Puede crear autorizaciones que los "propietarios del circuito", a su vez, pueden canjear. Los usuarios del circuito son propietarios de puertas de enlace de red virtual (que no se incluyen en la misma suscripción que el circuito ExpressRoute). Los usuarios del circuito pueden canjear autorizaciones (una autorización por red virtual).

El propietario del circuito tiene la capacidad de modificar y revocar las autorizaciones en cualquier momento. La revocación de una autorización dará como resultado la eliminación de todas las conexiones de vínculos de la suscripción cuyo acceso se haya revocado.

### <a name="circuit-owner-operations"></a>Operaciones del propietario del circuito

#### <a name="creating-an-authorization"></a>Creación de una autorización

El propietario del circuito crea una autorización. Esto da lugar a la creación de una clave de autorización que puede usar un usuario del circuito para conectar sus puertas de enlace de red virtual al circuito ExpressRoute. Una autorización solo es válida para una conexión.

1. En la hoja de ExpressRoute, haga clic en **Autorizaciones** y luego escriba un **Nombre** para la autorización y haga clic en **Guardar**.

    ![Autorizaciones](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)

2. Una vez guardada la configuración, copie los valores de **Identificador de recurso** y **Clave de autorización**.

    ![Clave de autorización](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

#### <a name="deleting-authorizations"></a>Eliminación de autorizaciones

Puede eliminar una conexión si selecciona el icono **Eliminar** en la hoja correspondiente a su conexión.

### <a name="circuit-user-operations"></a>Operaciones del usuario del circuito

   > [!NOTE]
   > El usuario del circuito necesita el identificador del recurso y una clave de autorización del propietario del circuito. 

#### <a name="redeeming-connection-authorizations"></a>Canjear autorizaciones de conexión


Rellene los detalles y haga clic en **Aceptar** en la pestaña Básica.

1.    Haga clic en el botón **+Nuevo**.

    ![Haga clic en Nuevo](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)

2.    Busque el nombre **"Conexión"** en Marketplace, selecciónelo y haga clic en **Crear**.

    ![Búsqueda de conexión](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)

3.    Asegúrese de que **Tipo de conexión** está establecido en "ExpressRoute".


4.    Rellene los detalles y haga clic en **Aceptar** en la hoja Básico.

    ![Hoja Básico](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)

5.    En la hoja **Configuración**, seleccione la **Puerta de enlace de red virtual** y active la casilla **Redeem authorization** (Canjear autorización).

6.    Escriba un valor en **Clave de autorización** y en **Peer circuit URI** (Emparejar URI de circuito) y asigne un nombre a la conexión. Haga clic en **Aceptar**.

    ![Hoja Configuración](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)

7. Revise la información de la hoja **Resumen** y haga clic en **Aceptar**.

#### <a name="releasing-connection-authorizations"></a>Liberación de autorizaciones de conexión

Puede liberar una autorización eliminando la conexión que vincula el circuito ExpressRoute a la red virtual.

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).

