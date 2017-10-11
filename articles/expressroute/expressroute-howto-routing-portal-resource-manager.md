---
title: "Configuración del enrutamiento (emparejamiento) de un circuito ExpressRoute: Resource Manager (Azure) | Microsoft Docs"
description: "Este artículo le guiará por los pasos necesarios para crear y aprovisionar las configuraciones entre pares privados, públicos y de Microsoft de un circuito ExpressRoute. Este artículo también muestra cómo comprobar el estado, actualizar, o eliminar configuraciones entre pares en el circuito."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8c2a7ed2-ae5c-4e49-81f6-77cf9f2b2ac9
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: cherylmc
ms.openlocfilehash: 55ccadfea55b8098ee58dcaef942f6ba54093665
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Creación y modificación del emparejamiento de un circuito ExpressRoute

Este artículo le ayuda a crear y administrar la configuración de enrutamiento de un circuito ExpressRoute en el modelo de implementación de Resource Manager mediante Azure Portal. También puede ver cómo comprobar el estado de los emparejamientos de un circuito ExpressRoute, así como el modo de actualizarlos o eliminarlos y desaprovisionarlos. Si quiere usar un método diferente para trabajar con el circuito, seleccione un artículo de la lista siguiente:


## <a name="configuration-prerequisites"></a>Requisitos previos de configuración

* Antes de comenzar la configuración, asegúrese de que ha revisado la página de [requisitos previos](expressroute-prerequisites.md), la página de [requisitos de enrutamiento](expressroute-routing.md) y la página de [flujos de trabajo](expressroute-workflows.md).
* Tiene que tener un circuito ExpressRoute activo. Antes de continuar, siga las instrucciones para [crear un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) y para que el proveedor de conectividad habilite el circuito. El circuito ExpressRoute debe estar en un estado habilitado y aprovisionado para poder ejecutar los cmdlets de las secciones siguientes.
* Si tiene pensado usar una clave compartida o un hash MD5, asegúrese de utilizarlos en ambos lados del túnel y limite el número de caracteres a 25 como máximo.

Estas instrucciones se aplican solo a los circuitos creados con proveedores de servicios que ofrecen servicios de conectividad de capa 2. Si usa un proveedor de servicios que ofrece servicios administrados de nivel 3 (normalmente VPN IP, como MPLS), el mismo proveedor de conectividad configura y administra el enrutamiento. 

> [!IMPORTANT]
> Actualmente no anunciamos los emparejamientos configurados por proveedores de servicios a través del Portal de administración de servicios. Se está trabajando para habilitar esta funcionalidad pronto. Antes de configurar los emparejamientos BGP, realice las comprobaciones pertinentes con su proveedor de servicios.
> 
> 

Puede configurar una, dos o las tres configuraciones entre pares (Azure privado, Azure público y Microsoft) para un circuito ExpressRoute. Puede establecer las configuraciones entre pares en cualquier orden. Pero tiene que asegurarse de que completa cada configuración entre pares de una en una.

## <a name="azure-private-peering"></a>Configuración entre pares privados de Azure

Esta sección le ayuda a crear, obtener, actualizar y eliminar la configuración de emparejamiento privado de Azure para un circuito ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Creación de un emparejamiento privado de Azure

1. Configure el circuito de ExpressRoute. Antes de continuar, asegúrese de que el proveedor de conectividad ha aprovisionado el circuito por completo.

  ![list](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Establecimiento de la configuración entre pares privados de Azure para el circuito. Asegúrese de que tiene los elementos siguientes antes de continuar con los siguientes pasos:

  * Una subred /30 para el vínculo principal. La subred no debe ser parte de ningún espacio de direcciones reservado para redes virtuales.
  * Una subred /30 para el vínculo secundario. La subred no debe ser parte de ningún espacio de direcciones reservado para redes virtuales.
  * Un identificador VLAN válido para establecer esta configuración entre pares. Asegúrese de que ninguna otra configuración entre pares en el circuito usa el mismo identificador de VLAN.
  * Número de sistema autónomo (AS) para la configuración entre pares. Puede usar 2 bytes o 4 bytes como números AS. Puede usar un número AS privado para esta configuración entre pares. Asegúrese de que no usa 65515.
  * **Opcional:** un hash MD5 si elige usar uno.
3. Seleccione la fila de emparejamiento privado de Azure, como se muestra en el ejemplo siguiente:

  ![privado](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. Configure el emparejamiento privado. La siguiente imagen muestra un ejemplo de configuración:

  ![configurar el emparejamiento privado](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. Guarde la configuración una vez que haya especificado todos los parámetros. Una vez que la configuración se haya aceptado correctamente, verá algo similar al siguiente ejemplo:

  ![guardar emparejamiento privado](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="to-view-azure-private-peering-details"></a>Visualización de los detalles del emparejamiento privado

Para ver las propiedades del emparejamiento privado de Azure seleccione el emparejamiento.

![ver emparejamiento privado](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="to-update-azure-private-peering-configuration"></a>Actualización del establecimiento de configuración del emparejamiento privado de Azure

Puede seleccionar la fila del emparejamiento y modificar las propiedades del mismo.

![actualizar emparejamiento privado](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="to-delete-azure-private-peering"></a>Eliminación del emparejamiento privado de Azure

Para quitar la configuración de emparejamiento, seleccione el icono de eliminación, como se muestra a continuación:

![eliminar emparejamiento privado](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="azure-public-peering"></a>Configuración entre pares públicos de Azure

Esta sección le ayuda a crear, obtener, actualizar y eliminar la configuración de emparejamiento público de Azure para un circuito ExpressRoute.

### <a name="to-create-azure-public-peering"></a>Creación de un emparejamiento público de Azure

1. Configure un circuito de ExpressRoute. Antes de continuar, asegúrese de que el proveedor de que el proveedor de conectividad ha aprovisionado el circuito por completo.

  ![mostrar emparejamiento público](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Establezca la configuración del emparejamiento publico de Azure para el circuito. Asegúrese de que tiene los elementos siguientes antes de continuar con los siguientes pasos:

  * Una subred /30 para el vínculo principal. Tiene que ser un prefijo IPv4 público válido.
  * Una subred /30 para el vínculo secundario. Tiene que ser un prefijo IPv4 público válido.
  * Un identificador VLAN válido para establecer esta configuración entre pares. Asegúrese de que ninguna otra configuración entre pares en el circuito usa el mismo identificador de VLAN.
  * Número de sistema autónomo (AS) para la configuración entre pares. Puede usar 2 bytes o 4 bytes como números AS.
  * **Opcional:** un hash MD5 si elige usar uno.
3. Seleccione la fila de emparejamiento público de Azure, como se muestra en la siguiente imagen:

  ![seleccionar fila de emparejamiento público](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. Configure el emparejamiento público. La siguiente imagen muestra un ejemplo de configuración:

  ![Configuración del emparejamiento público](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. Guarde la configuración una vez que haya especificado todos los parámetros. Una vez que la configuración se haya aceptado correctamente, verá algo similar al siguiente ejemplo:

  ![Guardado de configuración de emparejamiento público](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="to-view-azure-public-peering-details"></a>Visualización de detalles de un emparejamiento público de Azure

Para ver las propiedades de un emparejamiento público de Azure, seleccione el emparejamiento.

![ver propiedades de emparejamiento público](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="to-update-azure-public-peering-configuration"></a>Actualización del establecimiento de configuración del emparejamiento público de Azure

Puede seleccionar la fila del emparejamiento y modificar las propiedades del mismo.

![seleccionar fila de emparejamiento público](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="to-delete-azure-public-peering"></a>Eliminación del emparejamiento público de Azure

Para quitar la configuración de emparejamiento, seleccione el icono de eliminación, como se muestra en el ejemplo siguiente:

![eliminar emparejamiento público](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="microsoft-peering"></a>Emparejamiento de Microsoft

Esta sección le ayuda a crear, obtener, actualizar y eliminar la configuración de emparejamiento de Microsoft para un circuito ExpressRoute.

> [!IMPORTANT]
> Se anunciarán todos los prefijos de servicio para el emparejamiento de Microsoft de los circuitos ExpressRoute que se configuraron antes del 1 de agosto de 2017, incluso si no se definen filtros de ruta. No se anunciará ningún prefijo para el emparejamiento de Microsoft de los circuitos ExpressRoute que se configuraron el 1 de agosto de 2017 o con posterioridad, hasta que se asocie un filtro de ruta al circuito. Para más información, consulte [Configuración de un filtro de ruta para el emparejamiento de Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Creación del emparejamiento de Microsoft

1. Configure un circuito de ExpressRoute. Antes de continuar, asegúrese de que el proveedor de que el proveedor de conectividad ha aprovisionado el circuito por completo.

  ![mostrar emparejamiento de Microsoft](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Establezca la configuración del emparejamiento de Microsoft para el circuito. Asegúrese de que tiene la siguiente información antes de empezar:

  * Una subred /30 para el vínculo principal. Debe ser un prefijo de IPv4 público válido que sea de su propiedad y esté registrado en un Registro regional de Internet (RIR) o un Registro de enrutamiento de Internet (IRR).
  * Una subred /30 para el vínculo secundario. Debe ser un prefijo de IPv4 público válido que sea de su propiedad y esté registrado en un Registro regional de Internet (RIR) o un Registro de enrutamiento de Internet (IRR).
  * Un identificador VLAN válido para establecer esta configuración entre pares. Asegúrese de que ninguna otra configuración entre pares en el circuito usa el mismo identificador de VLAN.
  * Número de sistema autónomo (AS) para la configuración entre pares. Puede usar 2 bytes o 4 bytes como números AS.
  * Prefijos anunciados: tiene que proporcionar una lista de todos los prefijos que planea anunciar en la sesión BGP. Se aceptan solo prefijos de direcciones IP públicas. Si tiene pensado enviar un conjunto de prefijos, puede enviar una lista separada por comas. Estos prefijos tienen que estar registrados a su nombre en un Registro regional de Internet (RIR) o un Registro de enrutamiento de Internet (IRR).
  * **Opcional:** Cliente ASN: si los prefijos anunciados no están registrados en el número AS de emparejamiento, puede especificar el número de AS en el que están registrados.
  * Nombre del enrutamiento del Registro: puede especificar el RIR o TIR en el que están registrados el número AS y los prefijos.
  * **Opcional:** un hash MD5 si elige usar uno.
3. Puede seleccionar el emparejamiento que desea configurar, como se muestra en el ejemplo siguiente. Seleccione la fila del emparejamiento de Microsoft.

  ![seleccionar fila de emparejamiento de Microsoft](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Configure el emparejamiento de Microsoft La siguiente imagen muestra un ejemplo de configuración:

  ![configurar emparejamiento de Microsoft](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. Guarde la configuración una vez que haya especificado todos los parámetros.

  Si el circuito llega a un estado de validación necesaria (como se muestra en la imagen), debe abrir una incidencia de soporte técnico para mostrar la prueba de propiedad de los prefijos a nuestro equipo de soporte técnico.

  ![Guardado de la configuración de emparejamiento de Microsoft](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

  Puede abrir un vale de soporte técnico directamente desde el portal, tal como se muestra en el ejemplo siguiente:

  ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. Después de que la configuración se haya aceptado correctamente, verá algo similar a la imagen siguiente:

  ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="to-view-microsoft-peering-details"></a>Visualización de detalles del emparejamiento de Microsoft

Para ver las propiedades de un emparejamiento público de Azure, seleccione el emparejamiento.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="to-update-microsoft-peering-configuration"></a>Actualización de la configuración de emparejamiento de Microsoft

Puede seleccionar la fila del emparejamiento y modificar las propiedades del mismo.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="to-delete-microsoft-peering"></a>Eliminación del emparejamiento de Microsoft

Para quitar la configuración de emparejamiento, seleccione el icono de eliminación, como se muestra a continuación:

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="next-steps"></a>Pasos siguientes

Siguiente paso, [Conexión de una red virtual a un circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Para más información sobre los flujos de trabajo de ExpressRoute, vea [Flujos de trabajo de ExpressRoute](expressroute-workflows.md).
* Para más información sobre el emparejamiento de circuitos, vea [Circuitos y dominios de enrutamiento de ExpressRoute](expressroute-circuit-peerings.md).
* Para más información sobre redes virtuales, vea [Información general sobre redes virtuales](../virtual-network/virtual-networks-overview.md).
