---
title: "Creación y modificación de un circuito ExpressRoute mediante Azure Portal | Microsoft Docs"
description: "Este artículo describe cómo crear, aprovisionar, comprobar, actualizar, eliminar y desaprovisionar un circuito ExpressRoute."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68d59d59-ed4d-482f-9cbc-534ebb090613
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/07/2017
ms.author: cherylmc;ganesr
ms.openlocfilehash: e3721cd3c031622788f553e71a6555b844f3f7dc
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="create-and-modify-an-expressroute-circuit"></a>Creación y modificación de un circuito ExpressRoute
> [!div class="op_single_selector"]
> * [Portal de Azure](expressroute-howto-circuit-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-circuit-arm.md)
> * [CLI de Azure](howto-circuit-cli.md)
> * [Vídeo: Azure Portal](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit)
> * [PowerShell (clásico)](expressroute-howto-circuit-classic.md)
>

En este artículo se describe cómo crear un circuito Azure ExpressRoute con el Portal de Azure y el modelo de implementación de Azure Resource Manager. Los siguientes pasos también le mostrarán cómo comprobar el estado del circuito, actualizarlo, o eliminarlo y desaprovisionarlo.


## <a name="before-you-begin"></a>Antes de empezar
* Revise los [Requisitos previos y lista de comprobación de ExpressRoute](expressroute-prerequisites.md) y los [Flujos de trabajo de ExpressRoute para aprovisionamiento de circuitos y estados de circuitos de ExpressRoute](expressroute-workflows.md) antes de comenzar la configuración.
* Compruebe que tiene acceso al [Portal de Azure](https://portal.azure.com).
* Asegúrese de que tiene permisos para crear recursos de red. Si no tiene los permisos adecuados, póngase en contacto con el administrador de cuenta.
* Puede [ver un vídeo](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-an-expressroute-circuit) antes de comenzar para entender mejor los pasos.

## <a name="create-and-provision-an-expressroute-circuit"></a>Creación y aprovisionamiento de un circuito ExpressRoute
### <a name="1-sign-in-to-the-azure-portal"></a>1. Inicie sesión en el Portal de Azure.
Desde un explorador, navegue al [Portal de Azure](http://portal.azure.com) e inicie sesión con su cuenta de Azure.

### <a name="2-create-a-new-expressroute-circuit"></a>2. Creación de un circuito ExpressRoute.
> [!IMPORTANT]
> El circuito ExpressRoute se facturará a partir del momento en que se emita una clave de servicio. Asegúrese de realizar esta operación cuando el proveedor de conectividad esté listo para aprovisionar el circuito.
> 
> 

1. Puede crear un circuito ExpressRoute seleccionando la opción de creación de un recurso. Haga clic en **Nuevo** > **Redes** > **ExpressRoute**, tal y como se muestra en la imagen siguiente:
   
    ![Creación de un circuito ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit1.png)
2. Tras hacer clic en **ExpressRoute**, verá la hoja **Create ExpressRoute circuit** (Crear circuito ExpressRoute). Al rellenar los valores en esta hoja, asegúrese de especificar el nivel correcto de SKU y medición de datos.
   
   * **Nivel** determina si está habilitado un complemento estándar o premium de ExpressRoute. Puede especificar **Estándar** para obtener la SKU estándar o **Premium** si quiere el complemento Premium.
   * **Medición de datos** determina el tipo de facturación. Puede especificar **Metered** (Limitado) para un plan de datos limitado y **Unlimited** (Ilimitado) para un plan de datos ilimitado. Tenga en cuenta que puede cambiar el tipo de facturación de **Metered** (Limitado) a **Unlimited** (Ilimitado), pero no de **Unlimited** (Ilimitado) a **Metered** (Limitado).
     
     ![Configuración del nivel SKU y medición de datos](./media/expressroute-howto-circuit-portal-resource-manager/createcircuit2.png)

> [!IMPORTANT]
> Tenga en cuenta que la ubicación de emparejamiento indica la [ubicación física](expressroute-locations.md) de emparejamiento con Microsoft. **No** está vinculada a la propiedad Location, que hace referencia a la ubicación geográfica donde se encuentra el proveedor de recursos de red de Azure. Aunque no están relacionadas, se recomienda elegir un proveedor de recursos de red geográficamente cerca de la ubicación de emparejamiento del circuito. 
> 
> 

### <a name="3-view-the-circuits-and-properties"></a>3. Visualización de circuitos y propiedades
**Visualización de todos los circuitos**

Puede ver todos los circuitos creados seleccionando **Todos los recursos** en el menú de la izquierda.

![Visualización de circuitos](./media/expressroute-howto-circuit-portal-resource-manager/listresource.png)

**Visualización de las propiedades**

    You can view the properties of the circuit by selecting it. On this blade, note the service key for the circuit. You must copy the circuit key for your circuit and pass it down to the service provider to complete the provisioning process. The circuit key is specific to your circuit.

![Ver propiedades](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)

### <a name="4-send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>4. Envío de la clave de servicio al proveedor de conectividad para el aprovisionamiento
En esta hoja, **Estado de proveedor** da información sobre el estado actual del aprovisionamiento en el lado del proveedor de servicios. **Estado de circuito** proporciona el estado relativo al lado de Microsoft. Para más información sobre los estados de aprovisionamiento del circuito, consulte el artículo [Flujos de trabajo de ExpressRoute para aprovisionamiento de circuitos y estados de circuitos de ExpressRoute](expressroute-workflows.md#expressroute-circuit-provisioning-states) .

Cuando se crea un nuevo circuito ExpressRoute, dicho circuito estará en el siguiente estado:

Estado de proveedor: No aprovisionado<BR>
Estado de circuito: Habilitado

![Inicio del proceso de aprovisionamiento](./media/expressroute-howto-circuit-portal-resource-manager/viewstatus.png)

El circuito cambiará al estado siguiente cuando el proveedor de conectividad se encuentre en el proceso de habilitarlo:

Estado de proveedor: Aprovisionamiento<BR>
Estado de circuito: Habilitado

Para poder usar un circuito ExpressRoute, dicho circuito tiene que estar en el siguiente estado.

Estado de proveedor: Aprovisionado<BR>
Estado de circuito: Habilitado

### <a name="5-periodically-check-the-status-and-the-state-of-the-circuit-key"></a>5. Comprobación periódica del estado y la condición de la clave del circuito
Puede ver las propiedades del circuito que le interese seleccionándolo. Compruebe el **Estado de proveedor** y asegúrese de que se ha pasado a **Aprovisionado** antes de continuar.

![Estado del circuito y proveedor](./media/expressroute-howto-circuit-portal-resource-manager/viewstatusprovisioned.png)

### <a name="6-create-your-routing-configuration"></a>6. Creación de la configuración de enrutamiento
Consulte el artículo [Creación y modificación del enrutamiento de un circuito ExpressRoute mediante PowerShell](expressroute-howto-routing-portal-resource-manager.md) para ver las instrucciones paso a paso.

> [!IMPORTANT]
> Estas instrucciones se aplican solo a los circuitos creados con proveedores de servicios que ofrecen servicios de conectividad de nivel 2. Si usa un proveedor de servicios que ofrece servicios administrados de nivel 3 (normalmente VPN IP, como MPLS), el mismo proveedor de conectividad configurará y administrará el enrutamiento.
> 
> 

### <a name="7-link-a-virtual-network-to-an-expressroute-circuit"></a>7. Vinculación de una red virtual a un circuito ExpressRoute
A continuación, vincule una red virtual a su circuito ExpressRoute. Consulte el artículo [Vinculación de redes virtuales a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md) al trabajar con el modelo de implementación de Resource Manager.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>Obtención del estado de un circuito ExpressRoute
Puede ver el estado de un circuito seleccionándolo. 

![Estado de un circuito ExpressRoute](./media/expressroute-howto-circuit-portal-resource-manager/listproperties1.png)

## <a name="modifying-an-expressroute-circuit"></a>Modificación de un circuito ExpressRoute
Puede modificar determinadas propiedades de un circuito ExpressRoute sin afectar a la conectividad.

Puede hacer lo siguiente sin experimentar tiempo de inactividad:

* Habilitar o deshabilitar el complemento ExpressRoute Premium en su circuito ExpressRoute.
* Aumente el ancho de banda del circuito ExpressRoute, siempre que haya capacidad disponible en el puerto. Tenga en cuenta que no se admite la degradación del ancho de banda de un circuito. 
* Cambio del plan de medición de datos limitados a datos ilimitados. Tenga en cuenta que no es posible cambiar el plan de medición de datos ilimitados a datos limitados.
* Puede habilitar y deshabilitar *Allow Classic Operations*(Permitir operaciones clásicas).

Consulte la página [P+F de ExpressRoute](expressroute-faqs.md)para más información sobre los límites y las limitaciones.

Para modificar un circuito de ExpressRoute, haga clic en el **Configuración** tal como se muestra en la ilustración siguiente.

![Modificación del circuito](./media/expressroute-howto-circuit-portal-resource-manager/modifycircuit.png)

Puede modificar el ancho de banda,la SKU, el modelo de facturación y permitir operaciones clásicas en la hoja de configuración.

> [!IMPORTANT]
> Si el puerto existente no tiene la capacidad adecuada, tendrá que volver a crear el circuito ExpressRoute. El circuito no se puede actualizar si no hay más capacidad disponible en la ubicación.
>
> No podrá reducir el ancho de banda de un circuito ExpressRoute sin interrupciones. Para degradar un ancho de banda, es necesario desaprovisionar el circuito ExpressRoute y luego volver a aprovisionar un nuevo circuito ExpressRoute.
> 
> La operación de deshabilitación del complemento premium puede producir un error si usa recursos que son más grandes de lo que está permitido para el circuito estándar.
> 
> 

## <a name="deprovisioning-and-deleting-an-expressroute-circuit"></a>Desaprovisionamiento y eliminación de un circuito ExpressRoute
Puede eliminar el circuito ExpressRoute seleccionando el icono **Eliminar** . Tenga en cuenta lo siguiente:

* Tiene que desvincular todas las redes virtuales del circuito ExpressRoute. Si se produce un error en esta operación, compruebe si hay alguna red virtual vinculada al circuito.
* Si el estado de aprovisionamiento del proveedor de servicios del circuito ExpressRoute es **Aprovisionando** o **Aprovisionado**, debe colaborar con su proveedor de servicios para que desaprovisionen el circuito. Se le continuará reservando recursos y facturándole por ello hasta que el proveedor de servicios complete el desaprovisionamiento del circuito y nos lo notifique.
* Si el proveedor de servicios ha desaprovisionado el circuito (el estado de aprovisionamiento del proveedor de servicios está establecido en **No aprovisionado**), puede eliminar el circuito. Esto detendrá la facturación del circuito.

## <a name="next-steps"></a>Pasos siguientes
Después de crear el circuito, asegúrese de hacer lo siguiente:

* [Crear y modificar el enrutamiento para el circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Vincular la red virtual a su circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)

